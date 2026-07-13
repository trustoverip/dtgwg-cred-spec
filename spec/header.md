# Decentralized Trust Graph Credentials - Core Specification

_Version:_ 1.0  
_Document Status:_ Working Draft  
<!-- _DOI:_ To be assigned when this specification reaches ToIP Approved Deliverable status. See https://lf-toip.atlassian.net/wiki/spaces/HOME/pages/767787009/ToIP+Approved+Deliverable+Process#Persistent-DOI-Link -->

_Editors:_

- Alberto Leon, Applied Social Media Lab, Berkman Klein Center at Harvard University
- Brendan A. Miller, Applied Social Media Lab, Berkman Klein Center at Harvard University
- Geoff Turk, First Person Project
- Martina Kolpondinos, First Person Project
- Drummond Reed, First Person Project

_Contributors:_

- Sankarshan Mukhopadhyay
- Glenn Gore
- The participants of the Decentralized Trust Graph Working Group (DTGWG)

**Abstract**

A Decentralized Trust Graph (DTG) is a graph of cryptographically verifiable trust relationships between people, organizations, devices, and AI agents. This specification defines the DTG Core Credentials: six W3C Verifiable Credential types that create and annotate the nodes and edges of a DTG. Two edge credentials establish graph structure — membership in a verifiable trust community (VMC) and peer-to-peer relationships (VRC). One invitation credential (VIC) supports onboarding of new members. Three annotation credentials attach information to existing graph structure: personas for intentional correlation (VPC), endorsements for community-governed reputation assertions (VEC), and witness attestations of relationship formation (VWC). These credentials enable privacy-preserving zero-knowledge proofs of personhood, community membership, and relationships while minimizing correlation across contexts. The specification also defines how a credential may be bound to the context of the Trust Task exchange in which it was issued, so that verifiers can distinguish durable graph claims from task-dependent claims.

**Intellectual Property Rights**

This specification is provided under the [Joint Development Foundation (JDF) charter](https://cdn.platform.linuxfoundation.org/agreements/ToIP.pdf) for [Trust Over IP](https://trustoverip.org) (ToIP) and is subject to the intellectual property rights policy of the **Decentralized Trust Graph Working Group (DTGWG)**:

_Copyright:_ [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/)  
_Patent:_ W3C Mode (based on the [W3C Patent Policy](https://www.w3.org/Consortium/Patent-Policy-20040205/))  
_Source Code:_ [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0)

THESE MATERIALS ARE PROVIDED “AS IS.” The parties expressly disclaim any warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to the materials. The entire risk as to implementing or otherwise using the materials is assumed by the implementer and user. IN NO EVENT WILL THE PARTIES BE LIABLE TO ANY OTHER PARTY FOR LOST PROFITS OR ANY FORM OF INDIRECT, SPECIAL, INCIDENTAL, OR CONSEQUENTIAL DAMAGES OF ANY CHARACTER FROM ANY CAUSES OF ACTION OF ANY KIND WITH RESPECT TO THIS DELIVERABLE OR ITS GOVERNING AGREEMENT, WHETHER BASED ON BREACH OF CONTRACT, TORT (INCLUDING NEGLIGENCE), OR OTHERWISE, AND WHETHER OR NOT THE OTHER MEMBER HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
