# Delegation as a case study in the design-time window

*Design note responding to [dtgwg-cred-tf#39](https://github.com/trustoverip/dtgwg-cred-tf/discussions/39). Posted before rewriting [dtgwg-cred-spec#19](https://github.com/trustoverip/dtgwg-cred-spec/pull/19), because if the postulate is right, this is the moment to have the argument rather than after.*

Our seat is the issuer/verifier side rather than the wallet side: we build and run VTC community services that issue and revoke DTG credentials, status-list infrastructure, and a delegated-execution path where a personal agent acts for its principal. This complements @albertoleon7794's wallet-side experience rather than repeating it.

We are bringing a seventh credential type through the credentials spec — the **VDC (verifiable delegation credential)**, which attests that one entity has appointed another to act **in its name**, for a bounded scope, revocably. Reading #39 against that draft convinced us the draft is **too complicated, and complicated in exactly the places that cost privacy**. What follows is a proposal to cut it down, the reasoning, and a set of worked examples — including the edge cases that stopped us cutting further.

---

## 1. The proposal, first

**A delegation is a short-lived, single-hop appointment issued by the principal and countersigned by the delegate.** That is the whole normative core:

| Field | Meaning |
|---|---|
| `issuer` | the principal — the entity in whose name acts will be performed |
| `credentialSubject.id` | the delegate |
| `delegation.scope` | the acts, drawn from the governing VTC/VTN vocabulary |
| `delegation.u` | a salt, so every commitment over this block is blinded |
| `validUntil` | short — minutes to hours wherever the principal is reachable |
| `delegation.accepts` | in the acceptance: the SAID of the grant. **The acceptance is required.** |

Six fields. Verification is five local checks and **no network calls**: the proof verifies, `validUntil` has not passed, the requested act is within `scope`, the presenting party demonstrates control of `credentialSubject.id`, and the acceptance countersignature is present and matches.

Gone from our current draft: `parent`, `maxDepth`, `credentialStatus`, chain resolution, status-list infrastructure, and with them the ancestry disclosure and the revocation phone-home.

**Sub-delegation is not a right; it is a request.** A delegate that needs a sub-delegate asks the principal, who issues a fresh single-hop VDC directly to the sub-delegate. Depth is always one. The principal always holds the complete register of who may act in their name.

**Revocation is non-renewal.** Withdrawing an appointment means declining the next renewal, which takes effect within one `validUntil`. This is the shape everything else converged on: OAuth access tokens with a refresh endpoint, and short-lived certificates with no status responder.

### The one profile

There is exactly one case the core cannot serve: **the principal is genuinely unreachable for the life of the appointment.** For that, an opt-in profile re-adds what the core dropped:

| Field | Cost it re-introduces |
|---|---|
| `parent` (SAID) | ancestry disclosure at verification |
| `maxDepth` | chain-depth reasoning |
| `credentialStatus` | a live lookup, i.e. a correlation surface and an availability dependency |

Two properties matter more than the fields. The profile is **opt-in and visible to the verifier**, so policy can decline it; and its costs are **stated in the spec** rather than discovered by an implementer later. A governing VTC/VTN decides which scopes may use it.

We think this split is the simplification the thread's postulate calls for: everyone implements a core with no correlation surfaces at all, and the correlating machinery exists only where it is unavoidable, marked as such.

---

## 2. Why: a delegation is a cache of approval

A principal could approve every act individually. A delegation is a pre-approval of a *class* of acts, traded away for latency and availability. Under that frame the design questions become cache questions:

| Cache question | Answer in the core |
|---|---|
| Who may populate it? | only the origin — the principal |
| What is the TTL? | short, `validUntil` |
| How is it invalidated? | it expires; you decline to refill it |
| May an entry populate further entries? | **no** |

The last row is where our current draft — following ZCAP-LD and UCAN — makes the choice you would never make in a cache design. A chained delegation is **a cache that populates itself**, so the origin no longer knows what is cached. The principal cannot enumerate who is acting in their name, and therefore cannot revoke them: they do not know those credentials exist.

That is an accountability problem before it is a privacy problem, but it produces a privacy problem too.

### The chain-resolution boundary

Every other credential in WD01 is verified by evaluating **one** credential. A chained VDC is verified by resolving an ancestry: every ancestor must independently verify, scopes must nest, `validUntil` must be monotone, and the chain must terminate in a root whose `issuer` is the principal.

So the completion-evidence boundary of trade-off 2 has an exact analogue: **the chain-resolution boundary.** Excellent selective disclosure over a leaf VDC is close to worthless, because establishing representation at all requires disclosing the whole ancestry, including the identity of the principal. Different mechanism, same shape — which we take as support for @sankarshanmukhopadhyay's point that unlinkability must be evaluated across the composed presentation rather than credential-by-credential.

It also sequences the way the postulate predicts. This is a consequence of member layout and of *who issues what*, not of proof suite. It cannot be fixed later.

Where chaining survives — in the profile — its validity decomposes cleanly into set and range predicates: act ∈ scope_n ⊆ scope_n-1 ⊆ … ⊆ scope_root, depth bounded, `validUntil` monotone, revocation non-membership. @ScottJeezey, if the ZKP TF wants a second tractable target alongside Q2, **delegation-chain validity** may be better posed than Q1, and *"the root of this chain is a member of registry R"* is Q6 applied recursively.

---

## 3. What we would take from KERI and ACDC

**Delegation is cooperative, not unilateral.** Establishing a delegated AID in KERI requires commitments from both parties: the delegate creates the event, the delegator anchors an event seal in its own KEL, and the delegated event carries a location seal pointing back. The security property is the point — an exploiter compromising only the delegate's keys does not thereby capture control authority ([KID0007](https://identity.foundation/keri/kids/kid0007.html)). That is why we would make the acceptance **constitutive** rather than optional evidence: the grant/acceptance pair then *is* the two-seal handshake.

Worth noting what KERI keeps separate: identifier-level delegation is cooperative, while ACDC's I2I edges chain *independent issuances by accountable parties, each with its own registry* — not a token passed along and shrunk in private. The vLEI chain (GLEIF → QVI → LE → OOR/ECR) works that way. That undercuts the usual argument for unilateral chaining: the flagship deployment's intermediate issuers are all online and accountable.

**Self-addressing identifiers collapse the locator/binder split.** A SAID is a digest of the block it sits in, computed with its own field dummied out, so the identifier *is* the integrity check. Q3 asks for a blinded form preserving the locator/binder split; ACDC's answer is that with a SAID you do not need the split.

**A first-class salty nonce.** ACDC permits a UUID field (`u`) in any block; where present, the block's SAID becomes a *blinded* commitment. The draft is explicit about why: without that entropy an adversary can reconstruct block contents from the SAID plus the schema, by dictionary attack over the values the schema permits ([draft-ssmith-acdc-01](https://datatracker.ietf.org/doc/html/draft-ssmith-acdc-01)). This is why `u` is in our six-field core and not an optional extra — see edge case E3 below.

**Graduated disclosure needs no RDF canonicalization.** SAID-committed sections give partial disclosure using plain hashing, so the undisclosable-member hazard of trade-off 4 does not arise for them. We are not proposing the group abandon `bbs-2023`; we are observing that Q4's failure mode follows from the suite choice rather than from selective disclosure as such.

---

## 4. Worked examples

Each of these forced one decision above. The first four are the mainline; the rest are the edge cases that stopped us simplifying further.

| # | Scenario | What it forces |
|---|---|---|
| 1 | Personal agent books travel for its principal | the six-field core is sufficient |
| 2 | Research agent needs three sub-agents | sub-delegation is a request, not a chain |
| 3 | Community appoints its VTA to act for it | scope vocabulary belongs to governance |
| 4 | Bookkeeper acts for a business for a quarter | renewal loop replaces revocation |
| E1 | Advance appointment for incapacity | the offline profile must exist |
| E2 | Delegate's key is stolen | acceptance must be constitutive |
| E3 | Scope commitment is brute-forced | the salt is not optional |
| E4 | One agent, two principals | delegate DIDs must be per-principal |
| E5 | Revoke-then-act race | short lifetimes beat status lookups |

**1 · Personal agent books travel.** A principal's agent holds `["travel:search", "travel:book"]` with a two-hour `validUntil`, issued by the principal's own always-online agent service. The airline verifies locally: signature, expiry, `travel:book ∈ scope`, key control, countersignature. It contacts nobody. Two hours later the appointment is gone unless renewed. *No status list was needed, and the airline learned nothing it could correlate against a second booking beyond what the principal chose to present.*

**2 · Research agent needs three sub-agents.** Under a chain model the agent mints three attenuated sub-delegations itself, and the principal never learns those agents exist. Two things break. The principal cannot revoke what they cannot enumerate — and since a personhood credential asserts one membership per person, three agents each presenting a chained delegation can read to a verifier as three people. Under the core, the agent asks the principal's service for three single-hop VDCs; that is a round trip measured in milliseconds, and the register stays complete. **This is the case people reach for chaining to solve, and it is the case where chaining is worst.**

**3 · Community appoints its VTA.** A VTC's C-DID appoints its agent service to issue membership credentials in the community's name, scope `["members:admit", "members:remove"]`. Both parties are services, both always online, so renewal is free. The scope strings are defined by the community's governance framework, not by DTG — which is why the Q4 "term definitions MUST" has to reach **governance-defined vocabularies**, and why a per-VTC `@context` publication mechanism is a real gap that no spec currently owns.

**4 · Bookkeeper acts for a business for a quarter.** The natural draft is a ninety-day VDC with a status list. The core version is a ninety-day *policy* at the principal's agent, expressed as daily renewals. Withdrawing the appointment means the next renewal does not happen; worst case exposure is one day rather than however long the verifier's cached status was. The business gets a complete list of what it renewed, and no third party learns when anyone checked.

### Edge cases

**E1 · Advance appointment for incapacity.** A person appoints someone to act for them *if they become unable to act for themselves*. The principal is unreachable by construction, so renewal is impossible and non-renewal cannot express withdrawal. **Design consequence:** the offline profile is mandatory, not a legacy concession — long `validUntil` plus a real `credentialStatus`, with the correlation cost accepted and documented. This is the example that stopped us deleting `credentialStatus` outright, and it is worth noting that the correlation exposure lands on the least-protected party in any of these scenarios.

**E2 · The delegate's key is stolen.** Under a chain model an attacker holding the delegate's key mints sub-delegations silently and attenuation buys nothing, because the attacker chooses the attenuation. Under a constitutive acceptance the attacker can present the existing appointment but cannot manufacture new ones — creating one requires the principal to issue and the new delegate to countersign. **Design consequence:** acceptance is required, which is KERI's cooperative property recovered inside a credential model.

**E3 · The scope commitment is brute-forced.** A verifier is shown a commitment to `scope` rather than the values. The governing VTC publishes forty scope strings. An observer enumerates every subset of a plausible size and matches the digest in milliseconds — the commitment reveals the scope to anyone who reads the governance framework. **Design consequence:** `u` is in the core. This is exactly the attack ACDC's salty nonce exists to defeat, and it generalises: *every* digest-valued binder in DTG is currently an unsalted JCS-SHA-256 over content that is often low-entropy. We hit the same problem independently in a consent design and fixed it by salting with a per-request challenge; it is cheap, needs no ZK, and it is a **member-layout decision**, so it sits squarely inside the window the postulate describes.

**E4 · One agent, two principals.** An agent holds appointments from Alice and from Bob and presents both to the same verifier — perhaps in a single transaction. If `credentialSubject.id` is the same DID in both, the verifier now links Alice and Bob, neither of whom consented to that and neither of whose credential was individually revealing. **Design consequence:** the delegate identifier must be **per-principal**, and probably per-context, and the spec should say so rather than leaving it to implementers. Note that no amount of credential-side selective disclosure fixes this — it is a composition property, which is the boundary condition @sankarshanmukhopadhyay named.

**E5 · Revoke-then-act race.** The principal revokes at T. The delegate acts at T+1s. The verifier holds status cached from T−60s and accepts. Hard-failing on an unreachable status endpoint converts the issuer's outage into the verifier's outage, so in practice everyone soft-fails and the revocation is advisory. **Design consequence:** a short `validUntil` gives a bounded, *stated* exposure window, where a status lookup gives an unbounded and unstated one. This is the time-of-check-to-time-of-use problem, and we have hit it in our own consent path: policy evaluated when a request is minted is not policy at the moment of execution, and only re-evaluation at execution closes it.

---

## 5. Live lookups: status lists and trust registries are the same anti-pattern

Both are **pull**, and both leak the same two ways.

**Privacy.** A status-list host learns which verifier checked which credential, and when. Herd privacy over the bitstring does not touch that. We implement the mitigations in production — lists at the 131,072-bit floor, randomised slot allocation so the index does not correlate with issuance order, decoy bits on unassigned slots, and a never-reallocate-a-flipped-slot invariant so a departed member's index cannot be reused as a correlator — and the *fetch itself* still leaks. A trust registry leaks worse: it learns who is verifying whom.

This is not a speculative worry. Let's Encrypt retired its OCSP service on 6 August 2025 and gave privacy as the primary reason: a CA answering OCSP queries learns which site a visitor is visiting, from that visitor's IP address ([announcement](https://letsencrypt.org/2024/12/05/ending-ocsp), [end of life](https://letsencrypt.org/2025/08/06/ocsp-service-has-reached-end-of-life)). A status-list host answering revocation queries about a delegation learns strictly more than that, because the credential identifies the parties rather than merely the site.

**Availability.** Soft-fail is security theatre; hard-fail is an outage. Web PKI ran this experiment and came out on **pushed state and short lifetimes**: browsers moved to CRLSets and CRLite, and Let's Encrypt now offers six-day certificates and has announced a move from ninety days to forty-five. The direction of travel is away from asking a server whether a credential is still good, and toward credentials short-lived enough that the question rarely arises.

So: **the verifier performs no live lookups; the presentation carries what it needs.** In rough order of readiness — (1) freshness by re-issuance, which is what the core above already does; (2) a signed snapshot plus an inclusion or exclusion proof, where the verifier pins a root and the holder supplies the path; (3) an accumulator non-membership witness, which is the best privacy, is ZK-friendly, and is the one for the ZKP TF.

The unification we would most like reaction to: **anchoring, revocation status, and trust-registry membership are the same primitive** — a signed published set root plus a membership or non-membership proof carried in the presentation. One construction covers Q6, revocation correlation, the registry phone-home, and what remains of the chain-resolution boundary.

---

## 6. Where our own draft is wrong

Offered as implementer experience rather than self-flagellation.

- **Unsalted binders.** `parent` and `accepts` are unsalted JCS-SHA-256 digests over low-entropy content. See E3.
- **Scope equality across the edge blocks selective disclosure.** We require the acceptance's `scope` to be *identical* to the grant's, so that what the delegate consented to is legible without resolving the grant. Equality is not verifiable under partial disclosure, so that clause forecloses SD of scope across the edge. It is trade-off 6 with the sibling in a *different credential*. Referencing the grant by SAID fixes it.
- **`credentialStatus` REQUIRED mandates a correlation surface.** The requirement should be *the verifier MUST be able to establish that the appointment is currently in force*, with a status list as one satisfying mechanism among several — not as a mandated live lookup.
- **Re-delegation was specified by depth, not by authorisation.** `maxDepth` says how deep and never says who authorises the step. That is the wrong axis.

---

## 7. Positions on the open questions

- **Q1 (completion in ZK).** Agree with @ScottJeezey that it is not answerable inside either spec alone. Delegation supplies a second instance of the same dependency.
- **Q2 (blinded binder).** A salted commitment is a partial answer available now, without ZK. We would like the spec to treat every digest-valued binder as requiring blinding by default.
- **Q3 (verifier conduct).** Anything a verifier fetches at request time is downgradeable. Our rule elsewhere is that fetched metadata is for rendering and compiled-in code is for policy, with unknown versions failing closed. A profile should pin which proof satisfies a predicate rather than permitting a fallback to the cheap path.
- **Q4 (term definitions MUST).** Support, with a second reason: scope vocabularies are governance-defined, so the MUST must reach them. See example 3.
- **Q5 (set-valued members).** We would restate the guidance. `scope` is set-valued but subset disclosure is *safe*, because the verifier's check is existential — under-disclosure understates the appointment and cannot mislead. The sharper test is whether the predicate the verifier evaluates is **monotone under withholding**: existential checks are safe, completeness and equality checks are not. Our own acceptance clause fails that test, which is how we found it.
- **Q6 (issuer as predicate).** Strongly in favour, and it generalises — same primitive as anchoring and revocation.
- **Q8 (framework privacy with teeth).** Yes, and delegation makes it concrete: the grant is a credential, but the *invocation* is a Trust Task artifact, so the durable record of what a delegate actually did in whose name lives entirely on the framework side.
- **On @talltree's boundary proposal**, as refined by @sankarshanmukhopadhyay: agreed, with one addition. A fourth layer selects the privacy profile — the governing VTC/VTN determines status parameters, scope vocabulary, and whether the offline profile may be used for a given scope. DTG has no governance authority, so the specs should define what must be **parameterisable** by governance rather than fixing values.

---

## 8. What we can contribute

- Our status-list implementation as prior art on revocation correlation: herd-privacy floor, randomised allocation, decoys, never-reallocate invariant.
- Proposed spec text for salted or SAID-based binders, applicable to the VWC `digest` and the task citation as well as to the VDC.
- A reference verifier for the six-field core — the interesting property being that it makes no network calls, so it is a small and testable conformance target.
- Willingness to prototype the signed-set-root plus membership-proof primitive, which is load-bearing for Q6 and for revocation privacy at once.

## Two asks

1. **Is "the verifier performs no live lookups during verification" something this group wants as a design constraint?** It is the single decision with the most downstream consequences, it is design-time in the postulate's sense, and nearly everything above follows from it.
2. **Should delegation be inside the design-time sweep now?** If the eight questions are answered only for attestation credentials, the VDC gets cut wrong — and chain resolution is a disclosure boundary of the same kind as completion evidence.
