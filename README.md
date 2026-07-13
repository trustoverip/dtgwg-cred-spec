# DTG Core Credentials V1.0

This repo is the home for the DTG Core Credentials V1.0 specification, a deliverable of the [DTG Credentials Task Force repo](https://github.com/trustoverip/dtgwg-cred-tf).

Following the LF Joint Development Foundation (JDF) process, the repo will host a series of **Working Drafts**, numbered sequentially starting from 01, until the Task Force is ready to submit the specification for a vote as a **Working Group Approved Deliverable**.

For general discussions about DTG credentials (i.e., not specific to this specification), please see [the discussions section](https://github.com/trustoverip/dtgwg-cred-tf/discussions) of the DTG Credentials Task Force repo.

## RFC Keyword Usage

This specification uses the key words defined in [IETF RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119) (MUST, MUST NOT, REQUIRED, SHALL, SHALL NOT, SHOULD, SHOULD NOT, RECOMMENDED, MAY, OPTIONAL). The rule for when to capitalize them is simple:

- **Normative sections** — use ALL CAPS. These sections create conformance requirements that implementations must satisfy.
- **Informative sections** — use lowercase. These sections explain, illustrate, or provide context; they do not impose requirements.

### How to tell which sections are normative

Every section in the spec opens with one of:

- `*This section is normative.*`
- `*This section is informative.*`
- `*This subsection is informative.*`

Sections whose names end in "Considerations" (Security Considerations, Privacy Considerations, etc.) are informative, as is the Introduction, Terminology, and the DTG Credential Taxonomy section.

### Examples

Normative (caps):

> Verifiers MUST cryptographically verify the `proof` of every DTG credential.

Informative (lowercase):

> These credentials may be presented using standard VC presentation methods when privacy preservation is not desired.

## Contributing

All commits to this repository must include a `Signed-off-by` line in the commit message, indicating that the contributor has read and agrees to the [Developer Certificate of Origin (DCO)](https://developercertificate.org/):

```text
Signed-off-by: Your Name <your-email@example.com>
```

Use `git commit --signoff` (or `-s`) to add this automatically. This repository uses the Linux Foundation's [EasyCLA](https://www.linuxfoundation.org/blog/blog/easycla-beta) system to verify contributor license agreements.
