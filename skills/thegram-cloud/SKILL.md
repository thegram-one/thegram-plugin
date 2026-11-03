---
name: thegram-cloud
description: Use Thegram, the AI knowledge base, through the vault_* tools. Reads pick the record type, status and date a question implies and cite records and sources; counts and rankings use vault_exact; when the person asks to save, each fact becomes the right one of the 22 record types in the right .gram document.
---

# Thegram

Thegram keeps knowledge as `.gram` documents of typed, linked records, and every change is a revision. Tools (host prefixes allowed): find and read `vault_browse`, `vault_search`, `vault_read`, `vault_exact`, `vault_changes`, `vault_comments`; write `vault_create`, `vault_edit`, `vault_move`, `vault_delete`, `vault_restore`, `vault_comment`, `vault_feedback`; contract `vault_guide`. A ref is `@mounts/<vault>/<folder>/<doc>.gram`, a record `…gram#<id>`, a section `…gram§<heading>`; use refs exactly as tools return them. Answer in the person's language and cite `path#id`.

## 1. Scope first

Call `vault_browse {}` once per conversation unless the person gave a ref. Each row has `ref`, `label`, `access` and top `folders`.

- `access: "write"`: the person's and their organizations' vaults. `access: "read"` (`@mounts/hub-team-*`): shared GramHub datasets, read-only, often tens of thousands of documents; new ones appear over time. There is no fixed list.
- Always pass `folder` (a vault, hub or dataset ref) to `vault_search`, `vault_exact` and `vault_changes`, and `path` to `vault_browse`. An unscoped search spans every vault and hub, is slow and mixes personal notes with public data.
- Never walk folders to find a document: names are opaque and there can be thousands. Use only refs a tool returned; a guessed folder fails with `E_PATH`.

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

`asset` is an image file that Thegram stores; do not write it. Any other type word, `note` and `idea` included, fails.

## 3. Reading

Pick the call from what the question asks:

| the question | the call |
|---|---|
| what exists, what is open | `vault_browse {path, summary:true}`: `counts` by type show which types exist (filter only on those), open records, each `rev` and `hash`, `next` |
| records of one kind | `vault_search {query, folder, type}`; a plain list: `vault_browse {path, select:{type, status, tag, from, to, date}}` |
| open or finished work | `type:"task"` (or `blocker`, `risk`) with `status:"open"`, `"done"` or `"closed"` (a class, `vault_search` only) or one `x` value (also in `vault_browse select`); deadlines: `date:"due"` with `from`/`to` |
| what happened, when | `type:"event"` with `from`/`to`; `order:"-at"` newest first, `"at"` oldest |
| why, on what basis | read the `decision`, then `vault_read {refs, relations:{direction:"both"}}`: `supports`, `depends`, `replaces`… reach evidence, sources and context |
| what changed, what held then | `vault_changes {folder or path, since or since_revision}`; `vault_read {refs, as_of:{effective_at or recorded_at or revision}}`: `effective_at` is the date a fact held, `recorded_at` when it was written |
| how many, total, top N | `vault_exact`, below |

1. **Ask plainly.** `vault_search {query, folder}`; `query` is never empty: a sentence with the exact names, codes or numbers a record would hold, plus `type`, `status`, `from`/`to` from the table. A named document or article is looked up directly. `terms` (one to four nouns every hit must hold) filter; `query` ranks, and a small-scope keyword search can order ties differently, so choose by title and tags and read before relying on a hit. `response_mode:"compact"` returns candidates without inline text.
2. **Learn a dataset from one hit.** Hits carry `tags`, `at`, `due`, `observed`; otherwise `vault_read` one hit with `fields:["tag"]`. Tags are `key:value` exactly as stored: copy them, never guess a value, and never put a tag string in `query`. `tag:["k:v"]` needs every tag. A hub folder may hold `guide.gram` (what dates and fields mean) and `coverage.gram` (period covered): read them when meaning or freshness matters. `group_by:"<tag key>"` with `per_group` shows representative hits and candidate counts per value, not exact totals; leave out `related_by` unless asked.
3. **Read the answer's metadata every time.**
   - `INDEX_PENDING`, `INDEX_UNREADABLE`, `coverage.files_pending` > 0 or `files_unreadable` > 0: documents are left out. Say the result is partial; `files_pending:0` alone does not prove completeness. `E_INDEXING`: nothing indexed yet.
   - `mode:"keyword"` with `reason:"WIDE_SCOPE_KEYWORD"`: a wide filtered search ranks by matching words, not meaning; use the dataset's own words and check `matched` and `gaps`.
   - `omitted` with `PAGE_LIMIT` or `LOW_RELEVANCE`, or `more`: call `next.tool` with `next.args` exactly, never a built cursor. Page only when the question needs every match, and say where you stopped.
   - `relevance:"weak"`: the hits are only nearby; say the dataset may not hold the answer.
   - `E_SCOPE_TOO_WIDE`: `as_of` and date fields other than `at`, `due`, `observed` need a small scope; take a folder from `candidates` or name `documents`.
   - `valid_at` only compares `at` and `due` and does not know what they mean: use it when the dataset defines them as an in-force interval; a deadline is not an expiry, and a date before the earliest record returns 0, never present current text as valid then.
4. **Open before quoting.** A hit's `summary` is cut short. `vault_read {refs:[…], fields:["t","d","x","at","due"]}` once for every ref you need (up to 16). Reads use the current document unless `as_of` or `snapshot` is given, so a hit and a later read do not share a snapshot. Name the dataset, the ref and the record's date; hub data is a published snapshot, not live.
5. **Conclude carefully.** One empty or odd result is not "none found": change the words or a tag spelling once. Never infer an exact count, absence, Top N, average or maximum from candidates, groups or unread pages; say what coverage is missing.
6. **Counts, rankings, totals: `vault_exact {query, folder}`** (or `documents`). Run it first with `exact:{catalog:true}`: that lists the fields, operators and alias words the source supports without running the question. Ask in those alias words: for a Gram vault they are Korean, e.g. `미해결 할 일 레코드 개수` returns `matched_count` (aliases cover `할 일`/`작업`, `위험`, `이벤트`, `미해결`, `완료`; other types have no alias word). Give an IANA `exact.timezone` for calendar periods. Only `execution.status:"complete"` with `exact_within_scope:true` is a result, within the selected scope. `unresolved` (the message says why), `incomplete`, `expired`, `denied` and `running` (continue with the returned `next`) are not: never invent a field, condition or unit, and for an unresolved count of a type, give the `counts` from `vault_browse {path, summary:true}` and say it is not an exhaustive query. Read the rows with `vault_read {refs, snapshot:execution.read_set}`; access is checked again, and relations are one hop inside the document.
7. **Rate a search only when the person says so.** If they say a ranked search did or did not answer, `vault_feedback {receipt, request_id, correction:{answered:true|false}}`. `vault_search {receipt}` replays it against the documents as they were.
8. Hubs are never a write destination. Do not copy hub records into the person's vault unless asked.

## 4. Writing, only when the person asks to save

A writable connection is not permission: save only what the person asked, where they asked or where the subject already lives. Never switch vault after a refusal.

1. Before the first write in a conversation call `vault_guide {task:"memory"}` (`create` or `edit` for the call shape). It is the live contract; where it differs from this file, follow it.
2. Search for a document on the subject first and add to it. A new document: `vault_create {path, title, base_revision:null, request_id, content:{text, records}}` with a short ASCII slug filename in an existing writable folder, headings in `text`, and each record's `[[id]]` alone on a line where it should appear.
3. One fact, decision, task or risk per record, typed by §2. `t` is a short name for it, never a date or id; `d` is one self-contained sentence that states the outcome (search shows it as the summary); field values are arrays: `{"t":["…"],"d":["…"]}`.
4. Required: `t` always; `x` for `task`, `goal`, `project`, `decision`, `risk`; `at` for `event`; `value` and `unit` for `metric`; `ref` for `artifact`. `x` values: task backlog, open, active, review, blocked, waiting, completed, cancelled, archived; goal open, active, completed, cancelled, archived; project planning, active, at-risk, on-hold, completed, cancelled, archived; decision proposed, accepted, rejected, superseded; risk open, mitigated, resolved, accepted, closed; event confirmed, tentative, cancelled. Also `p` (high, medium, low), `owner`, `tag`, `due` (task), `observed` (metric), `source` (a `source` record id), `ref` (one URL others can open, never a local path). Times belong only in `at`, `due` and `observed`; pass `effective_at` on the call when recording a past fact.
5. `id`: lowercase letters, digits and `-` with a type prefix (`task-…`, `dec-…`, `risk-…`), no dots, spaces or dates. Links: `[["part-of","prj-x"]]` with part-of, implements, depends, derives, related, supports, contradicts, replaces, blocks, enables; the target must exist or be added earlier in the same call.
6. Before changing a record, read it (`vault_read`, `fields:["t","d","x"]`). A status change is `set x` with `old`, the exact value read (`null` when empty), never a new record. A replacement sets the old record `superseded` and links `replaces` from the new one. Never remove a record that was true.
7. Send one `vault_edit {path, request_id, operations}` per coherent change with a new stable `request_id` per intended change; reuse it only to repeat the same call. Omit `base_revision` and `expected_hash` for `set`, `unset`, `link` and `add`: Core refuses with `E_PRECONDITION` only when a field you read has changed. `remove` and whole-body changes need both, from a fresh `vault_browse {path, summary:true}`.
8. While the answer says `sync.state:"queued"`, repeat the identical call (5–20 seconds). The write landed only at `"synced"`: then `vault_read` the changed records.

Operations:

- add: `{"op":"add","record":{"type":"task","id":"task-x","fields":{"t":["…"],"x":["open"]},"links":[["part-of","prj-y"]]},"place":{"section":"<existing heading>"}}`. `place` may also be `{"after":"<id>"}` or `"end"`; the section must exist.
- set: `{"op":"set","entity":"task-x","key":"x","value":"completed","old":"active"}`
- prose: `{"op":"edit-text","entity":"doc","kind":"rich","old":"<exact text occurring once>","new":"…"}`. Add records first, edit prose in the next call.
- A new section, or a change to the last section: `edit-text` anchored on the body's last line, e.g. `"old":"[[last-id]]","new":"[[last-id]]\n\n# Heading\n\nText"`. The `section` op rewrites an existing section that is not the last, with `base_revision` and `expected_hash` and the stored Markdown from `vault_read {refs:["<path>§<heading>"], source:true}`; never copy its trailing internal `@ids` lines.

## 5. Errors: fix the cause, never resend unchanged

| code | fix |
|---|---|
| `E_TEMPLATE` | `details.diagnostics` names the record, field and allowed values (an unknown type word fails here or as `E_DUPLICATE_FIELD`) |
| `E_PRECONDITION` | `details.conditions` shows the actual value or missing anchor: reread, decide, resend with a new `request_id` |
| `E_ARGUMENT` | missing `old`, `place` mixed with a body edit, or a wrong shape |
| `E_SYNTAX` | an id, link target or field key holds a bad character, usually a dot |
| `E_MISSING_TARGET` | add the link target first, in the same call |
| `E_AMBIGUOUS_TEXT` | `old` must occur exactly once: copy a longer exact span from a `source:true` read |
| `E_CAS_REQUIRED` | add `base_revision` and `expected_hash` from a fresh summary |
| `E_REQUEST_REUSE` | that `request_id` carried different content: use a new one |
| `E_RICH` "Invalid @ids line" | a `section` op reached the end of the body: use the `edit-text` append above |
| `E_LONG_PARAGRAPH` | a paragraph would pass 1,000 characters: put new detail in its own paragraph |
| `STALE_CURSOR` | the documents changed: rerun from the first page |
| `AGENT_SCOPE_DENIED`, `E_READ_ONLY`, `E_FORBIDDEN`, `E_PATH` | stop and tell the person (Thegram Settings → Connected agents); never write elsewhere |

A write answer may carry `decision_analysis` suggestions. They are never applied and need no action.

## 6. Report

Name the refs you relied on. For a write, give the document path, the new revision and the changed record ids after the readback. Say plainly what was partial: index pending, pages left unread, weak relevance, an exact query that did not complete.
