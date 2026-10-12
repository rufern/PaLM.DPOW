<div markdown="1" class="stu-note">

This Test Plan page is a prototype. We expect the maturity of the content will improve over time. For now, we summarize the high-level testing scope and proposed Connectathon scenarios. Comments are welcome.
</div>

## Introduction

The goal of DPOW testing is to demonstrate that conforming implementations of the PAWM, DAWM, and IMA actors can exchange the HL7 v2.5.1 messages defined in Volume 2 and take the Expected Actions, both against test tools (unit testing) and against each other (integration testing at Connectathon).

Because DPOW is an HL7 v2 messaging profile, conformance is validated against the message abstract syntax and segment specifications in the [Volume 2 Appendices](volume-2-appendix.html), rather than against FHIR StructureDefinition profiles. This section will be filled in as the IHE Connectathon need drives the creation of detailed test procedures, test tools, and reporting.

### Unit Test Procedure

Unit testing in this context is where a System Under Test (SUT) is tested against a simulator or validator. A simulator is an implementation of an actor designed specifically to exercise its paired actor (for example, a PAWM simulator that emits each [LAB-100] flavour so a DAWM can be tested). A validator checks message conformance against the DPOW segment and message specifications. Message-level validation SHALL confirm `MSH-12 = 2.5.1`, the correct `MSH-21` profile identifier, and the required segments and fields for each transaction.

### Integration Test Procedure

Integration testing in this context is where two SUTs of paired actors test against each other. The subset of tests that can be executed is the intersection of the capabilities both partners declare. Testing this intersection is necessary but not sufficient; testing must also exercise negative and edge cases (e.g., `ORL^O22` with an error acknowledgement, de-association notifications) to confirm that failure modes are handled properly by both SUTs.

## Proposed Connectathon Test Scenarios

*(Draft - for discussion at PaLM TC. Pass criteria require validation - see WI-DPOW-01.)*

| Test ID | Description | Actors | Option | Pass Criteria |
|---|---|---|---|---|
| DPOW-T01 | Specimen registration [LAB-100] `DPOW-SPECIMEN^IHE` | PAWM -> DAWM | `DPOW-SPECIMEN` | DAWM creates case entry; `ORL^O22` AA returned |
| DPOW-T02 | Block registration [LAB-100] `DPOW-BLOCK^IHE` | PAWM -> DAWM | `DPOW-BLOCK` | DAWM updates case; parent SPM-3 linkage correct |
| DPOW-T03 | Stained slide registration [LAB-100] `DPOW-SLIDE^IHE` | PAWM -> DAWM | - | Slide appears in DAWM with correct OBX stain metadata |
| DPOW-T04 | Unstained slide + stain update ([LAB-100] x 2) | PAWM -> DAWM | - | Placeholder created; updated with stain OBX when stained |
| DPOW-T05 | Case status update [LAB-101] | PAWM <-> DAWM | - | Correct ORC-5/ORC-25 pair received; `ORL^O22` AA returned |
| DPOW-T06 | Work order - IHC ([LAB-102] + [LAB-103]) | DAWM -> PAWM -> DAWM | `DPOW-WORDER` | Order placed and acknowledged (accept / ORC-1 UA if not fulfillable); on acceptance a single [LAB-103] fulfilment received (ORC-5 CM, SPM-2.2 set); new [LAB-100] slide follows |
| DPOW-T07 | Image availability notification [LAB-104] SPM-20=Y | IMA -> PAWM, DAWM | - | DAWM marks case ready; Study UID accessible |
| DPOW-T08 | Image de-association [LAB-104] SPM-20=N | IMA -> PAWM, DAWM | - | PAWM and DAWM clear the images-available indication for the slide |
| DPOW-T09 | Full workflow end-to-end | PAWM + IMA + DAWM | - | All steps of DPOW-T01 through T08 that apply to the options both partners declare complete in sequence |
{: .grid}
