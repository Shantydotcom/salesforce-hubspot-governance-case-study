# Architecture

## Why a governed boundary

Field-by-field two-way synchronization between a marketing platform and a CRM looks simple, but it hides the important questions. Which system is right when they disagree? Who may change ownership? What happens to a record that arrives incomplete?

This design answers those questions at one governed boundary, and gives every piece of contact data a defined owner. It also separates two things that are easy to conflate: moving a record successfully, and that record being ready for Sales.

## How data flows

![Architecture overview](../assets/architecture-overview.svg)

1. **HubSpot-originated contacts.** HubSpot is the marketing system. It is the source of a contact's marketing identity, lifecycle stage and original traffic source.
2. **Revenue Handoff Integration (real).** A custom HubSpot → Salesforce API integration reads approved contacts from HubSpot and creates or updates them in Salesforce by their HubSpot identity, so a resubmitted contact updates its existing record instead of creating a new one. It runs on demand, writes only through a dedicated integration identity, and sends marketing data only.
3. **Salesforce governance (real).** Each HubSpot-originated contact receives a governance verdict as it arrives and whenever it changes, regardless of how it arrived.
4. **Exceptions and remediation (real).** Contacts that can't proceed carry a readable reason. A data steward fixes the data in Salesforce, and the contact is re-evaluated automatically.
5. **Sales action and operational reporting (real).** Governed, sales-ready contacts produce a follow-up for Sales. Salesforce reports give data stewards a remediation queue.
6. **Integration-health analytics (real, read-only).** Power BI reads governed contact state from Salesforce for a separate, modeled handoff population, along with that population's handoff outcome, and never writes back. See [Analytics relationship](#analytics-relationship).

## Revenue Handoff Integration

The integration is deliberately narrow:

- **HubSpot is read-only to it.** It holds read access to contacts only, and nothing is written back to HubSpot.
- **A dedicated integration identity.** It authenticates to Salesforce as an API-only integration user, not as an administrator or a person.
- **Marketing data only.** It sends the HubSpot identity, name, email, lifecycle stage and original traffic source. It never sends the governance verdict, the exception reason, the account association or the owner, and the integration user has no access to edit the governance fields.
- **Non-destructive.** Name and email are set when a contact is created, and afterwards only fill blanks in Salesforce. A blank HubSpot value never clears Salesforce data.
- **Approval-controlled.** The default mode is a dry run that reports what would change and writes nothing. A live write requires explicit approval for a specific record, and the run stops if the target environment or identity isn't the expected one.
- **No duplicate-rule bypass.** Salesforce's duplicate protection applies to the integration like any other writer.

After a steward remediated a live-integrated contact, a dry run proposed no changes: the integration doesn't fight the steward's decisions.

### Ownership at intake

Contacts the integration creates are owned by the integration identity. Assigning a human owner is a separate steward decision, kept out of the integration on purpose. Because the sales follow-up is assigned to the contact owner, ownership matters for sales-ready contacts; testing surfaced this, and the steward procedure reflects it (see [Testing summary](testing-summary.md)).

## System responsibilities

| System | Owns | Contributes | Never does |
|---|---|---|---|
| HubSpot | Marketing identity, lifecycle stage, original traffic source | Contact details for new contacts | Writes Salesforce governance state, ownership, account association or sales activity |
| Revenue Handoff Integration | Moving approved marketing data into Salesforce | — | Writes to HubSpot; sets governance state, ownership or account association; overwrites populated Salesforce contact details |
| Salesforce | The governance verdict and reason, record ownership, account association, sales follow-up | Corrections made by Sales | Invents marketing identity, lifecycle or attribution |
| Power BI | Integration-health analysis of the modeled population | — | Writes to Salesforce or HubSpot, or redefines governance or lifecycle state |

## Data ownership by domain

- **Marketing-owned:** identity, lifecycle stage and original source come from HubSpot. Salesforce reads them but does not redefine them.
- **Salesforce-owned:** governance state, ownership, account association and sales activity. HubSpot never writes them.
- **Shared and governed:** contact details such as name and email. Shared does not mean "last write wins". An explicit policy decides which system may change them.

## Why HubSpot's native Salesforce integration wasn't used

HubSpot's native Salesforce integration was assessed and deliberately not used, for three reasons:

- According to HubSpot's documentation, it requires an administrator-level connected user and synchronizes record ownership in both directions. Neither is acceptable in a shared Salesforce org built around least privilege.
- The project's HubSpot environment is a developer test account without a Professional or Enterprise hub, where the native integration isn't available.
- A custom integration could enforce the project's own rules: one-way data movement, a least-privilege identity, and no ownership sync.

Revenue Handoff Integration is that custom integration. It is not HubSpot's native integration and doesn't replicate it.

## Analytics relationship

Power BI is a read-only consumer. It reads governed contact state from Salesforce through the standard Salesforce Objects connector, and the handoff outcome from the modeled load's evidence. It reports on the modeled handoff population only: 24 synthetic submissions loaded into Salesforce by HubSpot identity, with Salesforce duplicate protection active. Contacts moved by the custom integration are not part of that population. Salesforce reports remain the place where work gets done; Power BI monitors health. See [Analytics](analytics.md).
