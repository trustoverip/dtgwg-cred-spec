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

**VMC (verifiable membership credential)**:
Attests to the membership of an entity in a VTC or VTN; two VMCs (one per direction) form a complete DTG edge. Issuer is always the VTC's C-DID.

**VRC (verifiable relationship credential)**:
Attests to a relationship between two entities; two VRCs (one per direction) form a complete DTG edge.

**VIC (verifiable invitation credential / DTG invitation credential)**:
Authorizes onboarding of a prospective member into a VTC or VTN. One W3C type (`InvitationCredential`); the glossary's VTC/VTN invitation subtypes are prose distinctions expressed via issuer/subject rules, not separate type strings.

**VPC (verifiable persona credential)**:
Links a persona DID to an existing relationship, enabling intentional correlation under holder control.

**VEC (verifiable endorsement credential)**:
A standard container attaching community-governed reputation/skill assertions to a party.

**VWC (verifiable witness credential)**:
Third-party attestation that an edge was established under specific conditions. Remains in DTG Core Credentials (per 2026-06/07 discussions); the only type for which `taskContext` is REQUIRED. Its issuer is the witness's own DID (an M-DID or a VTA's DID per VTC policy).
_Avoid_: W-DID (not an official DTG identifier type)

### Identifiers

**DTG verifiable identifier (VID)**:
A verifiable identifier for a DTG node; this spec version uses DIDs. Exactly four official DID types: R-DID (relationship), M-DID (membership), C-DID (community), P-DID (persona).
_Avoid_: W-DID

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
