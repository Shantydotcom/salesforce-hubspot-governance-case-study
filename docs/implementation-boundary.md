# Implementation Boundary

This page is the authoritative statement of what this project is and isn't. Keeping these categories separate is deliberate. It is the same discipline the project applies to data.

## Real: implemented and verified

| Capability | Evidence |
|---|---|
| Salesforce governance configuration (contact data model, access, page layout) | Deployed and inspected in a Salesforce org |
| Governance automation: classification, readable exceptions, automatic re-evaluation | Tested on positive, negative and remediation paths, including at 200-record volume |
| Sales follow-up automation | Tested for governed readiness and duplicate prevention |
| Duplicate surfacing, by deliberately reusing Salesforce's standard duplicate management | Tested at the boundary |
| Operational reporting: remediation queue and HubSpot-originated inventory | Matched independent queries |
| Power BI Pipeline Console, reading Salesforce data through the standard Salesforce connector | Built, and accepted after its rendered output was reviewed |
| QA, including a 200-record bulk test, regression and cleanup verification | See [Testing summary](testing-summary.md) |

## Modeled / simulated

| Item | What it was |
|---|---|
| HubSpot → Salesforce inbound handoff | A governed, modeled load of 24 synthetic contact submissions into Salesforce. It used the same identity-based create-or-update path an integration would use, with Salesforce duplicate protection active, so 2 likely duplicates were genuinely blocked. Submissions outside the handoff scope were never sent |

## Verified / referenced

| Item | Basis |
|---|---|
| HubSpot contact property names and lifecycle-stage values | Checked by the project owner in a real HubSpot portal on the Free tier |

## Designed, not built

| Item | Status |
|---|---|
| Bounded API-based HubSpot → Salesforce integration | Designed as a possible next phase; deliberately not built |
| Dedicated integration user | Least-privilege access is defined but deliberately not assigned to any user |

## Not built / not claimed

- A live HubSpot connector or synchronization
- HubSpot workflows, forms, scoring or other HubSpot configuration
- Bidirectional synchronization of any kind
- Production deployment
- Apex code or Lightning Web Components (the automation is declarative)
- Publishing to the Power BI service or scheduled refresh

## Source not published

This repository is a case study. The following are intentionally private:

- Salesforce metadata and automation
- the Power BI report and semantic model
- scripts and datasets
- detailed testing methods
- the governance and implementation methodology

## Data

MeridianCloud Software is a fictional company. All contacts, companies and email addresses are synthetic.
