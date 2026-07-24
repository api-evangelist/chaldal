---
name: Create and track a third-party delivery
description: Book a delivery task on the Chaldal Egg Transport (Gogo Bangla) network, print its label, and follow it to completion.
api: openapi/chaldal-eggtransport-openapi-original.json
operations:
- Identity_GetDeliveryAreas
- Task_CreateThirdPartyDeliveryTask
- Task_GetTaskStatus
- Task_GetLabelsPdf
- Task_GetTaskAuditTrail
- Task_OrgCancelTask
---

# Create and track a third-party delivery

Use this skill to book and follow a single delivery on the Chaldal Egg Transport
(Gogo Bangla) logistics network.

## Auth
Every request needs two header credentials (issued by tech@chaldal.com):
- `X-Egg-ApiKey: <your api key>`
- `X-Egg-UserId: <your user id>`
There is no OAuth and no idempotency key — do not retry a create blindly on a network
timeout; check status first (see step 5).

## Steps
1. **Confirm serviceability.** Call `Identity_GetDeliveryAreas` and verify the destination
   area is covered before creating a task.
2. **Create the delivery.** `POST` `Task_CreateThirdPartyDeliveryTask` with the pickup and
   drop-off details. Capture the returned task identifier.
3. **Print the label.** Call `Task_GetLabelsPdf` for the new task to get the printable
   shipping label PDF.
4. **Track status.** Poll `Task_GetTaskStatus` for the task id. Handle `404` (unknown task)
   distinctly from a valid in-progress status.
5. **Audit if disputed.** For a full history of state transitions call `Task_GetTaskAuditTrail`.
6. **Cancel if needed.** `Task_OrgCancelTask` cancels a task; expect `409`/`412` if the task
   has already progressed past a cancellable state, and `403` if your org lacks permission.

## Error handling
- `401` — missing/invalid `X-Egg-ApiKey`/`X-Egg-UserId`.
- `403` — authenticated but not permitted for this org/resource.
- `404` — the task does not exist in your org.
- `409` / `412` — conflicting or precondition-failed state (refetch status, then retry).
See `errors/chaldal-problem-types.yml`.
