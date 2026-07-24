---
name: Consolidate tasks into a consignment
description: Batch an organization's unconsolidated delivery tasks into a consignment for handover on the Chaldal Egg Transport network.
api: openapi/chaldal-eggtransport-openapi-original.json
operations:
- Task_GetUnconsolidatedTasksForMyOrg
- Task_ConsolidateTasksForMyOrg
- Task_GetConsignmentsForMyOrg
- Task_GetConsolidatedTasksForConsignmentInMyOrg
- Task_GetLabelsPdf
---

# Consolidate tasks into a consignment

Use this skill to batch many delivery tasks into a single consignment for handover to the
Chaldal Egg Transport (Gogo Bangla) network.

## Auth
Send `X-Egg-ApiKey` and `X-Egg-UserId` headers on every call (credentials via tech@chaldal.com).

## Steps
1. **List what is pending.** Call `Task_GetUnconsolidatedTasksForMyOrg` to get the tasks in
   your org that are not yet part of a consignment.
2. **Consolidate.** `POST` the selected task ids to `Task_ConsolidateTasksForMyOrg`. Expect
   `204` on success; `404` if a task id is unknown, `403` if not permitted.
3. **List consignments.** Call `Task_GetConsignmentsForMyOrg` to confirm the new consignment
   and get its tracking reference.
4. **Inspect membership.** `Task_GetConsolidatedTasksForConsignmentInMyOrg` returns the tasks
   inside a given consignment.
5. **Print labels.** Call `Task_GetLabelsPdf` to produce printable labels for handover.

## Notes
- No idempotency key is available; treat consolidation as non-idempotent and re-list before
  retrying after a timeout.
- Cross-reference `conventions/chaldal-conventions.yml` (bulk import, error envelope) and
  `errors/chaldal-problem-types.yml`.
