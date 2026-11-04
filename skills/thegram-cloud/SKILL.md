---
name: thegram-cloud
description: Use Thegram, the AI knowledge base, through its five tools. Reads pick the record type, status and date a question implies and cite records and sources; counts and rankings use vault_exact; when the person asks to save, each fact becomes the right one of the 22 record types in the right .gram document.
---

# Thegram

Thegram keeps knowledge as `.gram` documents of typed, linked records, and every change is a revision. Five tools (host prefixes allowed):

| tool | `op` | does |
|---|---|---|
| `vault_ask` | `ask` (default), `browse`, `changes`, `comments`, `guide` | answers a question with hits and the best records' fields; vaults, folders, outlines, summaries and trash; what changed; comment threads; the write contract |
| `vault_read` | – | reads records, sections and documents |
| `vault_add` | `create`, `comment`, `restore`, `feedback` | writes that remove or overwrite nothing |
| `vault_change` | `edit`, `move`, `delete` | writes that change, relocate or remove |
| `vault_exact` | – | exact counts, totals, rankings |

A ref is `@mounts/<vault>/<folder>/<doc>.gram`, a record `…gram#<id>`, a section `…gram§<heading>`; use refs exactly as tools return them. When an answer's `next` names a tool you were not listed (`vault_search`, `vault_browse`…), call it with `next.args` exactly: it still works. Answer in the person's language and cite `path#id`.

## 1. Scope

- Without `scope`, `vault_ask` searches the person's own vaults, never shared hubs; the answer's `scope.hubs_skipped` says what it left out. Most questions need nothing more.
- `vault_ask {op:"browse"}` lists the vaults once when you need a location: `access:"write"` are the person's and their organizations' vaults; `access:"read"` (`@mounts/hub-team-*`) are shared GramHub datasets, read-only, often tens of thousands of documents. Search a hub only by passing its ref as `scope`.
- Never walk folders to find a document: names are opaque and there can be thousands. A guessed path fails with `E_PATH`.

## 2. The 22 record types

Every record has one `type`. This table picks the filter when reading and the type when writing. Choose by what the sentence means, not its topic; "not" names the neighbouring type.

- Information
  - `knowledge`: a stated fact, explanation or experience, including a report of something already done. Not a goal, duty or decision.
  - `evidence`: a result or material offered to prove or refute a named claim. A quote, or a report with no claim to prove, is `knowledge`.
  - `context`: background, scope or conditions that make something else understandable. Not a report of an activity.
  - `history`: how a thing changed over time. One past activity is `knowledge`; a dated occurrence is `event`.
  - `assumption`: a premise stated without verification. Not a confirmed fact or a duty.
  - `source`: identification and citation of a document, law or paper.
- Judgment
  - `decision`: an explicit choice, proposed to superseded. A plan or an action taken is not one.
  - `requirement`: a duty, prohibition, qualification or acceptance criterion. Not a task given to someone, not a wish.
- Execution
  - `goal`: a state, outcome or target number to reach. A target figure is a goal, never a `metric`.
  - `task`: concrete work assigned to someone. Not a finished-activity report, not a wish.
  - `project`: a named undertaking that groups tasks: overview, scope, phases.
  - `blocker`: an obstacle that exists now and stops progress. A possible one is a `risk`.
  - `run`: one explicit execution with input, process and result (a job, experiment, automation). A finished run does not complete its task or goal.
- Risk
  - `risk`: an uncertain future loss. Already happened: `blocker`. Background: `context`.
- Measurement
  - `metric`: a measured, named number with `value` and `unit`, past actuals included. Targets, IDs, phone numbers and schedules are not metrics.
  - `event`: something with a stated date or time: meeting, launch, incident. A publication date or deadline alone is not an event.
- Actor
  - `person`: who someone is: role, profile. A name or contact line alone makes no person.
  - `agent`: an automated agent's identity, role and abilities.
- Structure
  - `system`: what a system does: purpose, structure, operation. Staffing and org charts are not systems.
  - `service`: a service offered: features, use, scope.
  - `component`: a part of a system or product: identity, function, interface.
  - `artifact`: an output that exists, with its link (`ref`). A deliverable not yet made is a `goal` or `task`.

`asset` is for images, never for text. Any other type word (`note`, `idea`…) is refused with `E_UNKNOWN_TYPE` and the allowed list.

## 3. Reading

| the question | the call |
|---|---|
| anything about the person's notes | `vault_ask {question, terms?}` |
| records of one kind, status or period | `vault_ask {question, plan:{conditions:[…]}}`, below |
| what exists, what is open | `vault_ask {op:"browse", path, summary:true}`: `counts` by type, open records, each `rev` and `hash`, `next` |
| a plain list | `vault_ask {op:"browse", path, select:{type, status, tag, from, to, date}}` |
| why, on what basis | read the `decision`, then `vault_read {refs, relations:{direction:"both"}}`: `supports`, `depends`, `replaces`… |
| what changed | `vault_ask {op:"changes", folder or path, since or since_revision}` |
| what held then | `vault_read {refs, as_of:{effective_at or recorded_at or revision}}`: `effective_at` is when a fact held, `recorded_at` when it was written |
| how many, total, top N | `vault_exact`, below |

1. **Ask plainly.** `question` is the person's question with the exact names, codes or numbers a record would hold; `terms` are one to four nouns every hit must hold. The answer has `hits`, `records` (the best record hits with `t`, `d`, `x`, `at`, `due`, so most questions end there) and `interpretation` (what was applied, and from which words).
2. **Filters are conditions you read from the question.** `plan:{conditions:[{field, op?, value, quote}]}` with `field` `record.type`, `record.status` (an `x` value), `record.status_class` (`open`, `done`, `closed`), `record.tag`, `record.s`, `record.at`/`record.due`/`record.observed` (`op` `gte`, `lte` or `eq`), and `quote` the question's own words it comes from (`source:"context"` when it comes from the conversation). Nothing is guessed: a value the vocabulary lacks or a quote not in the question comes back in `interpretation.unresolved` with the allowed values; fix it or ask the person. Ranking options (`limit`, `order` `-at`/`at`, `group_by`, `per_group`, `mode`, `as_of`, `valid_at`, `documents`) go in `options`.
3. **Learn a dataset from one hit.** Hits carry `tags`, `at`, `due`, `observed`. Tags are `key:value` exactly as stored: copy them into `record.tag`, never guess one, never put one in `question`. A hub folder may hold `guide.gram` (what dates and fields mean) and `coverage.gram` (period covered). `group_by` shows representative hits per value, not totals.
4. **Read the answer's metadata every time.**
   - `INDEX_PENDING`, `INDEX_UNREADABLE`, `coverage.files_pending` or `files_unreadable` > 0: documents are left out; say the result is partial. `E_INDEXING`: nothing indexed yet.
   - `mode:"keyword"` with `reason:"WIDE_SCOPE_KEYWORD"`: ranked by matching words, not meaning; use the dataset's own words.
   - `omitted` (`PAGE_LIMIT`, `LOW_RELEVANCE`) or `more`: call `next.tool` with `next.args` exactly, never a built cursor. Page only when the question needs every match, and say where you stopped.
   - `relevance:"weak"`: the hits are only nearby; say the data may not hold the answer.
   - `interpretation.exhaustive`: the question asks for a whole population; the hits are not one. Use the `vault_exact` call in `next`.
   - `E_SCOPE_TOO_WIDE`: `as_of` and other date fields need a smaller scope; take one from `candidates`.
   - `valid_at` only compares `at` and `due`; a deadline is not an expiry.
5. **Open before quoting** what `records` does not cover: `vault_read {refs:[…], fields:[…]}` (up to 16 refs), or a section `…§heading`. Name the dataset, the ref and the record's date; hub data is a published snapshot.
6. **Conclude carefully.** One empty result is not "none found": change the words or a tag spelling once. Never infer a count, absence, Top N, average or maximum from hits, groups or unread pages.
7. **Counts, rankings, totals: `vault_exact {query, folder}`** (or `documents`). First run it with `exact:{catalog:true}` to list the fields, operators and alias words the source supports, and ask in those words: for a Gram vault they are Korean, e.g. `미해결 할 일 레코드 개수` returns `matched_count`. Give an IANA `exact.timezone` for calendar periods. Only `execution.status:"complete"` with `exact_within_scope:true` is a result. `unresolved`, `incomplete`, `expired`, `denied` and `running` (continue with `next`) are not: never invent a field or unit; for an unresolved count of a type give the `counts` of a browse summary and say it is not exhaustive. Read rows with `vault_read {refs, snapshot:execution.read_set}`.
8. **Rate a search only when the person says so:** `vault_add {op:"feedback", receipt, request_id, correction:{answered:true|false}}`.
9. Hubs are never a write destination; do not copy hub records into the person's vault unless asked.

## 4. Writing, only when the person asks to save

A writable connection is not permission: save only what the person asked, where they asked or where the subject already lives. Never switch vault after a refusal.

1. Before the first write in a conversation call `vault_ask {op:"guide", task:"memory"}` (`create` or `edit` for the call shape). It is the live contract; where it differs from this file, follow it.
2. Look for a document on the subject first and add to it. A new one: `vault_add {op:"create", path, title, base_revision:null, request_id, content:{text, records}}` with a short ASCII slug filename in an existing writable folder, headings in `text`, and each record's `[[id]]` alone on a line where it should appear.
3. One fact, decision, task or risk per record, typed by §2. `t` is a short name, never a date or id; `d` is one self-contained sentence that states the outcome; field values are arrays: `{"t":["…"],"d":["…"]}`.
4. Required: `t` always; `x` for `task`, `goal`, `project`, `decision`, `risk`; `at` for `event`; `value` and `unit` for `metric`; `ref` for `artifact`. `x` values: task backlog, open, active, review, blocked, waiting, completed, cancelled, archived; goal open, active, completed, cancelled, archived; project planning, active, at-risk, on-hold, completed, cancelled, archived; decision proposed, accepted, rejected, superseded; risk open, mitigated, resolved, accepted, closed; event confirmed, tentative, cancelled. Also `p` (high, medium, low), `owner`, `tag`, `due` (task), `observed` (metric), `source` (a `source` record id), `ref` (one URL others can open, never a local path). Times belong only in `at`, `due` and `observed`; pass `effective_at` when recording a past fact.
5. `id`: lowercase letters, digits and `-` with a type prefix (`task-…`, `dec-…`, `risk-…`), no dots, spaces or dates. Links: `[["part-of","prj-x"]]` with part-of, implements, depends, derives, related, supports, contradicts, replaces, blocks, enables; the target must exist or be added earlier in the same call.
6. Before changing a record, read it (`vault_read`, `fields:["t","d","x"]`). A status change is `set x` with `old`, the exact value read (`null` when empty), never a new record. A replacement sets the old record `superseded` and links `replaces` from the new one. Never remove a record that was true.
7. Send one `vault_change {op:"edit", path, request_id, operations}` per coherent change, with a new stable `request_id` per intended change; reuse it only to repeat the same call. Omit `base_revision` and `expected_hash` for `set`, `unset`, `link` and `add`; `remove` and whole-body changes need both, from a fresh browse summary.
8. While an answer says `sync.state:"queued"`, repeat the identical call (5–20 seconds). The write landed only at `"synced"`: then `vault_read` the changed records.
9. Move, delete (to the trash) and restore only when asked: `vault_change {op:"move", path, to}`, `vault_change {op:"delete", path}`, `vault_add {op:"restore", path}`. Comments: `vault_add {op:"comment", path, action:"open"|"reply"|"resolve"|"reopen", …}`.

Edit operations:

- add: `{"op":"add","record":{"type":"task","id":"task-x","fields":{"t":["…"],"x":["open"]},"links":[["part-of","prj-y"]]},"place":{"section":"<existing heading>"}}`. `place` may also be `{"after":"<id>"}` or `"end"`; the section must exist.
- set: `{"op":"set","entity":"task-x","key":"x","value":"completed","old":"active"}`
- prose: `{"op":"edit-text","entity":"doc","kind":"rich","old":"<exact text occurring once>","new":"…"}`. `place` cannot be combined with a body edit; to add a record and prose in one revision, leave `place` out and write the record's `[[id]]` line in `new`.
- A new section, or a change to the last section: `edit-text` anchored on the body's last line, e.g. `"old":"[[last-id]]","new":"[[last-id]]\n\n# Heading\n\nText"`. The `section` op rewrites an existing section that is not the last, with `base_revision`, `expected_hash` and the stored Markdown from `vault_read {refs:["<path>§<heading>"], source:true}`; never copy its trailing `@ids` lines.

## 5. Errors: fix the cause, never resend unchanged

| code | fix |
|---|---|
| `E_ARGUMENT` | a missing or unknown `op` (the message lists the allowed ones), missing `old`, `place` mixed with a body edit, or a wrong shape |
| `E_UNKNOWN_TYPE` | the record `type` is not one of the 22: pick one from `details.allowed` |
| `E_TEMPLATE` | `details.diagnostics` names the record, field and allowed values |
| `E_PRECONDITION` | `details.conditions` shows the actual value or missing anchor: reread, decide, resend with a new `request_id` |
| `E_SYNTAX` | an id, link target or field key holds a bad character, usually a dot |
| `E_MISSING_TARGET` | add the link target first, in the same call |
| `E_AMBIGUOUS_TEXT` | `old` must occur exactly once: copy a longer span from a `source:true` read |
| `E_CAS_REQUIRED` | add `base_revision` and `expected_hash` from a fresh summary |
| `E_REQUEST_REUSE` | that `request_id` carried different content: use a new one |
| `E_RICH` "Invalid @ids line" | a `section` op reached the end of the body: use the `edit-text` append |
| `E_LONG_PARAGRAPH` | a paragraph would pass 1,000 characters: start a new one |
| `STALE_CURSOR` | the documents changed: rerun from the first page |
| `AGENT_SCOPE_DENIED`, `E_READ_ONLY`, `E_FORBIDDEN`, `E_PATH` | stop and tell the person (Thegram Settings → Connected agents); never write elsewhere |

A write answer may carry `decision_analysis` suggestions; they are never applied and need no action. A queued write that keeps failing ends `blocked` after its retries; report it.

## 6. Report

Name the refs you relied on. For a write, give the document path, the new revision and the changed record ids after the readback. Say plainly what was partial: index pending, pages left unread, weak relevance, an exact query that did not complete.
