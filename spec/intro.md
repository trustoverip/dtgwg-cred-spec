## Introduction

*This section is informative.*

A [[ref: decentralized trust graph]] (DTG) is a graph of trust relationships between people, organizations, devices, and AI agents in which every node and every edge can be cryptographically verified. This specification defines the **DTG Core Credentials**: six [[ref: VC]] types that are used to create and annotate that graph. These credentials are W3C-compliant verifiable credentials and may be presented using standard VC presentation methods when privacy preservation is not desired. They should, however, be presented using **privacy-preserving zero-knowledge proofs (ZKPs)** — of personhood, community membership, and facts about relationships — whenever privacy preservation is desired, since ZKPs are the only inherently privacy-preserving option for proof of personhood with DTG credentials (cf. [Personhood Credentials, Adler et al. 2024](https://arxiv.org/abs/2408.07892)). Used this way, ZKPs allow holders to prove what they need to prove from the perspective of the entities involved while maintaining minimal correlation across contexts.

The six credential types fall into three functional categories:

1. **Edge credentials** — establish relationships between existing entities (nodes) in the DTG:
    - [[ref: VRC]] (verifiable relationship credential) — attests to a relationship between two entities; the relationship is verified through a bi-directional pair of VRCs
    - [[ref: VMC]] (verifiable membership credential) — attests to the membership of an entity in a community; membership is verified through a bi-directional pair of VMCs

2. **Invitation credentials** — bootstrap new members into communities:
    - [[ref: VIC]] (verifiable invitation credential) — authorizes onboarding of a prospective member

3. **Annotation credentials** — attach data to existing edges without creating new graph structure:
    - [[ref: VPC]] (verifiable persona credential) — links a [[ref: persona]] to a relationship
    - [[ref: VEC]] (verifiable endorsement credential) — endorses skills or reputation
    - [[ref: VWC]] (verifiable witness credential) — third-party attestation of an edge

> **Important:** These three categories are **descriptive only** and aid understanding. They do not appear in credential schemas. The formal type hierarchy has only one abstract parent: `DTGCredential`.

This Working Draft supersedes the [v0.3 proposal draft](https://github.com/trustoverip/dtgwg-cred-tf/blob/main/dtg.md) published in the [DTG Credentials Task Force repository](https://github.com/trustoverip/dtgwg-cred-tf).

> **Note on identifiers:** The DTG model is designed to be compatible with [verifiable identifiers](https://glossary.trustoverip.org/#term:verifiable-identifier) (VIDs) in general, consistent with the ToIP Trust Spanning Protocol. This version of the specification uses [decentralized identifiers](https://www.w3.org/TR/did-1.0/) (DIDs) exclusively. Future versions may generalize to other VID types (such as X.509 certificates or KERI AIDs) as ecosystem demand emerges.

### Related Specifications

This specification is designed to work alongside the following planned companion specifications of the [[ref: DTGWG]]. Neither has been published yet; this section will be updated with references as they become available.

- **DTG Core Trust Task Protocols** *(planned)* — will define the [trust task](https://glossary.trustoverip.org/#term:trust-tasks) protocols necessary to offer, issue, request, present, and revoke the DTG Core Credentials defined in this specification. It will also define the structure of the Trust Task completion artifact (outcome evidence) referenced by the `taskContext` binding mechanism in this specification.

- **DTG Verifiable Data Structures** *(planned)* — will define [[ref: verifiable data structure]] (VDS) types that are exchanged over DTG relationships but are not DTG credentials, including:
    - [[ref: r-card]] (relationship card) — a combination of human-readable and machine-readable data describing its publisher, exchanged in conjunction with [[ref: VRC]]s; a modern, self-updating analog of a [vCard](https://en.wikipedia.org/wiki/VCard).
    - **Agent card** — a VDS describing the identity and capabilities of an AI agent (its provider, capabilities, and skills), similar in spirit to an r-card but modeled on the [Agent2Agent (A2A) protocol's AgentCard](https://agent2agent.info/docs/concepts/agentcard/) discovery document.

## Requirements Language

The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in [IETF RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119).
