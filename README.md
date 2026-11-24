# Precogly prototype 02: App mockup (medical devices)

The medical device version of an earlier Precogly prototype built for financial services. It has the same three proposals, the same screens and the same flows, rebuilt around a fictional device maker, Acme Medical, which makes a brain-computer interface (BCI), a deep brain stimulation (DBS) programmer and an imaging archive:

- **OU Structure, Portfolio and Roles:** OU tree (Division, Business unit, Program, Team), portfolio items (Product family, Device, Software item), functional roles held through OU membership, Lead / Contributor / Viewer, propose and approve, tasks.
- **Integration-Ready Precogly Core:** central permission check, directory-managed OUs, group mappings, OpenID Connect sign-in, directory connectors outside Precogly, webhooks (including an eQMS for CAPA).
- **Security Requirements as the Common Thread:** engagements (threat model, security risk assessment, pen test), requirements per portfolio item from every source, controls that address requirements, calculated status.

Every record follows the shape of the CycloneDX 2.0 TM-BOM (see "Aligned with CycloneDX 2.0" below).

All data is fictional. Nothing is saved; reloading the page resets it.

## Laws and standards used

The laws and standards the prototype draws on:

| Source | Where it shows up |
|---|---|
| FD&C Act section 524B (Consolidated Appropriations Act 2023, section 3305) | Requirement pack for cyber devices on the US market: 524B(b)(1) postmarket vulnerabilities (the Vulnerabilities tab), (b)(2) updates and patches, (b)(3) SBOM (components linked to SBOM entries) |
| HIPAA Security Rule (45 CFR 164.308, 164.312) | Pack for items with PHI: encryption, audit controls, transmission security. PHI data flows carry HIPAA in their data profile |
| IEC 81001-5-1 | Main pack, applied to all health software; pen tests count under §5.7 |
| ISO 13485 | Design controls pack (7.3.x), validation 7.5.6, record retention 4.2.5, CAPA 8.5.2; the QMS certificate on the Submission tab |
| AAMI TIR57 and AAMI SW96 | Method and scales for security risk assessments, and a pack for safety class B and C items |
| NIST CSF 2.0 | Organization-level pack (for example ID.RA-08, vulnerability disclosure) |
| NIST SP 1800-24 | Pack for the PACS archive in the Imaging family |
| STRIDE, LINDDUN, CAPEC, CWE, MITRE ATT&CK for ICS, ATLAS, EMB3D | On each library threat: categories, weaknesses, attack patterns and techniques |

The "NIST CSF Healthcare Profile" is left out on purpose: [Precogly issue 339](https://github.com/precogly/precogly/issues/339) found that no such NIST publication exists.

Added to make the demo realistic:

| Source | Why |
|---|---|
| FDA premarket cybersecurity guidance ("Cybersecurity in Medical Devices") | The list of documents a 524B submission needs; its pack brings the FDA claims on the Submissions tab |
| EU MDR Annex I §17 and MDCG 2019-16 | The EU counterpart to 524B, for devices on the EU market; its pack brings the notified body's claims on the Submissions tab |
| IEC 62304 | Software safety class A, B or C on every item and on every component in the diagram |
| ISO 14971 | Risks and threats whose damage scenarios have a safety or health impact are copied to the safety risk file; the risk appetite is the risk acceptability policy (§4.2) |
| 21 CFR Part 11 | Approvals, sign-offs and attestations are electronic signatures with a stated meaning |
| FDA QMSR (21 CFR 820, which incorporates ISO 13485) | Mentioned on the ISO 13485 pack |
| CVSS with the medical device rubric | Scores on vulnerabilities |

The EU Cyber Resilience Act is not included: it excludes devices covered by the MDR.

## Aligned with CycloneDX 2.0

The prototype treats the current `2.0-dev` schema (`cyclonedx-2.0-bundled.schema.json`) as final. Each part of the mockup maps to it:

| In the mockup | CycloneDX 2.0 | Standards it supports |
|---|---|---|
| Damage scenarios (for example DS-1 "Unintended stimulation": affects the patient, safety impact catastrophic) | `risks.damageScenarios[]` with `affects` and `impacts[]` (category and level) | ISO 14971 harms, AAMI TIR57, HIPAA |
| Library threat ("Replay of captured messages", with STRIDE, CWE, CAPEC and ATT&CK for ICS) | `threats.threats[]` with `origin`, `categories`, `weaknesses`, `attackPatterns` | Taxonomies from the list |
| Threat in a threat model (T-16 on the therapy command flow) | `threats.threatScenarios[]` with `actor` (a persona), `attackVector`, `accessLevel`, `intent`, `damageScenarios` | FDA guidance (threat modeling) |
| Risk register: statuses identified, assessed, treated, monitored, retired; inherent, residual and target ratings; responses reduce, share, avoid, accept | `risks.risks[]` with `status`, `inherentRisk`, `residualRisk`, `targetRisk`, `responses[].strategy` | AAMI SW96, ISO 14971 |
| A risk's links: threat scenarios (likelihood), damage scenarios (impact), vulnerabilities, requirements | `relatedThreats`, `relatedDamageScenarios`, `relatedVulnerabilities`, `relatedRequirements` | AAMI SW96 |
| Risk assessment engagement: type, cadence, trigger, assessors, overall risk, conclusion | `risks.assessments[]` with `type`, `cadence`, `triggeredBy`, `assessors`, `overallRisk`, `conclusion` | AAMI TIR57, ISO 14971 |
| Risk appetite per domain (safety averse, privacy cautious) | `risks.riskAppetites[]` | ISO 14971 §4.2 |
| Pen test findings and supplier advisories, with an analysis state (in triage, exploitable, resolved, not affected) | `vulnerabilities[]` with `ratings` (CVSS) and `analysis` | 524B(b)(1), NIST CSF ID.RA-08, IEC 81001-5-1 §5.7 |
| Countermeasures with a type (preventive, detective, recovery) and the requirements they satisfy | `controls[]` with `category`, `effectiveness`, `satisfies` | All packs |
| Requirement codes (524B(b)(2), IEC 81001-5-1 §5.2) and product requirements with acceptance criteria | `definitions.standards[].requirements[]`, requirements with `acceptanceCriteria` | ISO 13485 7.3.3 |
| Diagram: trust zones, trust boundaries, data flows (protocol, encryption, authentication, data profile), components (IEC 62304 class, SBOM entry), assumptions | `blueprints[]`: `zones`, `boundaries` with `crossingRequirements`, `flows`, `assets` with `safetyIntegrityLevels` and `componentRef`, `assumptions` with `validity` | FDA security architecture views, IEC 62304, 524B(b)(3), HIPAA |
| Threat model version, reviewer, approver, frozen version, valid until | Blueprint `metadata` (`ordinalVersion`, `frozen`, `reviewer`, `approver`, `validityPeriod`) | ISO 13485 7.3.9 |
| E-signatures | `signatures` on the TM-BOM | 21 CFR Part 11 |
| Submissions tab: one claim set per pack (claims, evidence, attestation), and certificates | `declarations` (claims, evidence, attestations, affirmation) and `certifications` | FDA premarket guidance, EU MDR |

Two things the schema does not cover yet, assumed to come later and left out of the demo: category lists for MITRE ATT&CK for ICS, ATLAS and EMB3D (only STRIDE, LINDDUN, MAESTRO and MITRE ATT&CK have one), and an SBOM component's support status.

## What is different from the financial version

- **Damage scenarios instead of a "patient harm" field.** A threat or risk links to damage scenarios. The pill shows the impact category ("Safety", "Privacy"), so the same design fits a bank ("Financial"). Anything with a safety impact goes to Product Security, whatever its risk level, and to the ISO 14971 risk file.
- **Damage scenarios are a shared library** (Libraries › Damage scenarios), kept by Product Security with safety engineering. Each one says where it comes from: a harm in the ISO 14971 safety risk analysis (DS-1 to DS-3, DS-5) or the privacy impact assessment (DS-4). A product can add its own: DS-5 "Imaging studies unavailable" is for the PACS archive only. Product Security adds them and rates each impact; engineers only pick from the list when they add threats, and the risk assessment links to the library.
- **Item attributes.** Device class (FDA and EU MDR), IEC 62304 safety class, cyber device yes or no, markets (US, EU), handles PHI.
- **Packs are assigned by hand, with a reason.** There is no rule engine: deciding which law applies is a judgment call that auditors expect a person to make and explain. Regulatory Affairs assigns laws and regulations, Product Security assigns standards and company procedures. A pack can be assigned on an item (the items below inherit it) or on an OU (every item it owns). Each item's Requirements tab shows its packs, where each one comes from, the reason, who assigned it and when. Attributes stay as context and filters; when one changes (the DBS programmer added the EU market), the item is flagged under "Packs to review" until someone assigns a pack or records that no change is needed.
- **Vulnerabilities tab** on each item: pen test findings and supplier advisories, with CVSS, analysis state, linked threat and linked risk. V-8 shows a "not affected" decision (code not reachable) that can be shared as VEX.
- **Submissions tab** on each device. The lists of claims are not built in: they come with requirement packs. The FDA premarket guidance pack brings the 9 documents the FDA expects; the EU MDR pack brings the cybersecurity parts of the technical documentation a notified body reviews. A device gets one card per pack that applies to it: NeuroLink (US and EU) has both, the DBS programmer (US only) has the FDA list. Each claim shows its requirement, its evidence, status and conformance; Regulatory Affairs attests and signs each list separately. Certificates (ISO 13485, EU MDR) sit below. The Regulatory Affairs home and the Compliance page show one row per device and list. In another industry, other packs would bring other lists, for example IEC 62443-4-2 certification for an industrial controller, or a DORA review for a bank, and the tab would take its name from them.
- **E-signatures.** "Approve", "Sign off", "Mark verified" and "Attest" open a signing dialog (signer, meaning, password).
- **Roles.** "Security Team" becomes "Product Security"; "Legal" becomes "Regulatory Affairs".
- **Threat model workspace.** Opening a threat model engagement shows the workspace from today's app, with medical data. Each of the 5 threat models has its own diagram, threats and countermeasures:
  - **Overview:** diagram preview; completion status with assumptions and two medical checks (safety-relevant threats in the ISO 14971 risk file, components linked to an SBOM entry); the Review bar with version, reviewer, approver and "valid until", and Approve as an e-signature that freezes a version; Assumptions with their validity (one is invalid in the NeuroLink model).
  - **Changes since approval** (Clinician programmer threat model only): version 3 compared with frozen version 2, the diagram with changes outlined in amber, and "Sign off changes".
  - **Threat Analysis:** Tree › Threats › Threat and countermeasures. The detail shows the library threat (with its taxonomies), this scenario (actor, attack vector, intent, what it can lead to), the linked risk, and the countermeasures with their type and the requirements they satisfy. "Traceability table" lists threat › damage scenario › countermeasure › requirement › evidence.
  - **Diagram:** view only, plus Trust boundaries (derived from the flows that cross zones), Data flows and Components.
  - **Export** (button): threat model report, FDA threat model section, security architecture views, traceability matrix, TM-BOM (CycloneDX 2.0).
  - **Pen test feedback** (on Overview): how many findings the model predicted, and the ones it missed, with "Add as threat".
  - **No Risk Analysis, Pentests, Reports or Compliance tabs.** Those are separate activities with their own home: risk assessment, pen test, the product's Submission tab, and the product's Requirements tab.
- **Threat modeling and risk assessment are separate activities**, done by different people, on shared data:
  - **Threat model** (engineers, Security Champion): what can go wrong, how, and what it can lead to.
  - **Risk assessment** (Product Security, risk owners), one per product: how likely and how bad each risk is, who owns it, and whether to reduce, share, avoid or accept it. Each risk takes its likelihood from threat scenarios and its impact from damage scenarios. The page shows the overall risk, the conclusion at sign-off and today, the damage scenarios, the risk appetite with the risks above it, and threats not yet linked to a risk. Signing asks for a conclusion and produces the AAMI SW96 report.
  - **Risks** page: a read-only overview of every product's risks, for Product Security and management review.
- **Regulatory Affairs reads signed versions only.** Lena has no Engagements or Risks menu. The evidence links on the Submissions tab, and her item pages, open the signed version of a threat model (diagram, traceability, assumptions) or risk assessment (conclusion, residual risks), read only, with no drafts: the Clinician programmer threat model shows version 2 as approved, without T-33 or the remote programming flow. Unsigned work shows "nothing to show yet". She is involved where her role needs her: a task to give input when a safety risk is accepted (R-7), and a signed "Reportable?" decision for exploitable vulnerabilities that can lead to a safety damage scenario (F-4), following the FDA postmarket cybersecurity guidance and 21 CFR 806.
- **Requirements are the common thread, as in the financial version.** Each threat shows the requirement it addresses ("Addresses requirement: Firmware updates are signed and verified before install", with its sources 524B(b)(2), AAMI SW96 and the threat), and the traceability table reads threat › requirement › countermeasure › evidence. Each risk shows "Creates requirement: …" in the register, and each pen test finding or vulnerability does too (F-4 creates "Reject firmware older than the installed version"). One requirement can cover several threats (T-15 and T-16) and has one status. The Requirements tab lists them first; the requirement packs they come from are below the list.
- **Engagements follow the work, not just ownership.** A team sees engagements on its own items, on items it contributes to, and on the device or product above its items, because a system threat model covers its components. Erik (Implant Firmware) sees the NeuroLink system threat model, risk assessment and pen test ("Covers your item: Implant firmware") and the Clinician programmer engagements ("Your OU contributes"). An item's Engagements tab also lists the engagements on the device above it, and its threat model tile says which one it is approved through.
- **Only security risks to the device** go in the register. Most come from threat scenarios; some come from a vulnerability, such as R-17 (pen test finding F-4) and R-18 (supplier advisory V-7). Project risks, such as a submission delay, belong in the project plan.

## Files

- `index.html` opens directly in a browser. It needs internet only for the Geist font.
- `precogly-owl.png` is the logo.
- `swimlane.html` is a swimlane diagram of how the activities flow: one column per role, time running down the page, the 13 steps in words, and the records where activities meet. Hover over, tap or tab to any box for what happens there and a realistic example from the prototype's data. It also shows continuous threat modeling with feature blueprints, which the prototype does not have yet.

## How to use it

The demo starts on the **landing page**. Choose **Sign-In**, then pick a persona. You can switch persona later in the dark **Prototype** bar.

| Persona | Role | Default scope | What to look at |
|---|---|---|---|
| Ben Okafor | Product Security at Neurotechnology | Neurotechnology and below | Program overview, cyber devices covered, a high-risk waiver with a safety impact |
| Anna de Vries | Product Security at the root | Whole company | Same view for all divisions; an escalated risk acceptance above appetite |
| Carla Jansen | Security Champion (Lead) at Implant Systems | Implant Systems and below | Approvals waiting (with e-signature), "Members of my OU" |
| Erik Lund | Engineering at Implant Firmware | Implant Firmware only | My work, an overdue SBOM control; Engagements shows the NeuroLink engagements that cover his firmware and the Clinician programmer ones his team contributes to |
| Lena Petrova | Regulatory Affairs at Neurotechnology (Contributor) | Neurotechnology and below | A task to give input on a risk acceptance, a reportability decision (F-4), packs to review, Submissions (FDA and EU MDR claims) and attestation, compliance by pack. No Engagements or Risks menu: she reads signed versions only |
| Dev Sharma | Privacy at the root (Viewer) | Whole company | Items with PHI, a de-identification control to verify |
| Omar Haddad | Admin | Whole company | Setup health, Settings (item attributes, e-signatures, eQMS webhook) |

Things to try:

- **Engagements › NeuroLink system threat model v3 › Threat Analysis:** pick T-16. The detail shows the library threat (replay, with CWE-294, CAPEC-60 and ATT&CK for ICS T0830), the scenario (a nearby attacker, adjacent, targeted), and "Safety: Unintended stimulation". **Mitigate** with the library control: the approval goes to Product Security because of the safety impact.
- **Same threat model › Overview:** the version and "valid until" in the Review bar, the invalid assumption about hospital Wi-Fi, and Pen test feedback, where "Add as threat" turns finding F-4 into T-25.
- **Same threat model › Diagram:** trust boundaries, data flows with PHI and HIPAA, components with IEC 62304 class and SBOM entry. Compare the DBS threat model, where the legacy Bluetooth flow is unencrypted.
- **Threat T-16 › Risk R-12** opens the NeuroLink risk assessment: inherent, residual and target ratings, R-12 above the safety appetite until signed commands are in place, the damage scenarios, and "Conclusion today: inconclusive" next to "Conclusion at sign-off: acceptable".
- **Engagements › DBS programmer risk assessment 2025 › R-7:** above the safety appetite, with an acceptance proposed on a benefit-risk argument.
- **Portfolio › NeuroLink implant system › Vulnerabilities:** F-3 exploitable with a fix waiting for retest, V-7 in triage, V-8 not affected (code not reachable).
- **As Lena, Home:** "Waiting for you" has the R-7 input task (**Send input**), and "Reportability decisions" has F-4: **Decide** whether it must be reported to the FDA, with a reason and an e-signature. Then open an evidence link on NeuroLink's Submissions tab to see a signed threat model, read only.
- **As Lena, Home › Packs to review › DBS clinician programmer:** the EU market was added; **Assign pack** suggests EU MDR with a reason. After assigning, the EU MDR list appears on its Submissions tab.
- **As Lena, Portfolio › NeuroLink implant system › Submissions:** the FDA list and the EU MDR list, each with claims, evidence and conformance; **Attest and sign** one of them. Compare the DBS clinician programmer, which only has the FDA list.
- **As Ben, My tasks › View changes** on the Clinician programmer task: review the changes, then "Sign off changes" to freeze version 3.
- **Engagements › Clinician programmer risk assessment 2026** (as Ben): triggered by a design change; "Add risk" from a threat scenario and a damage scenario; "Sign off" with a conclusion.
- **Portfolio › NeuroLink implant system › Requirements:** one list from 524B, IEC 81001-5-1, HIPAA, ISO 13485, policy, threat model, risk assessment and pen test, with acceptance criteria on product requirements.
- **As Anna or Ben, Libraries › Damage scenarios:** where each damage scenario comes from, which products use it, and which risks and threats link to it. **Add damage scenario** for one product, then see it offered in that product's "Add risk" but not in others.
- **Risks:** the read-only overview with target ratings, impact categories and risks above appetite.

## Not covered

The diagram editor is unchanged and not mocked: the diagram here is view only. Use cases and data assets are summarized, not mocked. The TM-BOM export makes no file. No screenshots yet.
