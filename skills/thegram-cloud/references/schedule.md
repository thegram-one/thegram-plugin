# Schedule-specific meaning

Use [the common writing method](writing.md) to create and maintain the person's explicitly requested schedule in one `schedule.gram`. This reference supplies schedule semantics, not a separate import pipeline. Never create other documents as a side effect.

## Scope

- Reuse the established schedule path; otherwise resolve [the person root](person-vault.md). Infer the time zone from context; for a Korean-speaking person without another signal, use `Asia/Seoul`, and retain explicit source zones.
- Read relevant connected calendars, mail, chats, repositories and trackers: events, invitations, commitments, mentions, review requests, assigned issues and milestones. Respect the requested period. Without one, inspect the next 30 days and current open commitments; extend into earlier bounded windows when needed to establish their state. A requested historical schedule is reconstructed in bounded chronological windows.
- State what needs attention: a sourced deadline within 3 days, a request waiting on the person for 2 days or more, confirmed overlapping events, or tomorrow's meeting with no agenda. Keep this rule in the root's `d`; missing access is not proof that an agenda or completion does not exist.
- No recurring automation until the person requests it and the host supports it. Do not promise background updates merely because this document exists.

## Classify and reconcile

| In the sources | Record or update |
|---|---|
| an actual meeting, trip, launch, interview or other scheduled occurrence | `event`: sourced `at` and exclusive `end`, `location` when known, `x` `confirmed` or `tentative`; all-day items stay date-only |
| something the person must do by a time | `task`, sourced `owner` and `due`, `x` `open`; `completed` with `done` only when supported |
| something another person owes them | `task` with that owner and `x` `waiting`, or a `blocker` that actually blocks their work |
| an agreed change of time or place | `set at`/`end`/`location` on the same event with `old`; a separate accepted `decision` only when an explicit choice was made |
| a cancellation | `set x cancelled` and rewrite `d` to the outcome; retain the record |
| confirmed overlap or a supported deadline conflict | `risk` linked `related` to affected events/tasks; no invented priority |
| a recurring series | one `knowledge` record for the rule and one `event` per in-scope occurrence, each linked to the series |
| a meeting's documented outcome | the appropriate knowledge, decision, task or evidence with its source; attendance or a calendar invitation does not prove the outcome |

Use writing.md's source identities. Calendar occurrences, reschedules and cancellations retain their identity; match duplicates found in invitations or chat before adding. Keep task deadlines on tasks rather than manufacturing an event for every due date. Sources and their facts are saved together, and the current explanation and upcoming sections are updated rather than appended as a monthly import log.

## Runs and verification

The common method's coverage/version checkpoints also apply here. Read earlier windows, open work and changed source items rather than reimporting every past occurrence. Known future events are recorded at the current knowledge time, not with a future effective boundary. Past occurrences remain history through their sourced `at`; do not rewrite them unless corrected by a source.

For the existing automation dashboard contract ([automations.md](automations.md)), a schedule run is an `event` tagged `run`, not an additional `run`-type record: ID `ev-run-<stable run key>`, actual `at`, `x:confirmed`, `part-of` the root, and `d` with coverage, new/changed/cancelled items, gaps and next work. Checkpoint the same run while it progresses. New domain items `derives` this run; known items retain their original run link. Each watched source is a reused `source` tagged `target`, with detections linked `related`. Use `failed:<connector>` only for an actual failed read.

Read back changed events and tasks, check upcoming occurrences and sourced deadlines, and perform the common writing method's coverage/retrieval checks. For deliberate historical reconstruction, verify earlier/later states too. Report the one schedule path, the next 7 days, what needs attention, changes, gaps and unfinished scope.
