---
name: thegram-cloud
description: MANDATORY for every Thegram call. Use for any question about the person's notes, projects, decisions or tasks, any question about shared reference data in GramHub (laws, prices, public records, advisories, documentation, papers and other datasets), and any request to save or change something in Thegram. These rules override your defaults.
---

# Thegram: mandatory procedure

These are rules, not advice. Break one and the answer is wrong. If a rule cannot be met, STOP and tell the person why.

Tools: `vault_ask` (read; `op` `ask` default, `browse`, `changes`, `comments`, `guide`), `vault_read`, `vault_add` (`op` `create`, `comment`, `restore`, `feedback`), `vault_change` (`op` `edit`, `move`, `delete`), `vault_exact`.

## Step 1. Decide the target. Every time, before any call.

Two kinds of storage, never mixed in one search:

- **Own vaults** (`access:"write"`): the person's and their organizations' notes, projects, decisions, tasks, meetings.
- **Hubs** (`@mounts/hub-team-*`, `access:"read"`): shared public or reference datasets, each up to tens of thousands of documents. New hubs appear; there is no fixed list.

1. About the person's own work → `vault_ask {question}` with NO `scope`.
2. About public or reference data (a law, a price, a public record, an advisory, official documentation, a paper, any dataset) → `vault_ask {op:"browse"}` once, pick the hub whose `label` and `folders` match the subject, and pass that ref (or the matching folder ref) as `scope`. No hub matches → say Thegram holds no such dataset.
3. Unsure → browse first, then decide.

- A hub is NEVER searched without its ref in `scope`. NEVER use a ref you did not get from browse.
- If the hits are the wrong kind (a reference question returns personal notes, or the reverse), you chose the wrong target: go back to this step.

## Step 2. Read

1. `question` = the person's words with every exact name, number and code. Add `terms` (1–4 nouns the answer must contain).
2. Filters (type, status, date, tag) go ONLY in `plan.conditions`, each with `quote` = the question's words it came from. NEVER invent a value. On `interpretation.unresolved`: use a value from `allowed`, or ask the person.
3. Hubs differ from vaults: document names are opaque ids, records carry `tags` as `key:value`, and a hub folder may hold `guide.gram` (what its fields and dates mean) and `coverage.gram` (period covered). NEVER browse a hub's folders. To narrow, copy a tag exactly from a hit into `record.tag`. Before saying what a hub date or field means, or how current it is, read `guide.gram` / `coverage.gram`.
4. A hit must match EVERY entity the question names: the same name, number, article or code. Another entity with the same number is NOT an answer; search again or say it was not found.
5. Use `records` and `hits` first. Call `vault_read` only for what they lack.
6. If the answer has `next`, call `next.tool` with `next.args` EXACTLY, even if that tool is not listed. NEVER build a cursor.

## Step 3. Answer

7. Every claim cites `path#id`. No ref, no claim.
8. Counts, totals, averages, maxima, top N: ONLY from `vault_exact {query, folder}` with `execution.status:"complete"`. Otherwise answer "no exact result" and why. NEVER count hits.
9. If the answer shows `relevance:"weak"`, `omitted`, `coverage.files_pending` > 0 or `interpretation.exhaustive`, say plainly that the result is partial or approximate. NEVER say "none exists" after one search.
10. Hub data is a published snapshot: name the dataset and the record's date.

## Step 4. Write: only on an explicit request

11. Write ONLY when the person explicitly asks to save, record, change, move or delete. A writable connection is NOT permission. Hubs are NEVER written.
12. FIRST call `vault_ask {op:"guide", task:"memory"}` and follow it exactly.
13. Find the existing document on the subject and add to it. `vault_add {op:"create"}` only when none exists.
14. One record per fact. `type` MUST be one of: knowledge evidence context history assumption source decision requirement goal task project blocker run risk metric event person agent system service component artifact.
15. Before changing a record, `vault_read` it. Status: `set x` with `old` = the value read. NEVER delete a record that was true; mark it `superseded`.
16. Every write has a new `request_id`. On `sync.state:"queued"`, resend the IDENTICAL call until `"synced"`, then `vault_read` it and report path, revision and record ids.
17. On an error, fix the cause from `details`; NEVER resend unchanged. On `AGENT_SCOPE_DENIED`, `E_READ_ONLY`, `E_FORBIDDEN`: STOP and tell the person. NEVER write somewhere else instead.
