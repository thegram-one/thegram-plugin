---
name: thegram-cloud
description: Use for any question about the person's notes, decisions, tasks, documents or shared GramHub data, and whenever the person asks to save, record or change something in Thegram. Five tools; follow the rules exactly.
---

# Thegram rules

Tools: `vault_ask` (read; `op` `ask` default, `browse`, `changes`, `comments`, `guide`), `vault_read`, `vault_add` (`op` `create`, `comment`, `restore`, `feedback`), `vault_change` (`op` `edit`, `move`, `delete`), `vault_exact`.

## Read

1. ALWAYS start with `vault_ask {question}`: the person's words, with exact names, codes and numbers. Add `terms` (1–4 nouns) when you know them. Do NOT browse first.
2. A filter (type, status, date, tag) goes ONLY in `plan.conditions`, each with `quote` = the question's words it came from. NEVER guess a value. If `interpretation.unresolved` comes back, fix it from `allowed` or ask the person.
3. Answer from `records` and `hits`. Call `vault_read` ONLY for what they do not show. NEVER quote a ref you did not get from a tool.
4. If the answer has `next`, call `next.tool` with `next.args` EXACTLY, even if that tool is not listed. NEVER build a cursor.
5. Shared hubs (`@mounts/hub-team-*`) are searched ONLY when you pass their ref as `scope`. Get refs from `vault_ask {op:"browse"}`. NEVER walk folders.
6. Counts, totals, averages, maxima, top N: ONLY `vault_exact {query, folder}`. Accept ONLY `execution.status:"complete"`. Otherwise say there is no exact answer. NEVER count hits.
7. If the answer shows `relevance:"weak"`, `omitted`, `coverage.files_pending`>0 or `interpretation.exhaustive`, SAY the result is partial or approximate.
8. Cite every claim as `path#id`. Answer in the person's language.

## Write

9. Write ONLY when the person explicitly asks to save, record, change, move or delete. A writable connection is NOT permission.
10. Before the FIRST write in a conversation, call `vault_ask {op:"guide", task:"memory"}` and follow it.
11. Add to the existing document on the subject; create (`vault_add {op:"create"}`) only when none exists. NEVER write to a hub. NEVER switch vault after a refusal.
12. One record per fact, decision, task or risk. `type` MUST be one of the 22 below. `t` is a short title; `d` one sentence stating the outcome.
13. Before changing a record, `vault_read` it. Change status with `set x` and `old` = the value you read. NEVER delete a record that was true; mark it `superseded`.
14. Every write carries a new `request_id`. If the answer says `sync.state:"queued"`, resend the IDENTICAL call until `"synced"`. Then `vault_read` the result and report path, revision and record ids.
15. On an error, read `details` and fix the cause. NEVER resend an unchanged call. `AGENT_SCOPE_DENIED`, `E_READ_ONLY`, `E_FORBIDDEN`: stop and tell the person.

## The 22 types

`knowledge` fact or report · `evidence` proof for a named claim · `context` background · `history` change over time · `assumption` unverified premise · `source` citation · `decision` explicit choice · `requirement` duty or criterion · `goal` target state or number · `task` assigned work · `project` named undertaking · `blocker` current obstacle · `run` one execution · `risk` possible future loss · `metric` measured number with unit · `event` dated occurrence · `person` · `agent` · `system` · `service` · `component` · `artifact` existing output with `ref`.
