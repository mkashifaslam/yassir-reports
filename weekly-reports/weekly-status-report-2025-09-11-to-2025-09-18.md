# Weekly Status Report - September 11 - September 18, 2025

## Summary

This week (Sep 1118) we merged 5 PRs and completed key EPayment New Arch tickets focused on hybrid payment flows, refund handling and wallet integration. Main themes: hybrid payments (cash + YC), refund/status fixes, and wallet v2 multi-currency alignment.

## In Progress

- [EPayment New Arch] Improve code coverage of hybrid payment flow - [SERV-9448](https://yassir.atlassian.net/browse/SERV-9448) (In Progress)
- [EPayment New Arch] Hybrid Payment implementation (Epic) - [SERV-8369](https://yassir.atlassian.net/browse/SERV-8369) (In Progress)
- [EPayment New Arch] Payment Methods List API Mobile Team Feedback (Epic) - [SERV-7222](https://yassir.atlassian.net/browse/SERV-7222) (In Progress)
- [EPayment New Arch] Create Search Feeder Service (Epic) - [SERV-7814](https://yassir.atlassian.net/browse/SERV-7814) (In Progress)
- [EPayment New Arch] EPayment core integration with Express, Mobility etc. (Epic) - [SERV-7313](https://yassir.atlassian.net/browse/SERV-7313) (In Progress)

## Blocked

- PSSP - SATIM payment method - [@shabrina](https://yassir-team.slack.com/team/U08D82KTDQV)

## Done

- [EPayment New Arch] Implement Cash hybrid payment flow - [SERV-9294](https://yassir.atlassian.net/browse/SERV-9294) (Done, updated 16 Sep 2025)
- [NewArch][Refund] Wallet Capture endpoint responded with 500 Error - [SERV-9458](https://yassir.atlassian.net/browse/SERV-9458) (Done, updated 15 Sep 2025)
- [EPayment New Arch] Align wallet v2 balance check api with multi-currency - [SERV-9296](https://yassir.atlassian.net/browse/SERV-9296) (Done, updated 15 Sep 2025)

## Testing

_No items currently in testing_

## Other Items (PRs merged this period)

- docs(mono): add copilot instructions ([PR #812](https://github.com/YAtechnologies/fs-epayment/pull/812))
- fix(apis): add X-Country-Code header to authentication and wallet requests (SERV-0000) ([PR #811](https://github.com/YAtechnologies/fs-epayment/pull/811))
- refactor(core): reorganize and refactor imports (SERV-9432) ([PR #810](https://github.com/YAtechnologies/fs-epayment/pull/810))
- fix(core): update payment amount handling in capture endpoint (SERV-9458) ([PR #802](https://github.com/YAtechnologies/fs-epayment/pull/802))
- feat(core): update hybrid payment refund status handling (SERV-9294) ([PR #801](https://github.com/YAtechnologies/fs-epayment/pull/801))

## Next Action Items (To Do / Backlog)

- [EPayment New Arch] Implement Hybrid Payment Fallback Logic for Mobility Transactions - [SERV-9221](https://yassir.atlassian.net/browse/SERV-9221)
  - Next step: implement the fallback response at `getPaymentMethod` to return hybrid payment options when wallet debit fails (see issue description for response format).
- [EPayment New Arch] Refactor Error Handling to improve maintainability - [SERV-9035](https://yassir.atlassian.net/browse/SERV-9035)
  - Next step: centralize API error parsing and add tests.
- Update tenant, customer and client_auth Typeorm entities - [SERV-8161](https://yassir.atlassian.net/browse/SERV-8161)
  - Next step: update entities to match latest DB schema and run migrations in staging.
- Refactor service tenant validator - [SERV-7629](https://yassir.atlassian.net/browse/SERV-7629)
  - Next step: implement guard + interceptor approach and add unit tests.
- Update docker-compose file for local development - [SERV-6598](https://yassir.atlassian.net/browse/SERV-6598)
  - Next step: consolidate environment compose files and publish dev instructions.
- [Search Feeder] Integrate Yassir cash transactions (wallet v2) - [SERV-8270](https://yassir.atlassian.net/browse/SERV-8270)
  - Next step: subscribe to wallet pubsub events and map payloads into search feeder schema.
- [Search Feeder] Integrate driver cash transactions - [SERV-8269](https://yassir.atlassian.net/browse/SERV-8269)
  - Next step: add subscription and ingestion pipeline for driver cash events.

---

**Report Period:** September 11 (Thursday) - September 18 (Wednesday)  
**Generated on:** September 18, 2025  
**Repository:** [fs-epayment](https://github.com/YAtechnologies/fs-epayment)  
**Jira Project:** [SERV - Financial Services](https://yassir.atlassian.net/browse/SERV)
