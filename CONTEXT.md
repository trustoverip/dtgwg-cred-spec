# DTG Core Credentials

The specification defining the W3C Verifiable Credential types that create and annotate the Decentralized Trust Graph (DTG). Published with Spec-Up-T under the ToIP DTG Working Group.

## Language

### Graph structure

**DTG (Decentralized Trust Graph)**:
The graph whose nodes are entities (persons, devices, AI agents, VTCs) and whose edges are trust relationships (membership or peer-to-peer), established entirely through verifiable credentials.

**Edge Credential**:
A credential that establishes a relationship between existing entities (nodes) in the DTG — membership (VMC) or peer-to-peer (VRC). Descriptive category only; never appears in schemas.

**Annotation Credential**:
A credential that attaches data to existing edges or parties without creating graph structure (VPC, VEC, VWC). Descriptive category only.

**VRC (verifiable relationship credential)**:
Attests to a relationship between two entities; two VRCs (one per direction) form a complete DTG edge.

**VMC (verifiable membership credential)**:
Attests to the membership of an entity in a VTC or VTN; two VMCs (one per direction) form a complete DTG edge. One W3C type (`MembershipCredential`); the two directions are expressed via issuer/subject rules, not separate type strings. The community-issued VMC (the grant) is issued from the VTC/VTN's own DID to the member; the member-issued VMC (the acknowledgement) reverses those roles and carries a `digest` of the grant. The acknowledgement is the member's consent artifact — a community asserting someone's membership must be able to produce it.

**VDC (verifiable delegation credential)**:
Attests that one entity (delegator) has appointed another (delegate) to act **in the delegator's name**, for a bounded set of acts, for a limited period, revocably. Acts within `scope` are attributed to the delegator. An edge credential: a grant plus a matching acceptance forms the edge. `validUntil` and `credentialStatus` are REQUIRED on it. The grant is a credential; the invocation is a Trust Task artifact.
_Avoid_: authority, permission, capability, token, ZCAP (for what a VDC confers — see below)

**Delegation vs authority**:
Deliberately distinct, and the reason the VDC is its own type. **Authority** = may this party do this thing, *as itself*; the act is attributed to the party. **Delegation** = may this party act *in another's name*; the act is attributed to that other. Neither implies the other (access to a mailbox ≠ appointment to send mail as its owner), and a VDC never supplies missing authority. This spec defines delegation only; "authority" is reserved vocabulary for a possible future verifiable authority credential, so do not use it to describe what a VDC does.
_Avoid_: using "authority" for anything a VDC confers

**Delegation composes with authority; it does not carry it**:
A VDC moves the question, it does not answer it. The verifier substitutes the delegator for the delegate and asks whether *the delegator* may do the act. Three independent checks: (1) may this party act in that name — the VDC; (2) may the delegator do this — out of scope here; (3) must the delegate independently qualify — governance. Reach = intersection of (1) and (2), never the union. Nothing the delegator holds is copied to the delegate, so a delegator cannot re-issue what it was itself issued; withdrawing the delegator's own permission stops the delegate immediately without revoking the VDC.
_Avoid_: "delegating a credential", transfer, copy, hand over (for what a VDC does)

**VIC (verifiable invitation credential / DTG invitation credential)**:
Authorizes onboarding of a prospective member into a VTC or VTN. One W3C type (`InvitationCredential`); the glossary's VTC/VTN invitation subtypes are prose distinctions expressed via issuer/subject rules, not separate type strings.

**VPC (verifiable persona credential)**:
Links a persona to an existing relationship, enabling intentional correlation under holder control. The persona is asserted under an identifier its holder ordinarily declares `directed`.

**VEC (verifiable endorsement credential)**:
A standard container attaching community-governed reputation/skill assertions to a party.

**VWC (verifiable witness credential)**:
Third-party attestation that an edge was established under specific conditions. Remains in DTG Core Credentials (per 2026-06/07 discussions); the only type for which `taskContext` is REQUIRED. Its issuer is the witness's own DID (a member's, or a VTA's per VTC policy), which is `directed` at minimum.
_Avoid_: W-DID (not a DTG identifier type)

### Identifiers

**DTG verifiable identifier (VID)**:
A verifiable identifier for a DTG node; this spec version uses DIDs. A VID is not typed by the role its holder plays — roles are conferred by credentials. What a VID carries is a declared **correlation scope**, one of three monotonic values: `pairwise` (one counterparty), `directed` (a set the holder chooses) or `public` (unbounded).
_Avoid_: R-DID, M-DID, C-DID, P-DID (retired identifier types), W-DID

### Proofs

**Pairwise ZKP**:
A ZKP construction available to any two VRC holders, regardless of shared community membership. Selectively discloses attributes (e.g., `directed` persona identifiers) while hiding the `pairwise` ones; confers no community-level assurance by itself.

**Community-anchored ZKP**:
A ZKP construction available when both VRC parties hold VMCs from the same community. Three-part proof (VRC + VMC + the same community identifier); carries forward whatever assurances the community attaches to its VMCs (e.g., personhood).

**ZKPs by default**:
DTG credentials MAY be presented via standard W3C VC methods, but implementations SHOULD default to ZKP presentation so privacy requires no user opt-in. Community membership is never a precondition for issuing, holding, or presenting a VRC.

### Trust-task boundary

**Credential**:
A durable claim about the graph that is true standing alone, outside any exchange. Test: "true outside the exchange? → credential."
_Avoid_: artifact (for durable claims)

**Artifact**:
A work-product of a Trust Task (intermediate or completion), meaningful only within its exchange; carried as a Trust Task document correlated by `threadId`, never as a new credential type.
_Avoid_: credential (for in-exchange work products), VXC

**taskContext**:
A credential field holding the originating Trust Task `threadId`, binding the credential to its task context. OPTIONAL on all DTG credentials, REQUIRED on VWC. Defined in this spec; the completion-artifact envelope is defined in the Trust Task protocol spec.

**Outcome interpretability**:
The verifier rule that a `taskContext`-bearing credential MUST NOT be read as proof of task completion unless the matching outcome artifact is reachable.

**Trust Task**:
The governed unit of work (defined outside this spec; ToIP-level concept, transport-agnostic, not DTG-specific).

**Ceremony**:
The socio-technical pattern through which a Trust Task is carried out. Adopted as a named layer above Trust Tasks.

### Data structures

**VDS (Verifiable Data Structure)**:
A structured data object implemented as a W3C VC but not a DTGCredential subtype — used for data exchange, not graph claims. RCard is the first; specified in a future Verifiable Data Structures spec, not in DTG Core Credentials.

**RCard (Relationship Card)**:
A VDS carrying human-readable identity/contact data (JCard-formatted), analogous to a business card.

**Agent Card**:
A planned VDS describing an agent's identity and capabilities, analogous to the A2A protocol's AgentCard discovery document; to be defined alongside RCard in the Verifiable Data Structures spec.
