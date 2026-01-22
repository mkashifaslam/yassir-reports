# Weekly Status Report - January 9 - January 14, 2026

## Key Highlights

- PRs merged: 3; Avg merge: 77h 05m; Fastest: 2h 22m
- Status counts — In Progress: 1, Done: 2, Testing: 0, Blocked: 0
- Notable deliverables: [Multiple Partial Refunds - Add new config params of partial refunds to payment method table - SERV-10373](https://yassir.atlassian.net/browse/SERV-10373) ✅; [Multiple Partial Refunds - Add new columns of partial refunds to refund / transaction tables - SERV-10374](https://yassir.atlassian.net/browse/SERV-10374) ✅
- Notable PRs: [feat(core): add support of multiple partial refund (SERV-10337)](https://github.com/YAtechnologies/fs-epayment/pull/981), [feat(core): add columns for partial refund support in payment method table (SERV-10373)](https://github.com/YAtechnologies/fs-epayment/pull/986)
- Blockers: None
- Next actions: 0

## In Progress:

- [Support for Multiple Partial Refunds] - [SERV-10337](https://yassir.atlassian.net/browse/SERV-10337) (In Review) - [PR #981](https://github.com/YAtechnologies/fs-epayment/pull/981)

## Blocked:

_No blocked items_

## Done:

- [Multiple Partial Refunds - Add new config params of partial refunds to payment method table] - [SERV-10373](https://yassir.atlassian.net/browse/SERV-10373) ✅
- [Multiple Partial Refunds - Add new columns of partial refunds to refund / transaction tables] - [SERV-10374](https://yassir.atlassian.net/browse/SERV-10374) ✅

## Testing:

_No testing items_

## Other Items:

- [chore(core): add new refund destination and payment method columns in refund table (SERV-10374)](https://github.com/YAtechnologies/fs-epayment/pull/985)

## Next Action Items:

- getting error on multiple partial refund
	- refund transaction showing full wallet amount instead of showing given amount to refund request
- normal refund - 
	-  This transaction was already refunded.
---

**Report Period:** January 9 (Thursday) - January 14 (Tuesday)  
**Generated on:** January 14, 2026  
**Repository:** [fs-epayment](https://github.com/YAtechnologies/fs-epayment)  
**Jira Project:** [SERV - Financial Services](https://yassir.atlassian.net/browse/SERV)

--

External integration with auth
cashback - through new in olg
payment flow with 

---

yassir cash be used with external clients.

pin to use for wallet? - pin via sms, enter pin and fill the transaction, 
yassir auth service, re-use pin

- testing should be on staging
- check with Shabrina on external cashback api tasks - highest priority
- feb end deadline of yassir cash normal payment flow with shared auth service integration with pin,
   it also include auth module of merchant, service registration and customer management and handling.
	- check with Yousra how two auth (pin and customer access token work with this external system backend)
	- usually pin used for each time want to perform a transaction.
	- customer identification is required for transaction auth flow. (init, proceed, check, refund, reserve, capture, cancel etc.)
	- check with Yousra, will it support all payment apis for normal payment.