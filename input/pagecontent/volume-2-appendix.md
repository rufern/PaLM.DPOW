This page collects the Volume 2 appendices for the DPOW profile: the message abstract syntax and the common HL7 v2 message segment definitions (Appendix A).

All DPOW transactions leverage a constrained profile of HL7 `OML^O21`. The `OML^O21` (Laboratory Order) message initiates each transaction; `ORL^O22` (Laboratory Order Response) is the application acknowledgement.

**Key aspects of OML^O21 / ORL^O22:**

- Trigger event: any change to a laboratory order (new order, cancellation, update)
- Designed for specimen-centric workflows where specimen/container information is embedded within the ORC/OBR segment group
- Supports complex orders with multiple specimens, containers, and test requests
- `ORL^O22` is the direct response from the filler to the placer

> **Work Item WI-DPOW-01 - Message Structure Validation.** The DPOW message abstract syntax and segment usage require formal validation against the normative HL7 v2.5.1 `OML^O21` / `ORL^O22` structure by the PaLM TC HL7 working group before Trial Implementation.

---

## Appendix A - Common HL7 Message Segments for DPOW Transactions

The following segment definitions apply to all DPOW transactions. Usage codes: **R** Required, **RE** Required but may be empty, **O** Optional, **C** Conditional, **X** Not supported.

### Message Abstract Syntax

Every DPOW transaction uses a constrained HL7 v2.5.1 `OML^O21` message; the response is `ORL^O22`. The trees below give the abstract syntax for each message profile - identified by MSH-21 - showing each segment and segment group with its cardinality `[min..max]` (`0` = optional, `*` = repeatable). Indentation shows segment-group nesting, following the standard `OML^O21` groups (PATIENT, PATIENT_VISIT, ORDER, TIMING, OBSERVATION_REQUEST, OBSERVATION, SPECIMEN). Message profiles that share the same abstract syntax are listed together. The overall structure and the placement of the pre-adopted `PRT` segment is subject to validation (see WI-DPOW-01).

**Physical Asset Registration** - `DPOW-SPECIMEN^IHE`, `DPOW-BLOCK^IHE`, `DPOW-SLIDE^IHE` (Physical Asset Registration [LAB-100])

```
MSH                       [1..1]
PATIENT                   [1..1]
  PID                     [1..1]
  PATIENT_VISIT           [0..1]
    PV1                   [1..1]
ORDER                     [1..1]
  ORC                     [1..1]
  TIMING                  [0..1]
    TQ1                   [1..1]
  OBSERVATION_REQUEST     [1..1]
    OBR                   [1..1]
    NTE                   [0..*]
    PRT                   [0..*]   pathologist / participants
    SPECIMEN              [1..1]
      SPM                 [1..1]
      OBX                 [0..*]   fixative / embedding / stain
      SAC                 [0..1]
      NTE                 [0..*]
```

**Case Update** - `DPOW-CASE-UPDATE^IHE` (Case Update [LAB-101])

```
MSH                       [1..1]
PATIENT                   [1..1]
  PID                     [1..1]
ORDER                     [1..1]
  ORC                     [1..1]
  OBSERVATION_REQUEST     [1..1]
    OBR                   [1..1]
    NTE                   [0..*]   case-level comments
```

**Place Work Order** - `DPOW-PLACE-WO^IHE` (Place Work Order [LAB-102])

```
MSH                       [1..1]
PATIENT                   [1..1]
  PID                     [1..1]
  PATIENT_VISIT           [0..1]
    PV1                   [1..1]
ORDER                     [1..1]
  ORC                     [1..1]   ORC-1 = NW
  TIMING                  [0..1]
    TQ1                   [1..1]
  OBSERVATION_REQUEST     [1..1]
    OBR                   [1..1]
    NTE                   [0..*]
    SPECIMEN              [1..1]
      SPM                 [1..1]   SPM-3 parent asset / SPM-2.1 placer temp ID
```

**Accept Work Order** - `DPOW-ACCEPT-WO^IHE` (Accept Work Order [LAB-103])

```
MSH                       [1..1]
PATIENT                   [1..1]
  PID                     [1..1]
ORDER                     [1..1]
  ORC                     [1..1]   ORC-5 = CM
  OBSERVATION_REQUEST     [1..1]
    OBR                   [1..1]
    SPECIMEN              [1..1]
      SPM                 [1..1]   SPM-2.1 placer / SPM-2.2 filler identifier
```

**Image Availability Notification** - `DPOW-IMAGE-NOTIF^IHE` (Image Availability Notification [LAB-104])

```
MSH                       [1..1]
PATIENT                   [1..1]
  PID                     [1..1]
ORDER                     [1..1]
  ORC                     [1..1]
  OBSERVATION_REQUEST     [1..1]
    OBR                   [1..1]
    SPECIMEN              [1..1]
      SPM                 [1..1]
      OBX                 [0..*]   Study / Series Instance UID (Value Type RP)
```

**Acknowledgement** - `ORL^O22` (response to all DPOW transactions)

```
MSH                       [1..1]
MSA                       [1..1]
ERR                       [0..*]
PATIENT                   [0..1]
  PID                     [1..1]
ORDER                     [0..*]
  ORC                     [1..1]   e.g., ORC-1 = OK (accepted) / UA (cannot fulfil) for [LAB-102]
```

### A.1 MSH Segment

| SEQ | DT | Usage | Card | Element Name |
|---|---|---|---|---|
| 9 | MSG | R | [1..1] | Message Type |
| 12 | VID | R | [1..1] | Version ID (2.5.1) |
| 21 | EI | R | [1..1] | Message Profile Identifier |
{: .grid}

MSH-21 SHALL reference the applicable DPOW transaction. The last column links each message profile to the actor option, defined in [Volume 1, Section 1:XX.2](volume-1.html#actor-options), that governs its support; "-" means that support does not depend on an option.

| IHE DPOW Transaction | Transaction Name | MSH-21 Value | Volume 1 Option (Profile Option Identifier) |
|---|---|---|---|
| LAB-100 (specimen) | Physical Asset Registration - Specimen | `DPOW-SPECIMEN^IHE` | Specimen Registration Option (`DPOW-SPECIMEN`) |
| LAB-100 (block) | Physical Asset Registration - Block | `DPOW-BLOCK^IHE` | Block Registration Option (`DPOW-BLOCK`) |
| LAB-100 (slide) | Physical Asset Registration - Slide | `DPOW-SLIDE^IHE` | - (required) |
| LAB-101 | Case Update | `DPOW-CASE-UPDATE^IHE` | - (required) |
| LAB-102 | Place Work Order | `DPOW-PLACE-WO^IHE` | Additional Work Order Option (`DPOW-WORDER`) |
| LAB-103 | Accept Work Order | `DPOW-ACCEPT-WO^IHE` | Additional Work Order Option (`DPOW-WORDER`) |
| LAB-104 | Image Availability Notification | `DPOW-IMAGE-NOTIF^IHE` | - (required) |
{: .grid}

Profile Option Identifiers identify actor options (Volume 1); they are not message content and are never sent in MSH-21.

### A.2 ORC Segment

| SEQ | DT | Usage | Card | Element Name | Example |
|---|---|---|---|---|---|
| 1 | ID | R | [1..1] | Order Control | NW, XO, CA |
| 2 | EI | O | [0..1] | Placer Order Number | PL12345 |
| 3 | EI | O | [0..1] | Filler Order Number | FL098756 |
| 5 | ID | O | [0..1] | Order Status | IP |
| 15 | DTM | O | [0..1] | Order Effective Date/Time | 20250526094900 |
| 25 | CWE | O | [0..1] | Order Status Modifier | Final^Final^99DPOW |
| 34 | CWE | O | [0..*] | Order Workflow Profile | (see value set below) |
| 35 | ID | O | [0..1] | Action Code | U |
| 38 | EI | RE | [0..1] | Filler Order Group Number | S25.0001 |
{: .grid}

**ORC-34 Order Workflow Profile - Proposed Value Set**

> **Pre-adoption note:** ORC-34 was introduced in HL7 v2.9. Its use in DPOW (targeting HL7 v2.5.1) is a pre-adoption. Implementations SHALL declare support for this field.

> **Pre-adoption note:** ORC-38 (Filler Order Group Number) was introduced in HL7 v2.9. Its use in DPOW (targeting HL7 v2.5.1) is a pre-adoption to carry the case accession number for DPIA alignment (see [Closed Issue 7](issues.html)); implementations SHALL declare support for this field.

| Value | Display Name | Comment |
|---|---|---|
| IMAGE | Image | LIS-driven workflow and reporting |
| PACS-WORKFLOW | PACS Workflow | PACS-driven workflow and reporting on LIS |
| PACS-REPORTING | PACS Reporting | PACS-driven workflow and reporting |
{: .grid}

### A.3 OBR Segment

| SEQ | DT | Usage | Card | Element Name | Example |
|---|---|---|---|---|---|
| 2 | EI | O | [0..1] | Placer Order Number | PL12345 |
| 3 | EI | O | [0..1] | Filler Order Number | FL098756 |
| 4 | CWE | R | [1..1] | Universal Service Identifier | 687891.024^Digital AP Study^L |
| 13 | CWE | O | [0..*] | Relevant Clinical Information | - |
| 25 | ID | O | [0..1] | Result Status | - |
| 31 | CWE | O | [0..*] | Reason for Study | - |
{: .grid}

### A.4 SPM Segment

| SEQ | DT | Usage | Card | Element Name | Example |
|---|---|---|---|---|---|
| 2 | EIP | O | [0..1] | Specimen Identifier | S25.0001.A.01.01 |
| 2.1 | EI | O | [0..1] | Placer Assigned Identifier | (DAWM temp ID for work orders) |
| 2.2 | EI | O | [0..1] | Filler Assigned Identifier | S25.0001.A.01.01 (PAWM definitive ID) |
| 3 | EIP | O | [0..*] | Specimen Parent IDs | S25.0001.A.01 |
| 4 | CWE | R | [1..1] | Specimen Type | TISS^Tissue^HL70487 |
| 6 | CWE | O | [0..*] | Specimen Additives | (see OBX-5) |
| 7 | CWE | O | [0..1] | Specimen Collection Method | INCBIO^Incisional biopsy^L |
| 8 | CWE | O | [0..1] | Specimen Source Site | 80248007^Breast structure^SCT |
| 9 | CWE | O | [0..*] | Specimen Source Site Modifier | 7771000^Left^SCT |
| 14 | ST | O | [0..*] | Specimen Description | Left breast incisional biopsy |
| 17 | DTM | O | [0..1] | Specimen Collection Date/Time | 20230822073025 |
| 18 | DTM | O | [0..1] | Specimen Received Date/Time | 20230822100052 |
| 20 | ID | O | [0..1] | Specimen Availability | Y (available) / N (de-associated) |
| 25 | CQ | O | [0..1] | Specimen Current Quantity | 1^number&UCUM |
| 27 | CWE | O | [0..1] | Container Type | (see SPM-27 value set) |
| 30 | CX | O | [0..*] | Accession ID | S25.0001 |
{: .grid}

**SPM-6 SHALL NOT be used.** Fixative, embedding medium, and staining information SHALL be conveyed in `OBX-5 (CWE)` segments (see A.10).

**SPM-4** SHALL use `TISS` or an appropriate SCT code. SPM-5 SHOULD NOT be used.

**SPM-8** SHOULD use SNOMED CT codes for body structure (e.g., `80248007^Breast structure^SCT`).

**SPM-9** SHOULD use SNOMED CT codes for laterality (e.g., `7771000^Left^SCT`).

**SPM-18** carries the **scan date/time** exclusively in [LAB-104] (Image Availability Notification). In all other DPOW transactions it retains its standard HL7 meaning of Specimen Received Date/Time.

**SPM-20** is used in [LAB-104] to indicate image availability (`Y`) or de-association (`N`).

**SPM-27 Container Type - Value Set**

| Value | Display Name | Used for |
|---|---|---|
| CONTAINER | Specimen Container | Specimen / container registration |
| CASSETTE | Block Cassette | Block registration |
| GLASS | Glass Slide | Slide registration |
{: .grid}

### A.5 SAC Segment

| SEQ | DT | Usage | Card | Element Name | Example |
|---|---|---|---|---|---|
| 2 | EI | O | [0..1] | Accession Identifier | S25.0001 |
| 3 | EI | O | [0..1] | Container Identifier | S25.0001.A |
| 4 | EI | O | [0..1] | Primary Container Identifier | S25.0001 |
| 7 | DTM | O | [0..1] | Registration Date/Time | 20230822093007 |
| 8 | CWE | O | [0..1] | Container Status | R^Process Completed^HL70370 |
| 15 | CWE | O | [0..*] | Location | HISTO-LAB-01^Histology Lab A1^L |
| 48 | CWE | O | [0..1] | Container Material | WSI |
{: .grid}

> **SAC-48 pre-adoption:** SAC-48 (Container Material) is a field introduced in HL7 v2.9.1. Its use in DPOW (targeting HL7 v2.5.1) is a pre-adoption. Implementations SHALL declare support for this field.

**SAC-48 Container Material - Value Set**

| Value | Display Name | Comment |
|---|---|---|
| WSI | Whole Slide Image | Glass slides eligible for or already scanned |
| OTH | Other | Non-scannable or non-eligible specimens |
{: .grid}

### A.6 NTE Segment (Notes and Comments)

| SEQ | DT | Usage | Card | Element Name | Example |
|---|---|---|---|---|---|
| 2 | ID | O | [0..1] | Source of Comment | L |
| 3 | FT | O | [0..*] | Comment | Malignant region painted in yellow |
| 4 | CWE | O | [0..1] | Comment Type | BLOCK^Block comment^99DPOW |
{: .grid}

**NTE-4 Comment Type - Proposed Value Set**

| Value | Display Name | Context |
|---|---|---|
| *(empty)* | Order-level remark | After ORC |
| SBOX | Specimen box comment | After SAC |
| BLOCK | Block comment | After SAC |
{: .grid}

### A.7 TQ1 Segment (Timing/Quantity)

| SEQ | DT | Usage | Card | Element Name | Example |
|---|---|---|---|---|---|
| 2 | CQ | O | [0..1] | Quantity | 1 |
| 7 | DTM | O | [0..1] | Start Date/Time | 20230822103000 |
| 8 | DTM | O | [0..1] | End Date/Time | 20230824103000 |
| 9 | CWE | O | [0..1] | Priority | R^Routine^HL70485 |
{: .grid}

### A.8 PRT Segment (Participation)

> **Pre-adoption note:** PRT is a v2.7+ segment. Implementations targeting HL7 v2.5.1 SHOULD use `OBR-32` (Principal Result Interpreter) for the reading pathologist instead.

| SEQ | DT | Usage | Card | Element Name | Example |
|---|---|---|---|---|---|
| 1 | EI | O | [0..1] | Participation Instance ID | 1151651916348 |
| 2 | ID | R | [1..1] | Action Code | AD |
| 4 | CWE | R | [1..1] | Participation | PI^Primary Interpreter^HL70443 |
| 5 | XCN | O | [0..*] | Participation Person | path01^SMITH^JACQUELINE |
| 9 | PL | O | [0..1] | Participation Organization | HospA |
{: .grid}

### A.9 PID Segment (Patient Identification)

| SEQ | DT | Usage | Card | Element Name | Example |
|---|---|---|---|---|---|
| 3 | CX | R | [1..*] | Patient Identifier List | 1234567890^^^MyHospHIS&1.2.3.4.5&ISO |
| 5 | XPN | R | [1..*] | Patient Name | SMITH^MARY |
| 7 | DTM | O | [0..1] | Date/Time of Birth | 19000101 |
| 8 | CWE | O | [0..1] | Administrative Sex | F |
| 29 | DTM | O | [0..1] | Patient Death Date and Time | |
| 30 | ID | O | [0..1] | Patient Death Indicator | N |
{: .grid}

### A.10 OBX Segment

OBX is used in two distinct contexts in DPOW.

#### A.10.1 OBX for Specimen Preparation Metadata ([LAB-100])

One or more OBX segments SHALL follow the SPM segment in [LAB-100] to convey fixative (specimen level), embedding medium (block level), and staining (slide level) information. This replaces the former use of SPM-6 and aligns with DPIA (IHE PaLM DPIA Rev 1.4, Section 3 and Appendix A.2).

SPM-6 SHALL NOT be used for this purpose in DPOW (consistent with DPIA).

| SEQ | DT | Usage | Card | Element Name | Notes |
|---|---|---|---|---|---|
| 1 | SI | R | [1..1] | Set ID | Sequential, starting at 1 |
| 2 | ID | R | [1..1] | Value Type | CWE |
| 3 | CWE | R | [1..1] | Observation Identifier | See Table A.10.1-1 |
| 4 | varies | O | [0..1] | Observation Sub-ID | Used to group multiple OBX segments for the same preparation step (e.g., H&E components). Common value links segments. |
| 5 | CWE | R | [1..1] | Observation Value | SCT code for the specific substance (fixative, medium, or stain) |
| 11 | ID | R | [1..1] | Observation Result Status | F |
{: .grid}

<p id="tA11.1-1" class="tableTitle"><strong>Table 2:A.10.1-1: Standard OBX-3 Codes for Specimen Preparation (aligned with DPIA)</strong></p>

| Preparation Type | OBX-3 Code | OBX-3 Display | Coding System | Level |
|---|---|---|---|---|
| Fixative | `430864009` | Tissue fixative | SCT | Specimen |
| Embedding medium | `430863003` | Embedding medium | SCT | Block |
| Stain | `397165007` | Stain | SCT | Slide |
{: .grid}

When a stain uses more than one substance (e.g., H&E), one OBX segment SHALL be sent per substance, and all segments for that stain SHALL share a common value in OBX-4 to group them.

OBX-3 (Observation Identifier) declares which preparation attribute the segment carries, and OBX-5 (Observation Value) carries the specific substance. For example, a formalin-fixed, paraffin-embedded, H&E-stained slide is conveyed as:

```
OBX|1|CWE|430864009^Tissue fixative^SCT||431510009^Formalin^SCT||||||F
OBX|2|CWE|430863003^Embedding medium^SCT||311731000^Paraffin wax^SCT||||||F
OBX|3|CWE|397165007^Stain^SCT|1|12710003^Hematoxylin stain^SCT||||||F
OBX|4|CWE|397165007^Stain^SCT|1|36879007^Water soluble eosin stain^SCT||||||F
```

#### A.10.2 OBX for Image Availability ([LAB-104])

OBX segments following the SPM in [LAB-104] convey Study and Series Instance UIDs linking the notification to the DICOM study. Because each value is a DICOM UID reference rather than free text, the Value Type (OBX-2) is `RP` (Reference Pointer) and OBX-5 carries the UID as the reference pointer.

| SEQ | DT | Usage | Card | Element Name | Example |
|---|---|---|---|---|---|
| 1 | SI | R | [1..1] | Set ID | 1 |
| 2 | ID | R | [1..1] | Value Type | RP |
| 3 | CWE | R | [1..1] | Observation Identifier | StudyInstanceUID^^DCM |
| 5 | RP | O | [0..1] | Observation Value | 1.2.3.4.5.1234567890 |
| 11 | ID | R | [1..1] | Observation Result Status | F |
{: .grid}

---

## Appendix B - Message Examples

Worked message examples are deferred pending validation of the message structure against the normative HL7 v2.5.1 `OML^O21` / `ORL^O22` definition (see WI-DPOW-01). Message content is specified by the Message Abstract Syntax and the segment definitions in Appendix A.

---

## Namespace Additions for Volume 2

No new namespace additions at this time. The PaLM registry of OIDs is located at the IHE PaLM Google Drive.
