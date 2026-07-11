[[def: VMC, verifiable membership credential, VMCs, verifiable membership credentials]]

~ A [[ref: DTG edge credential]] that defines membership in a [[ref: VTC]]. The issuer of a VMC is always identified with the VTC's [[ref: C-DID]]. If the counterparty is another VTC, it is also identified with a C-DID. Otherwise the counterparty is identified with a [[ref: M-DID]]. A VMC is only verifiable as a [[ref: DTG edge]] in the context of a specific [[ref: VTN]] if the issuing VTC is recognized as a [[ref: VTN trust anchor]] on that VTN.

~ For example, a VMC issued by a VTC whose governance specifies that the holder of the credential must be a real person who has exactly one VMC in the scope of that VTC can serve as a [[ref: PHC]].
