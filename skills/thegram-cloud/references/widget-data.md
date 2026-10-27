# Author facts that the widgets can read

Read this when authoring data for a dashboard, timeline, calendar or graph. Correct classification comes first; a widget's empty state is preferable to invented facts. A saved widget selects records, not commit prose. Record validity, readiness and visible rendering are separate checks.

Contents: What the dashboard counts · Authoring rules for displays · Inspect the target before promising a display · Inspected widget data contract

## What the dashboard counts

Widgets select stored records, their template states, relationships and real history; they do not turn commit prose into progress. Never add, split, relabel or complete records to move a number. With the person's consent, correct a misclassified record to the type its facts support (which may be a task update, decision, component, evidence, artifact, event or coverage only). Preserve useful content and provenance; removals require fresh CAS. Do not force every wrongly recorded commit into an event.

| Surface | Data needed | Authoring consequence |
|---|---|---|
| Task board/checklist and progress rings | selected tasks and their status class | track real work; update existing tasks from completion evidence; do not manufacture historical tasks |
| Goal progress | tasks with `part-of` that goal | `related` does not count; a goal with no linked tasks can remain without a ratio |
| Decisions/pending choices | decision `x`; full replacement `replaces` links | proposed stays proposed despite merged code; acceptance is distinct from implementation |
| Risk/attention | actual risks/blockers, explicit states and sourced priority | an empty attention list does not prove health |
| Calendar/timeline | event `at`, metric `observed`, other records' supported `due` | omit unknown dates; a source commit date is not every fact's occurrence date |
| Metric trend | same ID, numeric `value`, same `unit`, sourced `observed` and available history | retain real observations; never create revisions just to draw a line |
| Activity/revision history | actual runtime recording time and loaded revision history | imports remain recorded today; no fake commit-era activity |
| Automation (new items, runs, sources, watched targets) | `run` events; items with `derives` to their run, `ref`, `at`, `source:`/`topic:` tags; `target` records and `related` detections | one run record per real run; reuse items and targets; see [automations.md](automations.md) |
| Sync/team/presence/workspace | runtime/capability data | document records cannot fabricate live sync, membership, presence or access |

The inspected newer widget model counts completion as done ÷ (open + done), excluding closed and unknown states; older Core tracker summaries can use all-task totals. Check the target's current contract and selected records before explaining a percentage.

## Authoring rules for displays

- For saved widgets, read this file and, when exposed, `gram_profile {path, topic:"presentation"}`. Preserve the saved layout and report missing data or unavailable capabilities; never invent records, owners or dates to make a widget ready.
- Metrics: one `metric` record per quantity (`value` a plain number, constant `unit`, `observed` a past ISO time with zone). Each new measurement is `set value` + `set observed` on that record, never a new record.
- Record-derived document views and personal dashboard arrangements are separate. Preserve the current host's saved layout; a starter recommendation does not install widgets or change account preferences. Older template/view hints do not establish the current personal dashboard configuration.

## Inspect the target before promising a display

1. Read the relevant records, fields and links. When available, request `gram_profile {path,topic:"presentation"}` for saved selectors, required fields, capability needs, readiness and digest. A view's default arrangement is not proof of the person's saved arrangement.
2. Map each requested widget to existing records and supported atomic facts. Preserve selected IDs, `recordType`, exact `status`, and legitimate gaps. Do not change a valid type/status to force selection. Unknown owner/priority/date stays absent even if readiness wants it.
3. Save only authorized factual changes. Preserve `ui-layout`, fixed sidebars and other user presentation. New layouts and publication need their own requested scope. Use an optional `presentation_digest` only if the tool exposes it and a current digest was actually returned, with the required fresh CAS pair.
4. Read back the fields, links, placement and real history. Recheck readiness if available. Report selector/model testing separately from actual host rendering and runtime verification.

A reported `E_CONTEXT_BUDGET` on the presentation profile is an integration gap, not evidence of no saved layout. In a schema exposing only `path/task/topic`, “select fewer fields” cannot be followed: no cursor/field selector exists. Do not guess layout requirements or bypass through an undocumented endpoint. A separate contract repair should return a compact, versioned summary/digest plus paginated per-widget requirements/readiness within the byte budget, with revision-bound cursors; test large layouts, Unicode and stale cursors. This skill does not repair that server contract.

## Inspected widget data contract

These are source-backed rules from the newer layout/2 widget model, not a promise that every connected build has it. Existing layout/1 may still load. Use the current profile/schema to resolve differences.

| Widget family | Actual selector or derived input | Required authoring facts and limits |
|---|---|---|
| Task checklist/board/tabs | `task`; saved IDs/type/exact status further intersect | real task `x`; review queue uses exactly `review` tasks or `proposed` decisions, not merged-commit guesses |
| Progress rings/status distribution | selected tasks, goals or decisions; the vocabulary's `open/done/closed` classes | `share`: done/(open+done), closed and absent-status excluded; no denominator → no ratio; accepted decision means adopted, not implemented; the detail list says what it counts and filters by status |
| Goal progress | tasks with outgoing `part-of` to the goal ID | `related`/`depends` do not count; goal's own status is separate from child-task completion |
| Attention/waiting | blocked tasks, unresolved blockers, scoped dependencies; attention also high/urgent risks in the open class (open or mitigated) and overdue records | source real impediments, `blocks`/`depends` direction and deadlines; never invent priority or a due date to appear here |
| Risk matrix/register | `risk`, actual `p`/`x`, its status class | matrix may show an unknown-priority group; empty register does not prove no risk or live health |
| Decision log/pending | `decision`, `x`, stored `replaces` | pending uses open status class and age from real native addition time; log derives date from earliest dated implementing event; preserve proposed ADR despite code merge; do not create an event to supply a missing date |
| Calendar/up-next/countdown | record date: event `at`, metric `observed`, otherwise `due` | source precision/time zone; default dates do not include a source commit date; undated records need no invented timestamp |
| Day agenda / milestone ranges | agenda: `event` with actual `at`; ranges: event `at→end`, task/goal `start→due` | only ordered compatible date precision becomes a range; ordinary point events remain points; add optional fields only if exposed/supported and sourced |
| Metric stat/trend/gauge/compare | `metric`, finite numeric `value`, `unit`, sourced `observed`; loaded contiguous field-change history | same identity/quantity/denominator/unit; compare only same unit; trend stops at a unit/value gap or creation; repeated same observed instant is one point; partial history cannot prove an older baseline |
| Knowledge card/evidence count | `knowledge/evidence/source` cards; count selects `evidence` only | reusable explanation belongs in knowledge; source coverage alone must not inflate verification evidence |
| Relations graph | stored edges with both endpoints known, deduplicated | only supported predicates; dependency is not proof; no cross-document fetch implied; partial replacement uses scoped prose/`related` so `replaces` does not falsely label the whole old decision replaced |
| Activity feed/heatmap/agent activity | real loaded revisions and `recordedAt`, author metadata | old source dates cannot recreate old activity; a `history`, `event` or `agent` record cannot fabricate runtime revisions/authorship |
| Owner/team/workspace/presence/access/sync | sourced owners plus authorized runtime membership/history/workspace/presence/save state | missing capability is not solved by fake person, run, sync or deployment records; no other-file writes/aggregation merely to make a tile ready |
| Automation: new items/runs/sources/watched/collected/ahead (`topic-feed`, `automation-runs`, `source-mix`, `watch-targets`, `source-inbox`, `upcoming-windows`) | `event` tagged `run` with actual `at`; records whose `derives` names a run; `source:`/`topic:` tags; records tagged `target` with `related` detections; non-run events with `at`/`end` | one run record per real run (failed sources as `failed:<source>` tags); a found item keeps one stable ID across runs and links `derives` only to the run that first found it; cadence and lateness come from run gaps, so never backfill or invent runs; run events never appear in calendars, agendas or streaks |
| Agent work: by agent/board/flow/waiting on you (`agent-lanes`, `agent-board`, `work-flow`, `needs-you`) | `task` with `owner` (the agent), `s`, `start`, `done`, `x`; non-run `event` with `at`/`ref` linked `part-of` (or `related`) a task; task and decision status moves in loaded history; `proposed` decisions; open `high`/`urgent` risks | one task per piece of work moved through `active`/`review`/`waiting`/`blocked`/`completed`; one step event per PR, merge, release, deployment or verification; the done lane shows a week by `done` or the completing move, so never backdate or invent either |

Selection intersects the widget kind's allowed types, default type when present, saved `recordType`, saved `recordIds` and exact raw `status`; duplicate IDs and the document owner are excluded. Limits/order affect display, not record truth. Missing/mismatched saved IDs should be reported, not repaired by duplicating facts.

Older Core projections can use an all-task tracker denominator; newer widget `share` excludes closed/unknown statuses. Core event timelines read event `at`; revision activity reads recording time. These are different axes. Operational recovery projections additionally require actual risk-linked tagged events and evidence links: only author incident/recovery tags and relations if those facts are supported. Source acceptance alone is not live health. See the matching Core projection contract before claiming a derived recovery state.

Readiness checks structural counts and selected fields; it does not establish source consistency, loaded historical observations or useful rendering. A `metric-trend` can be profile-ready with one metric but still have only one actual point. Test those layers separately. Do not invent one record per widget kind.

Team standup uses sourced task `done`, with a native status-change recording-time fallback when it is absent. Importing an old completion today without a known finish date may therefore appear as recent work in that surface. Disclose this renderer limit; do not invent `done` or old revisions. Handoffs require actual native `owner` before/after changes and author/member resolution; an event saying “handed off” does not supply that history. Person records do not create membership, and source reports do not produce sync/save state.
