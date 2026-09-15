## Introduction

*This section is informative.*

A [[ref: decentralized trust graph]] (DTG) is a graph of trust relationships between people, organizations, devices, and AI agents in which every node and every edge can be cryptographically verified. This specification defines the **DTG Core Credentials**: seven [[ref: VC]] types that are used to create, annotate, and govern access within that graph. These credentials are W3C-compliant verifiable credentials and may be presented using standard VC presentation methods when privacy preservation is not desired. They should, however, be presented using **privacy-preserving zero-knowledge proofs (ZKPs)** — of personhood, community membership, and facts about relationships — whenever privacy preservation is desired, since ZKPs are the only inherently privacy-preserving option for proof of personhood with DTG credentials (cf. [Personhood Credentials, Adler et al. 2024](https://arxiv.org/abs/2408.07892)). Used this way, ZKPs allow holders to prove what they need to prove from the perspective of the entities involved while maintaining minimal correlation across contexts.

Five of the seven credential types fall into two functional categories, and two sit outside them:

1. **Edge credentials** — establish relationships between existing entities (nodes) in the DTG:
    - [[ref: VRC]] (verifiable relationship credential) — attests to a relationship between two entities; the relationship is verified through a bi-directional pair of VRCs
    - [[ref: VMC]] (verifiable membership credential) — attests to the membership of an entity in a community; membership is verified through a bi-directional pair of VMCs
    - [[ref: VDC]] (verifiable delegation credential) — attests that one entity has appointed another to act in its name, for a bounded set of acts; the delegation is verified through a grant and a matching acceptance

2. **Annotation credentials** — attach data to existing edges without creating new graph structure:
    - [[ref: VPC]] (verifiable persona credential) — links a [[ref: persona]] to a relationship
    - [[ref: VSC]] (verifiable statement credential) — a signed statement about a node under a governed predicate; the [[ref: VEC]] (endorsement) and [[ref: VWC]] (witness attestation of an edge) are its first two predicate profiles

Standing outside those categories, because neither forms a graph edge nor annotates existing structure:

- [[ref: VIC]] (verifiable invitation credential) — authorizes onboarding of a prospective member, bootstrapping a node into a community rather than annotating one already in the graph
- [[ref: VAC]] (verifiable authority credential) — states what its subject may *do* at a named scope, and may be attenuated by its holder to equip an agent or device with strictly less authority than they hold themselves

> **Important:** These two categories are **descriptive only** and aid understanding. They do not appear in credential schemas. The formal type hierarchy has only one abstract parent: `DTGCredential`.

This Working Draft supersedes the [v0.3 proposal draft](https://github.com/trustoverip/dtgwg-cred-tf/blob/main/dtg.md) published in the [DTG Credentials Task Force repository](https://github.com/trustoverip/dtgwg-cred-tf).

> **Note on identifiers:** The DTG model is designed to be compatible with [verifiable identifiers](https://glossary.trustoverip.org/#term:verifiable-identifier) (VIDs) in general, consistent with the ToIP Trust Spanning Protocol. This version of the specification uses [decentralized identifiers](https://www.w3.org/TR/did-1.0/) (DIDs) exclusively. Future versions may generalize to other VID types (such as X.509 certificates or KERI AIDs) as ecosystem demand emerges.

### Related Specifications

This specification is designed to work alongside the following planned companion specifications of the [[ref: DTGWG]]. None has been published yet; this section will be updated with references as they become available.

- **DTG Core Trust Task Protocols** *(planned)* — will define the [trust task](https://glossary.trustoverip.org/#term:trust-tasks) protocols necessary to offer, issue, request, present, and revoke the DTG Core Credentials defined in this specification. It will also define the structure of the Trust Task completion artifact (outcome evidence) referenced by the `taskContext` binding mechanism in this specification.

- **DTG Verifiable Data Structures** *(planned)* — will define [[ref: verifiable data structure]] (VDS) types that are exchanged over DTG relationships but are not DTG credentials, including:
    - [[ref: r-card]] (relationship card) — a combination of human-readable and machine-readable data describing its publisher, exchanged in conjunction with [[ref: VRC]]s; a modern, self-updating analog of a [vCard](https://en.wikipedia.org/wiki/VCard).
    - **Agent card** — a VDS describing the identity and capabilities of an AI agent (its provider, capabilities, and skills), similar in spirit to an r-card but modeled on the [Agent2Agent (A2A) protocol's AgentCard](https://agent2agent.info/docs/concepts/agentcard/) discovery document.

- **DTG Predicate Vocabulary** *(planned)* — a repo-driven registry rather than a specification: one definition file per [[ref: VSC]] predicate, generated into a human-readable vocabulary, the vocabulary served at the DTG namespace, and a machine-readable accept-list for verifiers, governed by pull request under stated admission criteria. It will hold the predicate profiles beyond the core profiles in this specification, and the core profiles move into it once it has a release. This specification defines the statement mechanism and how a verifier handles predicates; the registry defines what may be said.

### Specification Versioning

This specification distinguishes two version numbers that read as if they mean the same thing but do not:

- **`_Version:_`** states the version of this specification that the working group is converging toward — the number a wider ratifying body confirms when this specification reaches Working Group Approved Deliverable or ToIP Approved Deliverable status. It changes only when the specification is re-targeted, not with each Working Draft revision.
- **`_Document Status:_`** carries the semantic version of *this Working Draft* — `Working Draft MAJOR.MINOR.PATCH` — and is what editors and implementers use to coordinate day-to-day while the specification converges. It is the version referenced everywhere below and in [Compatibility Rules](#compatibility-rules).

Document Status follows [Semantic Versioning 2.0.0](https://semver.org/):

- **MAJOR** — a change that breaks conformance for existing implementations, such as removing or tightening a REQUIRED property, changing the semantics of an existing credential type, or removing a credential type.
- **MINOR** — a backward-compatible addition, such as a new OPTIONAL property, a new credential type, or a new informative section.
- **PATCH** — an editorial or clarifying change with no effect on conformance.

#### Compatibility Rules

A change to this Working Draft MUST be classified as either backward-compatible or breaking:

- A **backward-compatible** change — for example, adding an OPTIONAL property, relaxing a constraint, or adding a permitted enumeration value to a non-discriminating field — MUST result in a MINOR increment to `Document Status`.
- A **breaking** change — for example, adding or removing a REQUIRED property, removing a permitted enumeration value, narrowing a constraint, or changing the semantics of an existing credential type — MUST result in a MAJOR increment to `Document Status`, with MINOR reset to `0`.

A holder or verifier conformant to Document Status `M.N` MUST accept a credential issued under any earlier Document Status `M.K` where `K ≤ N`, and SHOULD accept a credential carrying an OPTIONAL property it does not recognize — introduced by a later MINOR version than the one it implements — rather than rejecting the credential solely for that property's presence. This specification does not currently embed its own version number in a credential's `@context` or `type`; a MAJOR revision is expected to be accompanied by a new `@context` version so that a credential issued under incompatible semantics is also structurally distinguishable, but until that mechanism is defined, a verifier implementing one MAJOR version MUST NOT assume it can safely interpret a credential issued under a different one.

> **Note on cross-specification versioning:** The DTGWG's specifications — this specification and its planned companions listed above — each follow their own semantic versioning process, and each uses the same `_Version:_` / `_Document Status:_` split. The editors have deliberately chosen **not** to synchronize Document Status numbers across specifications; a MAJOR release of one does not imply, require, or correspond to any particular release of another.
>
> Where this specification's conformance requirements depend on a mechanism defined by a companion specification — for example, the `taskContext` binding described in [Trust Task Context Binding](#trust-task-context-binding), which depends on the trust task exchange and outcome-evidence mechanisms of the planned **DTG Core Trust Task Protocols** specification — this specification instead states the **minimum Document Status of that companion specification** with which this Working Draft is compatible. The companion specification is expected to do the same in reverse, stating the minimum Document Status of this specification with which it is compatible. Implementers integrating both specifications MUST check both stated minimums rather than assuming that matching or adjacent version numbers imply compatibility.

> **Note on version history:** This specification's earlier Working Drafts were informally numbered "01" and "02", before this semantic versioning scheme was adopted; those ordinals do not correspond to `0.1.0`/`0.2.0`. This Working Draft's Document Status is `0.4.0` — continuing from the [pre-migration `v0.3` proposal draft](https://github.com/trustoverip/dtgwg-cred-tf/blob/main/dtg.md) it supersedes (see Introduction), rather than restarting at `0.1.0`, which would collide with that predecessor's own version number. `_Version:_` is set to `1.0`, the release this Working Draft is converging toward; it is not yet ratified.

## Requirements Language

The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in [IETF RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119).
