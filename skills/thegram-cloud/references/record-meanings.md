# Record meanings: the 22 types

Every Thegram document uses one vocabulary, `gram@1`, with 22 record types. Fields, statuses and supported operations follow Core's meaning catalog and the live `vault_guide {topic:"templates"}`. This skill narrows authoring selection for the current reading surface: although Core's broad `knowledge` category can include activity reports, do not use it as a fallback for past-only occurrences or outputs. Preserve those as dated events, actual artifacts, history or source material; reserve `knowledge` for the useful proposition itself.

Type names, field keys, status values, predicates, IDs and tag keys stay exactly as written here. Titles (`t`), descriptions (`d`), body text and tag values a person reads are written in the person's language.

Contents: Choose the type · Classify a source before writing · Writing a record · Closing a record · The 22 at a glance · Each type · Tags

## Choose the type: ask in this order

Split a source into atomic facts first. For each fact, the first question answered "yes" picks the type.

1. Is it the material itself (a conversation, thread, PR, file, export) rather than a fact from it? → `source`.
2. Did someone propose, choose, approve or reject an option? → `decision`.
3. Is it a rule that must be met (obligation, prohibition, eligibility, budget cap, acceptance criterion)? → `requirement`.
4. Is it a state someone wants to reach, including a target number? → `goal`.
5. Is it concrete work assigned to someone, including work the person handed to an AI? → `task`.
6. Is something already stopping progress? → `blocker`. Might something go wrong later? → `risk`.
7. Did something happen, or is it scheduled, at a stated time (an activity-log entry, meeting, launch, interview or trip)? → `event`. A report that a draft was written, a bug was reported, alternatives were compared or samples were cleaned up records an occurrence, even when it is not a milestone.
8. Is it a measured, observed or counted quantity with a unit? → `metric`.
9. Does a result exist that someone can open at a URL? → `artifact`.
10. Is an identified system, service or part described by what it is and does? → `system`, `service` or `component`.
11. Is a person's identity, role or profile described? → `person`. An AI agent's? → `agent`.
12. Is it one explicit execution with its input, process and result? → `run`.
13. Is it a named undertaking that groups several pieces of work? → `project`.
14. Otherwise it is information. Material offered to prove or disprove a specific stated claim → `evidence`. An unverified premise used for planning → `assumption`. Background needed to understand something else → `context`. How something changed over time → `history`. A reusable fact, explanation or source-supported lesson → `knowledge`. If an undated past report offers no such proposition, retain its actual claim in its source description/block rather than inventing an event time or a current knowledge card.

A name mentioned in passing is not a `person`; a product name mentioned in passing is not a `service`; a retrospective activity mention alone does not establish an assigned `task`; a future target number is a `goal`, not a `metric`. An old principle may still be valid knowledge, but the fact that an old activity happened is not itself a current principle.

## Classify a source before writing

1. Read the source body/diff and relevant follow-ups; separate choices, implementation, tracked work, observations and claims. Retain source identity, timestamp, scope and uncertainty for each fact.
2. Look up the existing fact/work/component/metric before adding anything. Update its fields and relations when the meaning is unchanged; repeated fixes do not create a new task lifecycle per commit.
3. Choose the type by meaning using the table below. Use only the current profile's fields, statuses and predicates. A merged implementation does not accept a proposed ADR, complete an entire task, or prove deployment or successful verification.
4. Add only relations the source establishes, in the stated direction. `A depends B` does not imply `B supports A`. For partial replacement, retain the still-applicable old decision and describe the changed scope with `related`; reserve `replaces` plus old `superseded` for full replacement.
5. Preserve source time separately from this write's runtime recording time. Reconcile current state and supported widget requirements, then submit the smallest sourced delta. No fact worth a record means no domain record; source coverage can still account for inspected material.

| Atomic meaning | Record/update | Section and relation | State boundary |
|---|---|---|---|
| a choice, proposed alternative or adopted policy | `decision` | Decisions; `part-of` project | `proposed` until a person accepts it, then `accepted`; `rejected`, or `superseded` when fully replaced |
| a desired outcome, required behavior or untested premise | `goal`, `requirement` or `assumption` respectively | Plan; `part-of` project/goal as sourced | do not infer these merely to fill a widget |
| tracked work or a sourced change to its progress | existing `task`, or add a genuinely sourced work item | Plan; `part-of` goal/project | partial → retain active work; review/unmerged PR → `review` when supported; `completed` only for the work's verified completion |
| architecture responsibility, interface or dependency | existing/new `component`, `system` or `service` | Architecture; `depends` in the sourced direction | code existence does not establish runtime health |
| possible harm versus an actual impediment | `risk` versus `blocker`; update affected task if sourced | Plan; `related` concern or blocker `blocks` task | `mitigated` while the harm is reduced but still possible, `resolved` once it is gone; do not invent priority, owner or due date |
| observation, test or report supporting/contradicting a claim | `evidence` | Log; `supports` or `contradicts` the claim | failed verification is evidence of failure; a source report is not this agent's runtime verification |
| reusable explanation or constraint | `knowledge` (or `context` for situational background) | relevant existing section; `part-of` project | preserve scope and uncertainty |
| a comparable measured quantity | same `metric` ID; `set value` + `set observed` | relevant section; retain report via `source`/`ref` | same definition, denominator and unit; one observation is valid |
| an identifiable build, report or deliverable | `artifact` with `ref` | Log; `derives` only from an actual producing record | publishing an artifact alone does not require an event |
| a meaningful dated occurrence, incident, verified rollout or milestone | `event` with sourced `at` and `ref`/`source` | Log; `implements` only a demonstrated choice, else `part-of` project | no event per commit or push; a tracked task's PR, merge, release, deployment or verification is one step event `part-of` that task ([automations.md](automations.md)); no guessed deployment or milestone |
| provenance or inspected low-signal material | reusable `source` or coverage note | cite near supported facts | routine image pins/formatting may need coverage only, with no event/task/evidence |

A factual outcome can update a task and component, explain a decision, and retain verification evidence from one commit. Do not fabricate completed historical tasks, intermediate statuses, events or revisions from its subject. Do not record chatter, raw logs, secrets, private assistant notes or every transient experiment. See [worked classification cases](examples.md) for ambiguous inputs.

An event's `at` is the actual occurrence time: a commit author date proves the commit time only, not a deployment time. Record source timestamps at their known precision in source descriptions or profile-supported source fields; do not force a decision or component onto a calendar with an invented `at`. Keep date-only facts date-only. `ref` is one URI others can open: a source permalink, release/PR URL or commit URL built from its actual remote. Never put a `file://` URL or a local path in `ref`; a local report stays in `d` with its path and hash, and without a remote retain the commit hash in provenance. Put a real source record ID, not a URL, in `source`.

## Writing a record

- `t` is a concise title naming the subject and claim, never a time or id. `d` is a short, self-contained explanation: keep the subject, applicable scope, essential conditions and exceptions needed to understand this record outside its document. Prefer completeness over a one-sentence limit; retain uncertainty and provenance without copying the whole source. Use the person's/source's natural terms, not keyword lists, invented synonyms or unsupported detail.
- Domain record IDs are stable short slugs with a type prefix (`dec-`, `ev-`, `task-`, `risk-`, `art-`, `evd-`, `m-`), not the current run time. Source/occurrence IDs retain stable item identity; run and request IDs identify the actual execution. Use only lower-case ASCII letters, digits and hyphens, preserving existing IDs and distinguishing opaque source identities as writing.md describes. Invalid characters such as a dot in a version can break the source line with `E_SYNTAX`.
- Array syntax does not make a singular field repeatable: `t`, `d`, `x`, `at` and `ref` keep one value unless the current profile says otherwise. `tag`, `s` and `source` may repeat. Keep source attribution near the supported claim.
- Task, goal, project, decision and risk need `x`; an event needs `at`, a metric `value` and `unit`, an artifact `ref`. Other optional fields: `p` (`urgent` `high` `medium` `low`), `owner`, `tag`, `due` (task, ISO date), `start` and `done` (task: when work began and finished). Unknown keys warn `W_EXTENSION_FIELD`.

Write records that survive retrieval outside their document: a concise `t` identifies the subject and claim, and a short `d` retains scope, essential conditions, exceptions and uncertainty from the source. For illustration only, if a source explicitly chooses monthly subscriptions for Cedar's paid team plan while leaving its personal plan free, "Pricing decision / Bill monthly" loses the subject and boundary; "Cedar paid team plan uses monthly subscriptions / Monthly billing applies to Cedar's paid team plan; the personal plan remains free" preserves them. Never introduce these details unless the source establishes them, and never add search terms as a list.

## Closing a record

- When a status moves to `done` or `closed`, rewrite `d` in the same patch to say how it ended (what replaced it, how the harm went away). Keep the old situation as a clause when it helps; the revision history keeps the earlier text. Thegram marks a description that was written before the status change.
- Link the evidence or event that resolved a risk or blocker with `related`. `supports`/`contradicts` argue for or against a claim, including a risk's claim that a loss may happen; never use them to say a risk is gone.
- `mitigated` stays open. Do not mark a risk `resolved` or a decision `accepted` without a source that says so; a merged change does not accept a proposal.
- Before changing a state, check relevant known claims in the authorized document. Related documents may be read when permitted, but do not update them merely because they mention the same fact; each additional write needs authorization for that destination and purpose.

## The 22 at a glance

| Type | Group | Means | Required | Status `x` (class) | ID prefix |
|---|---|---|---|---|---|
| `project` | execution | a named undertaking that groups work | `t` `x` | `planning` `active` `at-risk` `on-hold` (open), `completed` (done), `cancelled` `archived` (closed) | `prj-` |
| `goal` | execution | a state or number to reach | `t` `x` | `open` `active` (open), `completed` (done), `cancelled` `archived` (closed) | `goal-` |
| `task` | execution | concrete work assigned to someone | `t` `x` | `backlog` `open` `active` `review` `blocked` `waiting` (open), `completed` (done), `cancelled` `archived` (closed) | `task-` |
| `blocker` | execution | an existing impediment | `t` | free text; `open` (open), `completed` `resolved` (done), `closed` `cancelled` (closed) | `blk-` |
| `run` | execution | one explicit execution: input, process, result | — | — | `run-` |
| `decision` | judgment | an explicit choice: proposed, adopted, rejected | `t` `x` | `proposed` (open), `accepted` (done), `rejected` `superseded` (closed) | `dec-` |
| `requirement` | judgment | a rule to meet | — | — | `req-` |
| `risk` | risk | a possible future loss | `t` `x` | `open` `mitigated` (open), `resolved` (done), `accepted` `closed` (closed) | `risk-` |
| `metric` | measurement | a measured or counted value with a unit | `t` `value` `unit` | — | `m-` |
| `event` | measurement | an occurrence at a stated time | `t` `at` | `confirmed` `tentative` (open), `cancelled` (closed) | `ev-` |
| `knowledge` | information | a reusable fact, explanation or source-supported lesson | `t` | — | `k-` |
| `evidence` | information | proof or disproof offered for a stated claim | `t` | — | `evd-` |
| `context` | information | background needed to understand something | — | — | `ctx-` |
| `history` | information | how something changed over time | — | — | `his-` |
| `assumption` | information | an unverified premise | — | — | `asm-` |
| `source` | information | the identity and citation of material | `t` | — | `src-` |
| `person` | actor | a person's identity, role or profile | `t` | — | `per-` |
| `agent` | actor | an AI agent's identity, role and function | — | — | `agent-` |
| `system` | structure | a whole system: purpose, structure, operation | — | — | `sys-` |
| `service` | structure | a provided service: function, use, scope | — | — | `svc-` |
| `component` | structure | a part of a system or product: identity, function, interface | — | — | `cmp-` |
| `artifact` | structure | an existing deliverable with an openable link | `t` `ref` | — | `art-` |

Every type also takes `t` (single line, never a time or ID), `d` (self-contained explanation), `tag` (repeatable), `source` (repeatable, real `source` record IDs), `ref` (one openable URL) and `s` (scope). Field formats: `at`, `end`, `start`, `due`, `done`, `observed` are ISO dates or date-times with a zone; `end` is exclusive; `p` is `urgent` `high` `medium` `low`; `value` is a plain number.

Predicates, read "first → second": `part-of` (belongs to a project, goal, system, chapter), `depends` (work or goal relies on a condition), `blocks` (an impediment stops work), `enables` (a condition or result makes work possible), `implements` (work or result carries out a decision or requirement), `replaces` (new fully replaces old), `derives` (produced from an origin), `supports` / `contradicts` (a premise or evidence argues for or against a stated claim; see [knowledge-model.md](knowledge-model.md) section 3), `related` (an explicit connection that fits none of the above). Never invert a predicate to get a link you need.

## Each type

### `project`
Means a named undertaking that groups several pieces of work, with its scope and stages. Use it for each document's root and each real-world undertaking the person names (a side business, a move, a wedding). Not for every mention of an activity. `x` reflects declared state: `completed` only when someone says the purpose was achieved; `on-hold` for a project with no activity for a long time and no statement that it ended (say so in `d`); `archived` when it was archived or its period closed. Links: child records `part-of` it.

### `goal`
Means a state, result or number someone wants to reach ("pass the TOEIC with 900", "move to data engineering this year"). Only goals the person or source states; never infer one to fill a widget. `completed` only when the source reports it reached; `cancelled` when given up. A target number belongs here, not in `metric`. Links: `part-of` its project; tasks serving it link `part-of` the goal (goal progress counts only those).

### `task`
Means concrete work assigned to someone. Work the person handed to an AI has that assistant as `owner`; work the person must do themselves has `owner` set to the person's name. A bare past activity report does not establish an assigned task; use its dated occurrence or retain it in its source. A wish may instead be a `goal`. Fields: `start` when the work began, `done` when it finished, `due` only when stated. Status moves only with evidence: `completed` when the requested result was delivered and not rejected; `waiting` when it waits on the person's answer; `blocked` when something else stops it; `cancelled` when abandoned or refused, with the reason in `d`. Links: `part-of` project or goal, `depends` prerequisite tasks.

For current substantial research, the delegated request also authorizes the assistant to decompose its own execution into substantive question/coverage tasks, tagged `research-work`, in the same authorized document. Use the actual assistant as `owner`; retain scope, expected output, acceptance evidence and next action in `d`. These are not source-derived historical tasks or obligations assigned to the person. Reuse them across source units and sessions; completion requires the researched explanation and verified coverage, not just a saved batch or finished run. See [writing.md](writing.md).

### `blocker`
Means an impediment that exists now and actually stops progress (an account locked, an approval not given, a missing document). Not a possible future problem (`risk`). Close it (`resolved`) only when a source says it is gone, and rewrite `d` to how it ended. Links: `blocks` the task or goal it stops.

### `run`
Means one explicit execution recorded with its input, process and result, so it can be traced or repeated: an import of an export, a deep-research job, a CI pipeline run, a batch job. A finished run does not complete any task or goal. Not a general activity report. Thegram's dashboard reads scheduled-automation runs from `event` records tagged `run` instead ([automations.md](automations.md)); use the `run` type for executions that are not such automations. Links: `derives` the source it consumed, `part-of` the project.

### `decision`
Means an explicit judgment on a choice: proposed, agreed, approved or rejected. A plan for the future or the fact that something was done is not a decision. `proposed` until a person accepts; `accepted` when the person (not the assistant) chose it; `rejected` when declined; `superseded` with a `replaces` link from the new decision when fully replaced. An AI recommendation the person never confirmed stays `proposed`. Links: `part-of` project; the work that carries it out `implements` it.

### `requirement`
Means a rule that must be kept: obligation, prohibition, eligibility, application condition, processing standard, a limit the person set ("monthly hosting cost must be zero", "visa needs a bank statement"). Not a task assigned to someone, not a desired outcome. Links: `part-of` the project or goal; work that satisfies it `implements` it.

### `risk`
Means a loss or bad outcome that may happen. Not an incident that already happened (`event` or `blocker`) and not mere background. `mitigated` stays open while reduced but possible; `resolved` when gone (link the resolving evidence `related`); `accepted` when someone chose to live with it. `p` only when stated. Links: `related` to what it threatens.

### `metric`
Means a value actually measured, observed or counted, with a unit: a 5 km time, a weight, monthly spend, conversation counts. Past results count; future targets, phone numbers, identifiers, dates and lists of numbers do not. One record per quantity with a stable ID and unit; each new observation sets `value` and `observed` in its own write, so the history draws the trend. Never a new record per observation.

### `event`
Means a standalone occurrence (meeting, launch, interview, trip, milestone) whose time the source states. A publication date, a validity period or an application deadline alone is not an event. `at` is the occurrence time at its known precision (date-only stays date-only), `end` exclusive, `x` `confirmed` or `tentative`, `cancelled` when called off. One record per occurrence. Links: `part-of` its project, task or chapter; `implements` a decision only when it carried that decision out.

### `knowledge`
Means the useful proposition a statement teaches: a fact, explanation, principle or lesson that helps understand the subject under its stated conditions, even if its source is old ("Rust's Send error comes from…"). A line that only says a draft was written, a report was filed or cleanup finished is an occurrence/result, not reusable knowledge. Keep a separate insight only when the source actually supplies one; never invent a lesson to turn an activity into knowledge. Explicit goals, duties and decisions go to their own types. An explanation that came only from an AI answer says so in `d` ("as ChatGPT explained; not verified") and is `basis:reported`. Links: `part-of` project; premises `supports` the claims they argue for; `related` only for a connection no other predicate fits.

### `evidence`
Means a result or material presented to prove or disprove a specific claim that the source names: a test that passed, a measurement that contradicts an assumption. An independent activity report without a target claim is not automatically evidence or knowledge: retain its dated occurrence or source claim. Quoting something does not make it proof. A failed check is evidence of failure. Links: `supports` or `contradicts` the claim (decision, requirement, assumption, knowledge); `related` to a risk it resolved.

### `context`
Means the background, environment, scope or conditions needed to understand the main content: the person's job at the time, a move in progress, a team's constraints. A past activity alone is not ongoing background or reusable knowledge. Keep it to what explains later records; nothing sensitive beyond that. Links: `related` to the records where they matter.

### `history`
Means a record of how something changed over time: its stages, chronology and past changes. The periods or chapters of a timeline and the arcs of a theme are `history`. It is a narrative summary with ordinary fields; it has no lifecycle and never replaces the dated records it summarizes. A dated activity is an `event`; an undated past-only claim can remain in its source rather than becoming current knowledge. Links: periods `part-of` the document's root; records of a period `part-of` that period.

### `assumption`
Means a premise stated as the basis of a discussion, calculation or plan without being verified ("rent will stay the same next year"). Confirmed facts and obligations are excluded. Evidence later `supports` or `contradicts` it; never delete it when disproved.

### `source`
Means the identity and citation of material: a conversation, a Slack thread, a PR, an email thread, a calendar, an export file, a law or a paper. `t` names the material, `ref` is its permalink, `d` says what it covers (time span, scope, size). Facts cite it in their `source` field. A converter's file name or hash is system provenance, not a source of meaning. Links: `part-of` the project.

### `person`
Means one person's identity, role, career or profile as the source describes it. A name in passing or a contact line is not enough. Record only people who matter to the person's story, with the role the source states, and nothing sensitive about third parties. The vault owner is always `per-owner`. Links: `related` to the records where they matter.

### `agent`
Means an AI agent's identity, role and function as introduced by the source: ChatGPT, a custom GPT, Codex, Claude. Never invent an agent the source does not show. A task handed to an agent has that agent's name in `owner`.

### `system`
Means a system that performs a business, information or control function, described by purpose, structure or operation (the person's blog stack, a household budget spreadsheet with automation). An organization chart is not a system. Links: components `part-of` it; it `depends` services.

### `service`
Means a provided service described by its function, how it is used and its operating scope (Cloudflare Pages hosting the blog, a bank's open API). A bare product name is not a service record. Links: systems or components `depends` it.

### `component`
Means a part of a system or product described by identity, function or interface (the blog's build pipeline, the budget sheet's import script). Arbitrary part names and headcounts are excluded. Links: `part-of` its system; `depends` other components or services.

### `artifact`
Means a result that actually exists, with a link someone can open: a published post, a repository, a release, a shared conversation link. A file that is not reachable at a URL is not an artifact; describe it in the task's `d`. A planned deliverable is not an artifact yet. Links: `derives` the task or event that produced it.

## Tags used across workflows

Tag keys are fixed; values are lower-case ASCII slugs, except names a person reads.

| Tag | Meaning |
|---|---|
| `topic:<slug>` | the specific subject; the same slug in every document |
| `source:<connector>` `target` `run` `failed:<connector>` | scheduled automations ([automations.md](automations.md)) |
| `basis:observed` `basis:reported` `basis:inferred` | how a claim is known; exactly one on each knowledge, requirement, risk, evidence and metric record ([knowledge-model.md](knowledge-model.md) section 2) |
| `research-work` | the assistant's own research tasks ([writing.md](writing.md) section 2) |
