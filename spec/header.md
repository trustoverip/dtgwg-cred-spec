# Decentralized Trust Graph Credentials - Core Specification

_Version:_ 1.0  
_Document Status:_ Working Draft 01  
<!-- _DOI:_ To be assigned when this specification reaches ToIP Approved Deliverable status. See https://lf-toip.atlassian.net/wiki/spaces/HOME/pages/767787009/ToIP+Approved+Deliverable+Process#Persistent-DOI-Link -->

_Editors:_

- Alberto Leon, Applied Technology Lab at Harvard University
- Brendan A. Miller, Applied Technology Lab at Harvard University
- Geoff Turk, First Person Project
- Martina Kolpondinos, First Person Project, Kosma Connect
- Drummond Reed, First Person Project

_Contributors:_

- Sankarshan Mukhopadhyay
- Glenn Gore
- The participants of the Decentralized Trust Graph Working Group (DTGWG)

**Abstract**

A Decentralized Trust Graph (DTG) is a graph of cryptographically verifiable [trust relationships](https://glossary.trustoverip.org/#term:trust-relationships) between entities (people, communities, devices, and software agents (including AI agents)). This specification defines six **DTG Core Credential** types, expressed as W3C verifiable credentials. The six credential types fall into three functional categories: **Edge Credentials** comprise the **Verifiable Membership Credential (VMC)** and the **Verifiable Relationship Credential (VRC)**, which establish graph edges representing community membership and peer-to-peer relationships; the **Verifiable Invitation Credential (VIC)** supports member onboarding; and the **Annotation Credentials** comprise the **Verifiable Persona Credential (VPC)**, **Verifiable Endorsement Credential (VEC)** and **Verifiable Witness Credential (VWC)**, which associate [[ref: personas]] for intentional correlation, verifiable assertions about counterparties, including endorsements, and witness attestations of relationship formation, with existing relationships. The DTG credentials can be presented in privacy-preserving ways using zero-knowledge proof mechanisms. Such presentations can support holders to limit information they disclose and reduce correlation across contexts. The specification also defines a mechanism for associating credentials with the [trust task](https://glossary.trustoverip.org/#term:trust-task) context in which they were issued, allowing its meaning to be interpreted in the context of that exchange.

**Intellectual Property Rights**

This specification is provided under the [Joint Development Foundation (JDF) charter](https://cdn.platform.linuxfoundation.org/agreements/ToIP.pdf) for [Trust Over IP](https://trustoverip.org) (ToIP) and is subject to the intellectual property rights policy of the **Decentralized Trust Graph Working Group (DTGWG)**:

_Copyright:_ [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/)  
_Patent:_ W3C Mode (based on the [W3C Patent Policy](https://www.w3.org/Consortium/Patent-Policy-20040205/))  
_Source Code:_ [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0)

THESE MATERIALS ARE PROVIDED “AS IS.” The parties expressly disclaim any warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to the materials. The entire risk as to implementing or otherwise using the materials is assumed by the implementer and user. IN NO EVENT WILL THE PARTIES BE LIABLE TO ANY OTHER PARTY FOR LOST PROFITS OR ANY FORM OF INDIRECT, SPECIAL, INCIDENTAL, OR CONSEQUENTIAL DAMAGES OF ANY CHARACTER FROM ANY CAUSES OF ACTION OF ANY KIND WITH RESPECT TO THIS DELIVERABLE OR ITS GOVERNING AGREEMENT, WHETHER BASED ON BREACH OF CONTRACT, TORT (INCLUDING NEGLIGENCE), OR OTHERWISE, AND WHETHER OR NOT THE OTHER MEMBER HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
