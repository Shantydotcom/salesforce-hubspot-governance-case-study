# Architecture

## Why a governed boundary

Field-by-field two-way synchronization between a marketing platform and a CRM looks simple, but it hides the important questions. Which system is right when they disagree? Who may change ownership? What happens to a record that arrives incomplete?

This design answers those questions at one governed boundary, and gives every piece of contact data a defined owner.

## How data flows

![Architecture overview](../assets/architecture-overview.svg)

1. **HubSpot-originated contacts.** HubSpot is the marketing system. It is the source of a contact's marketing identity, lifecycle stage and original traffic source.
2. **Governed handoff boundary (simulated).** Contacts cross into Salesforce by their HubSpot identity, so a resubmitted contact updates its existing record instead of creating a new one. Likely duplicates are blocked by Salesforce's own duplicate protection. Contacts outside the handoff scope are never sent. In this project the boundary was a governed modeled load of synthetic contacts, not a live connector; see [Implementation boundary](implementation-boundary.md).
3. **Salesforce governance (real).** Each HubSpot-originated contact receives a governance verdict as it arrives and whenever it changes.
4. **Exceptions and remediation (real).** Contacts that can't proceed carry a readable reason. Fixing the data re-evaluates them automatically.
5. **Sales action and operational reporting (real).** Governed, sales-ready contacts produce a follow-up for Sales. Salesforce reports give data stewards a remediation queue.
6. **Integration-health analytics (real, read-only).** Power BI reads the current governed state from Salesforce, along with the handoff outcome evidence, and never writes back.

## System responsibilities

| System | Owns | Contributes | Never does |
|---|---|---|---|
| HubSpot | Marketing identity, lifecycle stage, original traffic source | Contact details for new contacts | Writes Salesforce governance state, ownership, account association or sales activity |
| Salesforce | The governance verdict and reason, record ownership, account association, sales follow-up | Corrections made by Sales | Invents marketing identity, lifecycle or attribution |
| Power BI | Integration-health analysis | — | Writes to Salesforce or HubSpot, or redefines governance or lifecycle state |

## Data ownership by domain

- **Marketing-owned:** identity, lifecycle stage and original source come from HubSpot. Salesforce reads them but does not redefine them.
- **Salesforce-owned:** governance state, ownership, account association and sales activity. HubSpot never writes them.
- **Shared and governed:** contact details such as name and email. Shared does not mean "last write wins". An explicit policy decides which system may change them.

## Why the native connector wasn't used

HubSpot's native Salesforce connector was assessed and deliberately not used, for three reasons:

- According to HubSpot's documentation, it requires an administrator-level connected user and synchronizes record ownership in both directions. Neither is acceptable in a shared Salesforce org built around least privilege.
- The HubSpot portal available to the project was on the Free tier, where the connector isn't offered.
- A modeled boundary let the project demonstrate the same governance outcomes with full control and evidence, without loosening security to make a demo work.

A bounded API-based integration was designed as a possible next phase and deliberately not built.

## Analytics relationship

Power BI is a read-only consumer. It reads governed contact state from Salesforce through the standard Salesforce connector, and the handoff outcome from the modeled load's evidence. Salesforce reports remain the place where work gets done; Power BI monitors health. See [Analytics](analytics.md).
