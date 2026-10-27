# Worked examples

These examples are fictional (`prj-atlas`, `prj-strings`, `example.com`, `<vault-id>`). Use them for shape only: never save them as facts, never submit placeholders, and replace paths and IDs with the authorized document's real ones.

## Retrieve before deciding

For "What did we choose for Atlas rollout, and is it actually shipped?", discover mounts, brief the existing document, recall rollout decisions, then read their context, implementing events and supporting evidence with `gram_relations`. The correct answer distinguishes an accepted decision from verified implementation and says which claims are observed, reported or inferred. This request alone causes **no write**.

On a later visit use `gram_changes` with the saved `next`, then fetch the relevant changed records. For "What did we know before rollout?" use `gram_read {path, revision: 0}` or a sourced recording-time boundary. Do not derive a past state by deleting current records in a copy.

## Replace a choice, keep its evidence and close a risk

This patch assumes a sourced full replacement and an inspected passing report; it does not claim a release or deployment. Both closed records get a `d` that says how they ended, the evidence supports the new decision, and the resolved risk is linked `related` rather than `supports`.

```json
{"path": "@mounts/<vault-id>/<root>/strings.gram", "request_id": "slugify-unicode",
 "operations": [
  {"op": "add", "record": {"type": "source", "id": "src-unicode-regression-report",
    "fields": {"t": ["Unicode slug regression report"], "d": ["Retained regression report for the slug change; read in full, including the Korean title cases."], "ref": ["https://example.com/reports/unicode-regression"]},
    "links": [["part-of", "prj-strings"]]}, "place": "none"},
  {"op": "add", "record": {"type": "decision", "id": "dec-unicode-slugs",
    "fields": {"t": ["Keep Unicode letters in slugs"], "d": ["slugify keeps letters and digits of every script and joins the rest with one hyphen."], "x": ["accepted"], "source": ["src-unicode-regression-report"]},
    "links": [["part-of", "prj-strings"], ["replaces", "dec-ascii-slugs"]]}, "place": {"section": "Decisions"}},
  {"op": "set", "entity": "dec-ascii-slugs", "key": "x", "value": "superseded", "old": "accepted"},
  {"op": "set", "entity": "dec-ascii-slugs", "key": "d", "value": "Replaced by keeping Unicode letters; slugs used to drop every non-ASCII character.", "old": "Slugs keep only ASCII letters and digits."},
  {"op": "add", "record": {"type": "evidence", "id": "evd-unicode-slugs",
    "fields": {"t": ["Korean titles keep their letters in slugs"], "d": ["The regression report shows '카페 Latte' maps to '카페-latte'."], "source": ["src-unicode-regression-report"], "tag": ["basis:observed"]},
    "links": [["supports", "dec-unicode-slugs"]]}, "place": {"section": "Log"}},
  {"op": "set", "entity": "risk-ascii-only", "key": "x", "value": "resolved", "old": "open"},
  {"op": "set", "entity": "risk-ascii-only", "key": "d", "value": "Resolved: Korean titles now keep their letters in slugs, as the regression report shows.", "old": "Korean titles turn into empty slugs."},
  {"op": "link", "entity": "evd-unicode-slugs", "predicate": "related", "target": "risk-ascii-only"},
  {"op": "add", "record": {"type": "task", "id": "task-slug-length",
    "fields": {"t": ["Cap slug length"], "d": ["Very long titles still produce unbounded slugs."], "x": ["open"]},
    "links": [["part-of", "prj-strings"], ["related", "dec-unicode-slugs"]]}, "place": {"section": "Plan"}}
 ]}
```

## Create and evolve within an authorized destination

Asked to establish a project's memory, one `gram_create` sets up readable sections around the subject's questions (for a project often Overview, Architecture, Plan, Decisions, Evolution and Log) with its root, goals, tasks, components, decisions, metrics, sources and evidence in one real revision; an older observation time does not backdate the document. The approved choice and its remaining verification task are explained in the narrative, not only as dates and links. Later, four separately verified outcomes are each one patch:

1. **Replace a choice:** preserve the previous decision, set `superseded`, create the accepted replacement with `replaces`, and attach source/evidence explaining why the constrained preview replaces gradual rollout. Adoption is still distinct from execution.
2. **Complete verified work:** only after the fictional check confirms the intended endpoint, set the existing task and goal to `completed`; add an event `implements` the accepted choice, an artifact `derives` that event, and evidence supporting the completed task. Avoid creating a second “completed task” record.
3. **Reconcile narrative:** an exact `edit-text` updates Overview and Evolution after the structured outcome. This is separate from adds with `place`; use the last returned rev/hash. Compare the overview and details before calling the document complete.
4. **Record a new measurement:** update the same `met-availability` numeric value and observation time, keep its definition/unit, add the specific report and supporting evidence, and link the metric to that report. The earlier report remains in the document. Readback requests `value`, `unit`, `observed` and `source` explicitly. Two real observations may yield two chart points on a supporting renderer; never fabricate points between them.

Use the current document's exact section heading/node ID. Follow `gram_profile` when preserving/adding rich nodes. `[[*]]{type=task status=active view=list}` and `[[*]]{type=risk status=open view=table}` are useful only when actual matching records exist; an empty collection does not justify inventing a task or risk.

## Uncertain replies and concurrent changes

Suppose the replacement patch succeeds but its reply is lost. Reconcile with a fresh brief/context/read, then resend the identical payload with the same request ID if needed. A replay should not create a second outcome. Read back the old status, new decision, replacement link and body placement before reporting saved.

Suppose another writer adds a release caveat after your brief. Sending your original base must fail stale. Read the new revision and relevant section, preserve the caveat, and re-evaluate whether the replacement remains needed. If it does, rebuild the intended delta using the new rev/hash and a **new** request ID. If the same intended fields now disagree, resolve the evidence/intent conflict rather than overwrite them. `STORAGE_CONFLICT` requires the same reconciliation with the provider's latest state.

Reusing an ID with different operations must fail; do not use replay as a way to change history. A create sent with a `request_id` replays the same way; without one, list/read the destination and compare its content before deciding whether creation is still needed.

## Time and source-grounded metrics

The example describes `met-availability` as the fraction of successful probe requests in each report's defined window. Do not equate it with every user's availability or infer a deployment caused a measurement change. Keep each report and its measurement window as evidence. Update a stable metric only when quantity, denominator and unit remain comparable.

If an actual source gives only `2026-10-10`, retain that date; do not attach the example's synthetic clock time. If only one observation is sourced, record one observation. Historical backfill needs explicit authorization and understood temporal semantics; do not split one current import into artificial historic writes to populate sparklines or a revision slider.

## Classify mixed sources before importing

Typical mixed inputs and their correct handling:

- A single commit containing an accepted cache choice, component responsibility, reusable constraint and scoped test result: four atomic facts plus provenance, no commit event.
- ADR-0047 still marked proposed while Ethereum stage-1 code is merged: retain documentary `proposed`, update the component and active task description, leave later stages incomplete. Its stated dependency on another ADR stays `depends`, with no inverse evidential `supports`.
- An unmerged PR under review and a failed check: the existing task remains `review`; record failure evidence and a real blocking condition. No completion, deployment or success event.
- Repeated fixes to one tracked problem: reuse the task/component, preserve current incomplete status and attach new scoped evidence. No new task per commit and no fabricated intermediate revisions.
- A real dated deployment report and retained build: event for the actual rollout time, artifact for the build and evidence explicitly attributed to the report; an existing verification task completes only when that report establishes its completion. A source report is not this agent's live check.
- Two comparable observations: use the same metric ID, quantity and unit, with actual observation timestamps. Their updates are real writes; an unrelated import need not generate metric history.
- Noisy image pin commits: account for the source range only. No new task, component, event or verification evidence is required.
- A partial decision replacement: keep still-applicable old scope accepted, describe the narrower change with `related`. Reserve whole-record `replaces`/`superseded` for a full replacement like the one above.
- A fix that resolves one risk and only reduces another: set the fixed risk `resolved` and rewrite its `d` to how it ended in the same patch; link the regression report `related`, not `supports`. The reduced risk becomes `mitigated` and stays open with a `d` that says what is still uncertain.

Sourced dates remain in provenance unless the fact has an actual supported date field. Current imports do not recreate old revision activity. [Widget data contracts](widget-data.md) explains why correct type, status, dates, relationships and real revision history matter for useful display; layout readiness never authorizes fake data.
