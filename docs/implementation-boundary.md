# Implementation Boundary

This page is the authoritative statement of what this project is and isn't. Keeping these categories separate is deliberate. It is the same discipline the project applies to data.

## Real: implemented and verified

| Capability | Evidence |
|---|---|
| Revenue Handoff Integration: a custom HubSpot → Salesforce API integration, run on demand for approved records | Offline self-test; dry runs that wrote nothing; a live create of a fictional contact, verified by read-back as the integration identity |
| Dedicated, least-privilege Salesforce integration identity | Access verified in the org, and at runtime through the integration |
| Salesforce governance configuration (contact data model, access, page layout) | Deployed and inspected in a Salesforce org |
| Governance automation: classification, readable exceptions, automatic re-evaluation | Tested on positive, negative and remediation paths, including at 200-record volume and on a contact created by the live integration |
| Sales follow-up automation | Tested for governed readiness and duplicate prevention |
| Duplicate surfacing, by deliberately reusing Salesforce's standard duplicate management | Tested on the modeled handoff |
| Operational reporting: remediation queue and HubSpot-originated inventory | Matched independent queries |
| Power BI Pipeline Console, reading Salesforce data through the standard Salesforce connector | Built, and accepted after its rendered output was reviewed |
| QA, including a 200-record bulk test, regression and cleanup verification | See [Testing summary](testing-summary.md) |

## Modeled

| Item | What it was |
|---|---|
| Analytics handoff population | A governed, modeled load of 24 synthetic contact submissions into Salesforce. It used the same identity-based create-or-update approach as the integration, with Salesforce duplicate protection active, so 2 likely duplicates were genuinely blocked. Submissions outside the handoff scope were never sent. The Power BI console and the reconciliation figures describe this population, not records moved by the custom integration |

## Verified / referenced

| Item | Basis |
|---|---|
| HubSpot contact property names, lifecycle-stage values and original traffic source values | Checked in the project's HubSpot developer test account, in the HubSpot UI and through the API |

## Limits of the custom integration

- Runs on demand, one approved record per live run. Multi-record live runs, volume and API rate limits are untested.
- The live path has not exercised Salesforce duplicate rules; those were tested on the modeled handoff.
- Its access behavior depends on this org's sharing model and is not claimed for other sharing models.
- Owner assignment for integration-created contacts is a manual steward step, not automated.
- No monitoring, alerting, scheduling or automatic retry.

## Not built / not claimed

- HubSpot's native Salesforce integration
- Real-time, continuous or scheduled synchronization
- Bidirectional synchronization, or any write back to HubSpot
- HubSpot workflows, forms, scoring or other HubSpot configuration
- Production deployment
- Apex code or Lightning Web Components (the automation is declarative)
- Power BI reporting on contacts moved by the custom integration
- Publishing to the Power BI service or scheduled refresh

## Source not published

This repository is a case study. The following are intentionally private:

- Salesforce metadata and automation
- the integration source and its operating controls
- the Power BI report and semantic model
- scripts and datasets
- detailed testing methods
- the governance and implementation methodology

## Data

MeridianCloud Software is a fictional company. All contacts, companies and email addresses are synthetic.
