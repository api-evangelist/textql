---
name: textql-schedule-a-playbook
description: Create, run, deploy and retire a TextQL Playbook — a saved analysis instruction that runs on a schedule and delivers a report to Slack, email or the feed.
api: TextQL v2 API
base_url: https://app.textql.com/v2
operations:
  - v2.listConnectors
  - v2.createPlaybook
  - v2.updatePlaybook
  - v2.runPlaybook
  - v2.deployPlaybook
  - v2.getPlaybook
  - v2.listPlaybooks
  - v2.deletePlaybook
generated: '2026-08-30'
method: generated
source: openapi/textql-v2-openapi.yml + https://docs.textql.com/core/how-it-works/playbooks
---

# Schedule a recurring analysis

A Playbook is a saved analysis instruction that runs on a schedule and delivers the generated report.

## Steps

1. **Pick the connectors.** `v2.listConnectors`. You will pass their ids as `connector_ids`.

2. **Create an empty playbook.** `v2.createPlaybook` (`POST /v2/playbooks`). The v2 API creates the
   playbook empty and configures it separately — do not expect one call to do both.

3. **Configure it.** `v2.updatePlaybook` (`PATCH /v2/playbooks/{id}`) carries the analysis
   instruction, `connector_ids`, `dataset_ids`, and delivery — `slack_channel_id` and
   `tagged_slack_user_ids` for Slack.

4. **Test it before deploying.** `v2.runPlaybook` (`POST /v2/playbooks/{id}/run`) executes it once
   and returns the generated report. The response carries a `chat_id` — a playbook run materializes
   as a chat, so you can inspect the generated SQL with `v2.getChatCells` exactly as in
   `textql-run-an-analysis`.

   A run that completes without producing a report returns error code `no_report`. Check the
   instruction and that the connectors returned rows.

5. **Deploy it.** `v2.deployPlaybook` (`POST /v2/playbooks/{id}/deploy`) makes it active on its
   schedule. Filter with `v2.listPlaybooks?status_filter=deployed` to see what is live.

6. **Retire it.** `v2.deletePlaybook` (`DELETE /v2/playbooks/{id}`).
   **This is irreversible.** No restore, undo or trash operation is published for playbooks
   (`conventions/textql-conventions.yml`). Confirm before deleting.

## Cautions for agents

- `v2.runPlaybook` is **billable and not idempotent**. Retrying a timed-out run starts a second run.
- There is no outbound webhook. You cannot be notified when a scheduled run completes — TextQL
  delivers to Slack, email or the in-product feed only. To learn an outcome programmatically you must
  poll `v2.getPlaybook` / `v2.listChats`. See `asyncapi/textql-webhooks.yml`.
- Pagination on `v2.listPlaybooks` is `limit`/`offset`, not cursor — unlike the newer collections in
  the same API.
