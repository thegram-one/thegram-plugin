---
name: thegram-cloud
description: Read and write Thegram memory with the vault_* tools. One method answers from the person's vaults and from every GramHub shared dataset with the right scope and filters, and, when the person asks to save, records facts as typed records in the right .gram document without write errors.
---

# Thegram

Tools (host prefixes allowed): reads `vault_browse`, `vault_search`, `vault_read`, `vault_changes`, `vault_comments`, `vault_guide`; writes `vault_create`, `vault_edit`, `vault_delete`, `vault_restore`, `vault_move`, `vault_comment`, `vault_feedback`. A ref is `@mounts/<vault>/<folder>/<doc>.gram`, a record `…gram#<id>`, a section `…gram§<heading>`; use refs exactly as tools return them. Answer in the person's language and cite `path#id`.

## 1. Scope first

Call `vault_browse {}` once per conversation unless the person gave a ref. Each row has `ref`, `label`, `access` and top `folders`.

- `access: "write"`: the person's and their organizations' vaults. `access: "read"` (`@mounts/hub-team-*`): shared GramHub datasets, read-only, often tens of thousands of documents, and new ones appear over time. There is no fixed list.
- Always pass `folder` (a vault, hub or dataset folder ref) to `vault_search`, and `path` to `vault_browse {select}`. An unscoped search spans every vault and hub, is slow and mixes personal notes with public data.
- Never walk folders with `vault_browse` to find a document: names are opaque and there can be thousands. Use only refs a tool returned; a guessed folder fails with `E_PATH`.

## 2. One way to read any vault or hub

The same steps serve a personal vault and every hub. Nothing here depends on what a dataset holds.

1. **Ask plainly.** `vault_search {query, folder}`; `query` is never empty. A sentence with the exact names, codes or numbers a record would hold. A named document or article is looked up directly and does not depend on the index.
2. **Learn the dataset from one hit.** Hits carry `tags`, `at`, `due`, `observed`; otherwise `vault_read` one hit with `fields:["tag"]`. Tags are `key:value` exactly as stored: copy them from a hit, never guess a value. Every data record in a hub carries one `종류` tag (its kind); guide and source records carry none. `group_by:"종류"` with the question's words lists which kinds match and how many. A record without tags keeps the source row as JSON in `d`: filter it by words and dates. Date meanings differ per dataset; a dataset folder may hold `guide.gram` (meanings) and `coverage.gram` (period covered): read them with `vault_browse {path, ids:true}` and `vault_read` when meaning or freshness matters.
3. **Narrow with structure.** `tag:["k:v", …]` (every tag must hold), `from`/`to` (`YYYY`, `YYYY-MM` or a date), `valid_at:"YYYY-MM-DD"`, `terms:[…]`, `group_by:"<tag key>"`, `per_group`, `type`, `status`, `limit` up to 20. `order:"-at"` returns the newest by `at` first and `"at"` the oldest first (for "latest", "first", "most recent"); the default ranks by match. `query` holds words from the record text, never a tag string.
   - `terms` (one to four nouns every hit must hold) filter; `query` ranks. By default hits are ordered by how rare the `query` words are in a record; a word in the title counts three times, in a tag twice. Records holding the same words tie and keep document order, so the first hit can be any of them: put the distinguishing words in `query` too, choose by `title` and `tags`, and read before relying on one.
   - `group_by` gives the size of each value of one tag key and a `next` call per group: it shows which kinds of record match and how many in one call. Report `candidates` instead of paging to count. Pair it with a tag that ties the records together (copied from a hit), or it groups every record the words touch. Leave out `related_by` unless asked.
   - `valid_at` keeps records in force that day (`at` on or before it, `due` empty or after it). A later date returns what is scheduled; a date before the earliest stored record returns 0 candidates: say older records are not stored, and never present current text as valid on that day.
4. **Read the answer's metadata every time.**
   - `note` with `INDEX_PENDING`, or `coverage.files_pending` > 0: those documents are left out. Say the result is partial; for a lookup, ask again without filters, or later. `E_INDEXING`: nothing indexed yet, same remedy.
   - `mode:"keyword"` with `reason:"WIDE_SCOPE_KEYWORD"`: a filtered wide search ranks by matching words, not meaning. Use the dataset's own words and check `matched` and `gaps`.
   - `omitted` with `PAGE_LIMIT`, or `more`: call `next.tool` with `next.args` exactly; never build a cursor. Page only when the question needs every match (counts, lists, rankings) and say where you stopped.
   - `relevance:"weak"`: the hits are only nearby; say the dataset may not hold the answer.
   - `E_SCOPE_TOO_WIDE`: `as_of` and date fields other than `at`, `due`, `observed` need a small scope; pick a folder from `candidates` or name `documents`.
5. **Open before quoting.** A hit's `summary` is cut short (for row datasets it is only the date). Call `vault_read {refs:[…]}` once with every ref you need (up to 16, `fields:["t","d","at","due"]`). Name the dataset, the ref and the record's date; hub data is a published snapshot, not live.
6. **Conclude carefully.** Never answer "none found" from one empty or odd result: change the words or a tag spelling once. A kind absent from `groups` holds no record with all the `terms`: say so for those words. A result you already have that returned the kind still counts: check it first. Stop when the question is answered; page further only when asked.
7. Hubs are never a write destination. Do not copy hub records into the person's vault unless asked.

## 3. More reads of one document

- Links of one record: `vault_read {refs, relations:{direction:"both"}}`.
- State: `vault_browse {path, summary:true}` gives open records, `rev`, `hash` and `next`; later `vault_changes {path, since: next}` gives only what changed.
- All record ids: `vault_browse {path, ids:true}`, then `vault_read` the ids you need.
- Lists: `vault_browse {path, select:{type, status, tag, from, to}}`.

## 4. Writing, only when the person asks to save

A writable connection is not permission: save only what the person asked, where they asked or where the subject already lives. Never switch to another vault after a refusal.

Every write:

1. Read the records you will change (`vault_read`, `fields:["t","d","x"]`). `set` and `unset` carry `old`, the exact value read (`null` when empty).
2. Send one `vault_edit {path, request_id, operations}` per coherent change, with a new stable `request_id` per intended change. Reuse a `request_id` only to repeat the same call.
3. While the answer says `sync.state: "queued"`, repeat the identical call; it usually settles within 5–20 seconds. The write landed only at `"synced"`: then `vault_read` the changed records.

Records:

- `type` is one of: project goal requirement assumption decision task blocker risk event artifact evidence metric component system service source history knowledge context person agent run. Any other word, `note` included, fails (with a misleading `E_DUPLICATE_FIELD`).
- Every record needs `t`, a short title, never a date or id. Field values are arrays: `{"t":["…"],"d":["…"]}`; `d` is one self-contained sentence of the outcome.
- `x` values: task backlog, open, active, review, blocked, waiting, completed, cancelled, archived; goal open, active, completed, cancelled, archived; project planning, active, at-risk, on-hold, completed, cancelled, archived; decision proposed, accepted, rejected, superseded; risk open, mitigated, resolved, accepted, closed. These five types require `x`. An event requires `at` (ISO date or time; `x` confirmed, tentative, cancelled), a metric `value` and `unit`, an artifact `ref`.
- `id`: lowercase letters, digits and `-` with a type prefix (`task-…`, `dec-…`, `risk-…`); no dots, spaces or dates.
- Links: `[["part-of","prj-x"]]` with part-of, implements, depends, derives, related, supports, contradicts, replaces, blocks, enables. The target must exist or be added earlier in the same call.
- A status change is `set x` with `old`, never a new record. A full replacement sets the old record `superseded` and links `replaces` from the new one. Never remove a record that was true.
- `ref` is one URL others can open, never a local path; `source` holds `source` record ids.

Operations:

- add: `{"op":"add","record":{"type":"task","id":"task-x","fields":{"t":["…"],"x":["open"]},"links":[["part-of","prj-y"]]},"place":{"section":"<existing heading>"}}`. `place` may also be `{"after":"<id>"}` or `"end"`; the section must already exist.
- set: `{"op":"set","entity":"task-x","key":"x","value":"completed","old":"active"}`
- prose: `{"op":"edit-text","entity":"doc","kind":"rich","old":"<exact text occurring once>","new":"…"}`. Never in the same call as an `add` with `place`; add records first, edit prose in the next call.
- New section, or a change to the last section: `edit-text` anchored on the last line of the body, e.g. `"old":"[[last-id]]"`, `"new":"[[last-id]]\n\n# Heading\n\nText"`. The `section` op works only on an existing section that is not the last, with `base_revision` and `expected_hash` from `vault_read {refs:["<path>§<heading>"], source:true}`. A source read of the last section ends with internal `@ids` lines: never copy them into new text.
- remove, and replacing the whole body, need `base_revision` and `expected_hash` (the `rev` and `hash` of `vault_browse {path, summary:true}`).

New document: first check that none on the subject exists. Then `vault_create {path, title, base_revision:null, request_id, content:{text, records}}` with a short ASCII slug filename in an existing writable folder, headings in `text` and each record's `[[id]]` alone on a line where it should appear.

## 5. Errors: fix the cause, never resend unchanged

| code | fix |
|---|---|
| `E_PRECONDITION` | `details.conditions` shows the actual value or the missing anchor: reread, decide, resend with a new `request_id` |
| `E_TEMPLATE` | `details.diagnostics` names the record, field and allowed values |
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

Name the refs you relied on. For a write, give the document path, the new revision and the changed record ids after the readback. Say plainly what was partial: index pending, pages left unread, weak relevance.
