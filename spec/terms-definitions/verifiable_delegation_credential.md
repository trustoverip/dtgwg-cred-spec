[[def: VDC, verifiable delegation credential, VDCs, verifiable delegation credentials]]

~ A [[ref: DTG edge credential]] that attests that one entity (the delegator) has appointed another entity (the delegate) to act in the delegator's name, for a bounded set of acts, for a limited period, revocably. Within that scope, what the delegate does is attributed to the delegator.

~ A VDC expresses delegation, not authority. Authority is a matter of what a party may do **as itself**; delegation is a matter of whether a party may act **in another's name**. Neither implies the other: a service permitted to read a person's mailbox has not thereby been appointed to send mail as that person. A VDC therefore never widens what may be done — a delegate cannot do in the delegator's name what the delegator could not do itself. This specification does not define a credential for authority.

~ A VDC neither carries authority nor confers it. Nothing the delegator holds is copied to the delegate: a verifier substitutes the delegator for the delegate and then asks whether *the delegator* may perform the act. The reach of a delegation is therefore the intersection of what the delegator may do and what the VDC appoints the delegate for.

~ A VDC attests only to the existence and bounds of the appointment; the act of exercising it is an invocation carried out within a [trust task](https://glossary.trustoverip.org/#term:trust-tasks), not a DTG credential.

~ For example, a person may issue a VDC to an AI agent appointing it to correspond in their name within a specific [[ref: VTC]], for a specific set of acts, for a specific period.
