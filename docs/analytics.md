# Analytics: Pipeline Console

The Power BI page **CRM Data & Integration Health**, in its Pipeline Console design, answers one question:

> *Are our HubSpot contacts arriving in Salesforce ready for Sales?*

![Power BI Pipeline Console](../assets/power-bi-pipeline-console.png)

*Power BI Pipeline Console: integration health for the modeled inbound population (synthetic data; simulated HubSpot handoff).*

## The five areas

| # | Area | Question it answers | What it shows |
|---|---|---|---|
| 1 | How the data moved | Did the contacts we sent arrive? | 24 submitted from the modeled HubSpot export → 2 blocked as likely duplicates, 1 not sent (outside handoff scope) → **21 reached Salesforce** → **15 ready for Sales**, **6 need attention** |
| 2 | Overall Data Health | How usable is what arrived? | **71.4%** of received contacts are ready to use (15 of 21). **87.3%** of required handoff information is present |
| 3 | What needs fixing? | Why can't the rest proceed? | Contacts needing review, ranked by missing-information reason |
| 4 | Where contacts are in the sales journey | Where in the funnel are the received contacts? | Count and share for each HubSpot lifecycle stage, in funnel order; contacts with no stage are shown openly |
| 5 | Contacts that need attention | Who exactly needs fixing? | A worklist of contacts needing review and their reasons. It points to the Salesforce remediation report, where the fixing happens |

## KPI definitions

- **Ready to use:** the share of contacts that reached Salesforce and passed governance.
- **Required information filled in:** the share of required handoff information (email, linked account, lifecycle stage) present across contacts that reached Salesforce.

## Design principles

- **Read-only.** Power BI never writes to Salesforce or HubSpot.
- **Honest about its data.** The page states that the HubSpot handoff is simulated and the data synthetic.
- **No decoration.** There are no invented trends, targets or metrics the data can't support.
- **Faithful categories.** Lifecycle stages and exception reasons are shown exactly as Salesforce stores them, not regrouped or relabelled in the report.
- **A clean population.** Test records are excluded, so the console reflects the modeled inbound population only.

## Analytics vs. operations

Salesforce reports are the operational layer, where data stewards work the remediation queue. The Power BI console is the monitoring layer, showing whether the handoff as a whole is healthy. Neither duplicates the other's job.
