# Scheduled automations and agent work

Read this for a scheduled run that writes a document (a research digest, a monitor, a collected log) or for work an agent does for the person. Their substance follows [writing.md](writing.md) and [knowledge-model.md](knowledge-model.md); this file fixes the record shapes the dashboards read.

## Scheduled automations

A document a scheduled run writes (a research digest, a monitor, a collected log) has one shape, and the 리서치, 감시·알림 and 수집 일지 dashboards read exactly it. Write each run as one patch:

1. Read what earlier runs left (`vault_browse {path, select: {type}}`, `vault_search` by URL or title). An item already recorded (same URL, release, notice or message) is not added again: `set` only the fields that changed on the existing record and leave its links alone, so it keeps pointing at the run that first found it.
2. Add one run record: `event`, `t` such as "리서치 실행", `at` the actual run time with its zone, `x` `confirmed`, `tag` `run` plus `failed:<source>` for every source that could not be read, `d` saying what was checked and how many new items, `part-of` the project. A run that finds nothing still records itself.
3. Each new item: `knowledge` for a news item, post or finding; `event` for something that happened or is scheduled (`at`, and `end` when the source gives one). Give it `ref` (the original URL), `at` (when it was published or happens; leave it out when the source gives no time), `tag` `source:<name>` (`news`, `hn`, `github`, `reddit`, `gmail`, `slack`, `calendar`, …) and `topic:<slug>` (one slug per story, the same on every source that covers it), link `derives` to this run only, and place it. Its ID comes from the item (`k-hn-41234567`, `ev-sui-testnet-v1-82-1`), never from the run time.
4. Each watched thing (a chain, server, repository or mailbox) is one record tagged `target`, created once and reused; every detection links `related` to its target.
5. What needs a person follows the automation's own rule in the root project's `d`. A change still ahead that someone must act on (an upgrade to apply, a price change to review) becomes a `task` due by its start; a possible harm nobody here can act on (a reboot during a provider's maintenance) is a `risk`, with `p` only when stated. A task is never created already overdue. Link either `related` to its item. Status belongs in `x` and dates, never in a title (`[완료]`, `[과거]`).
6. The schedule itself (cadence, sources, scope) is the root project's `d`; the dashboard reads the cadence from the runs, so never invent a run that did not happen.

## Agent work

When an agent works for the person (code, a release, an investigation), the AI 작업 보드 dashboard shows who is doing what and how it went from exactly these records:

1. One `task` per concrete piece of delegated work, added when it is planned or started and reused when it continues (find it by title, `ref` or repository with `vault_search`). `t` names the work, not a tool call; `owner` is your agent name as the person knows it (`Codex`, `Claude Code`, `ChatGPT`); `s` the repository when applicable; `start` when you actually began; `part-of` the project or goal. Substantial research requires the question/coverage tasks in writing.md, tagged `research-work`, with acceptance criteria in `d`. Never a task per commit or per session.
2. Move `x` with the work, in the same patch as the step that moved it: `open` while queued; `active` while you work; `review` when the result waits for the person's review or approval (`d` says what to look at); `waiting` when you need the person's answer (`d` asks the question; a choice between options is also a `proposed` decision linked `related`); `blocked` when something else stops it (`d` says what); `completed` only after its acceptance is verified, with `done` the finish time and `d` rewritten to the outcome; `cancelled` with the reason. Before stopping, reconcile active tasks with reality: unfinished queued work returns to `open` with its next action, not a fictional blocker or completion. A saved result or finished run alone does not complete research.
3. Each meaningful step is one `event` linked `part-of` the task: a PR opened for review, merged, a release, a deployment, a verification, or a plan the person agreed to. `at` is when it happened and `ref` the PR, commit, release or run URL. No event per commit, push or test run, and none for a step that did not happen.
4. A choice settled along the way is a decision ([record-meanings.md](record-meanings.md)); the step that carries it out may also link `implements` it.
