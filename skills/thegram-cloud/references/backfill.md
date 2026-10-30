# Backfilling memory from history

Use [the common writing method](writing.md) for investigation, document structure, source identity and checkpoints. This reference adds historical evidence rules. Revisions are append-only and recording time is when the import actually happened. `effective_at` labels an effective time without moving recording time. Import only when the person authorizes the purpose and destination; preserve sourced dates and the existing file's history. Never delete or rebuild a document to make a chart or history look older or more complete.

## 1. Gather and cover sources

- Confirm the authorized document and existing records, current profile and fresh revision/hash. Inventory one bounded window or item group at a time with stable IDs/permalinks, dates and inspected scope; read, reconcile and save it before widening the search. Account for low-signal material without inventing domain records.
- Read commit bodies/diffs, ADR status headers, issues/checklists, PR merge/review state, release/deployment reports and verification results together. Search later history before describing work as still open or completed. Commit size and subject counts can guide coverage but do not determine meaning or importance.
- Use the actual remote for repository identity and `ref`/`s`, not the local folder name. State branch/ref/hash and coverage gaps. A local checkout is not proof of current remote state; refresh only when network access is authorized.
- Separate documentary claims from inspected implementation and runtime observations. A report that says “deployed” remains a source report unless independently verified; preserve that distinction in `d` and completion reporting.
- When delegation is permitted, helpers may gather candidate facts; only the main agent writes. A source read is not permission to store it.

## 2. Classify atomic facts, then reconcile identity

Apply the decision procedure in [record-meanings.md](record-meanings.md) to each fact, regardless of whether its container is a commit, ADR, PR, conversation or report. One commit may support a choice, a component change, a tracked task update and failed-test evidence; it may also warrant no domain record. There is no one-event-per-commit default.

A `task` represents actual tracked work, supported by a request, issue, PR, checklist or plan. Reuse that task across recurring fixes. Do not infer a historical task or a sequence of `open → active → completed` from a commit subject. Imported tracked work can be added at its latest supported state with prior sourced dates explained in provenance; no replay of invented intermediate revisions is needed. Partial implementation and an unmerged PR do not establish completion. An ADR marked proposed remains proposed even when a stage of code has merged.

Prefer a `decision` for a choice, `component`/`service`/`system` for responsibility or dependency, `risk`/`blocker` for uncertainty or impediment, `evidence` for a scoped observation, `knowledge` for reusable explanation, `metric` for a measured quantity, and `artifact` for a deliverable. Use an `event` only for a meaningful sourced occurrence with an actual date. A release artifact, deployment occurrence and deployment verification are distinct facts; include only those supported.

Relations must express the source's meaning: `A depends B` does not license `B supports A`; use `supports`/`contradicts` for claim evidence. Allowed predicates are `related supports contradicts depends implements replaces derives blocks enables part-of`. Partial replacement keeps still-applicable old scope and status, explains the changed scope and uses `related`; full replacement may use `replaces` and old `superseded`. Do not invent relation names or infer decision acceptance from implementation.

Group facts into meaningful eras only where the sources support them. An optional `history` record is a narrative summary using supported ordinary fields, not a special replay schema. It neither stores a lifecycle nor creates past revisions. See the [worked examples](examples.md).

## 3. Write the import at its real recording time

- Reuse the authorized document. Only if creation is authorized and no established document exists, use one `vault_create` with `base_revision:null`, a stable `request_id`, a body with the purpose's sections and supported records. Place them under readable existing headings. No mandatory first event, era record or first-commit date.
- Use ordinary head patches for coherent batches of facts, with `old` on `set`/`unset`; preserve concurrent edits and stable IDs. Existing section replacements use the source read's revision/hash as described in [writing.md](writing.md) section 4. Batch by meaningful outcome and API limits, never by each commit to simulate history. Add targets before links and keep `place` separate from body edits.
- Keep actual source/occurrence/observation times separately from runtime `recorded_at`. Do not invent timestamps or fields. Ordinary imports use the present knowledge boundary. A request to reconstruct a subject from its beginning, including a timeline the person asks for, authorizes the ordered historical-state procedure in writing.md. It never backdates recording time or authorizes an additional document.
- Update only sourced states. Set a task's `done` when its finish time is known; omit unknown times. Resolve a risk only with evidence appropriate to its scope, link that evidence `related`, and rewrite the risk's `d` to how it was resolved. Do not auto-close goals, decisions or all related work after a commit.
- Reuse components by responsibility and metrics by comparable quantity/denominator/unit. A source-only image-pin change needs no component revision unless it establishes a meaningful change in responsibility, dependency or state.
- Metrics: retain actual observations and their original precision, never the era end as a substitute. One point is valid. A request to preserve a multi-observation series may require distinct real writes under the current history contract; do that only for sourced observations within the authorized scope, using the same metric ID, and disclose today's recording dates. Never split an import just to populate sparklines.
- Keep source coverage in a compact source record/narrative or existing source references. Repeat runs should reconcile the same facts and sources, not create duplicate entities or revisions. “No qualifying change” is valid.

## 4. Facts, narrative and display

- `d` states the source-supported outcome and scope; mark inference explicitly. No “first”, causal claim or runtime verification without evidence. Unknown owners, priorities and dates stay unknown.
- Use a real `source` ID in `source` and URLs in `ref`. Describe source timestamps at their known precision with no invented clock or custom replay fields. An event's `at` is the occurrence time, not automatically the cited commit author date.
- Organize the body around the subject's explanations, reasons and unresolved questions. Summarize important transitions where relevant, not every commit. Save the affected prose with its records when possible; otherwise finish it with fresh section preconditions before marking the unit complete.
- Read [widget data contracts](widget-data.md) for selector/date/status requirements and saved-layout readiness. Missing data does not authorize fabrication, layout rewriting or publication. Keep public-document privacy boundaries; no raw messages, credentials or personal names without authorization.

## 5. Verify

1. `vault_browse {path, summary:true}`: counts by type look right, and the open list holds only real open work.
2. Every record must be reachable from the document's root through meaningful relationships or source attribution. Page through `vault_browse {path, ids:true}` or `select` and read links and `source` fields with `vault_read`; inspect disconnected records and unresolved targets. Cross-document record links do not resolve: cite an authorized document's URL instead. Do not invent links merely to make the graph connected.

3. `vault_changes {path, since_revision: 0}` (follow every `more`): recording time is the actual import time, independently of sourced dates. Verify effective-time ordering and selected historical states only when deliberately used. A new record cannot exist in a revision before its real addition.
4. Follow the retrieval check in SKILL.md section 9: ask one or two representative natural questions with `vault_search`, then open the returned decision/risk or section with `vault_read` and verify its subject, conditions and answer. A title or section hint alone is not enough; report misses or model/index limits separately from successful storage.
5. Metrics have only the sourced observations: compare stable ID, definition, unit, `value` and `observed`; inspect real changes/history when multiple observations were authorized. A one-point series is valid and missing history remains disclosed.
6. Check semantic and widget results: sources can yield several types or coverage only; proposed ADRs stay proposed, partial/unmerged work stays incomplete, failures stay failures and links retain direction/scope. Task/goal widgets use actual tracked tasks and `part-of`; dates and metric observations are sourced. Read saved presentation requirements when available and disclose missing fields, runtime capabilities or unverified rendering. Do not change records to improve a number.
