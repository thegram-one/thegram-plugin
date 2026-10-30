# Knowledge model: claims, reasoning, provenance and governance

Read this with [writing.md](writing.md) for every substantial knowledge document. writing.md says how to investigate and write; this file defines what the saved document must make true so a team can rely on it: each material conclusion is a typed claim, its reasons and counterevidence are explicit links, its provenance is pinned, its audience is respected and its currency can be rechecked.

Contents: 1 Claims · 2 Basis · 3 Reasoning links · 4 Pinned provenance · 5 Identity, terms and documents · 6 Governance · 7 Currency and corrections · 8 Document shape · 9 Logic audit · 10 Domain patterns

## 1. Claims are the unit of knowledge

- Every material conclusion the body states, and every fact a reader may need to rely on by itself, is one record: `knowledge` (what is true or how something works), `requirement` (a rule that must hold), `risk` (a loss that may happen), `decision` (a choice and its state), `assumption` (an unverified premise), `goal`, or `metric` for a measured quantity. `system`, `service` and `component` carry responsibilities; claims about how they behave are `knowledge`.
- `t` states the proposition, subject plus assertion ("Core rejects a patch whose base revision is stale"), not a topic label ("Revision check"). `d` adds scope, conditions, exceptions, the version or date it holds for, and how it is known. A reader must be able to use the record outside the document.
- One proposition per record. Split "A and B" when either can change on its own; one proposition stated by several sources is one record citing all of them.
- Explanation stays in prose. A record is not a paragraph pasted into `d`.

## 2. Basis: how each claim is known

Tag every `knowledge`, `requirement`, `risk`, `evidence` and `metric` record with exactly one basis tag:

| Tag | Use when | Requires |
|---|---|---|
| `basis:observed` | this assistant inspected the primary material that establishes it: code or configuration at a pinned revision, a governing document's text, a log, test or measurement it read or ran | `source` on the record |
| `basis:reported` | a source asserts it and its underlying material was not inspected: release notes, a person's message, a paper's stated result, an earlier AI answer | `source`; `d` says who reports it |
| `basis:inferred` | the author's synthesis from other claims | an incoming `supports` link from every premise record; the reasoning and its limits in `d` |

- `decision`, `assumption` and `goal` take no basis tag: their status already says how settled they are, and an assumption is unverified by definition.
- Use `observed` only after reading the primary material. Reading code observes what the code does, not what production does: "the client retries three times" is observed from code; "production retries three times" stays reported or inferred until runtime evidence is inspected.
- When the basis changes, set the whole tag list (`{op:"set", entity, key:"tag", values:[...], old:[...]}`, keeping the other tags) and add the evidence or premise link in the same patch. A disproved claim is never deleted (section 7).

## 3. Reasoning links: make the argument traversable

The body explains the argument; links let `vault_read` with `relations` answer "why do we believe this?" without rereading prose. Use only Core predicates, read "A predicate B":

| Argument step | Link |
|---|---|
| evidence, an observation or a premise claim argues for a conclusion | premise `supports` conclusion |
| evidence or a claim argues against a conclusion | `contradicts` |
| a conclusion holds only while an unverified premise holds | the `assumption` `supports` the conclusion, and the conclusion's `d` names the dependency |
| a mechanism makes a loss possible | the claim or evidence `supports` the `risk` |
| a corrected or newer claim fully replaces an older one | new `replaces` old |
| work or a result carries out a decision or satisfies a requirement | work `implements` it |
| evidence that a risk is gone | evidence `related` risk (never `supports`) |
| a component, service or work relies on another | `depends`, in the sourced direction |
| an output produced from an origin (run, artifact) | output `derives` origin |
| membership | `part-of` the root, goal, system or chapter |

- Every `basis:inferred` claim has `supports` from each premise it uses. A risk has a `supports` link from the claim or evidence that makes it possible whenever that record exists.
- `related` is only for a connection that is neither evidential, structural nor membership. Never use it where `supports`, `contradicts`, `depends` or `implements` applies.
- A `source` record is provenance, not a premise: cite it in the `source` field. The observation drawn from it is an `evidence` or `knowledge` record that `supports` the conclusion.
- Conflicts stay visible: keep both scoped claims, link the counterevidence `contradicts`, and say in prose which holds under which conditions or that it is unresolved. Never average incompatible findings or silently pick one. Copies of one source are one support, not independent confirmation; sequence alone does not prove causation.
- Link only what the reasoning uses. Link density is not a goal; a claim with its sources and no decorative links is better than one with invented connections.

## 4. Pinned provenance

- Each inspected item is one `source` ([writing.md](writing.md) section 3) whose locator still identifies what was read after the material changes: code and configuration by blob URL at the full commit SHA, never a branch; PRs, issues and releases by their URL with the state seen and when; documents by canonical URL plus version or date and section; web pages by URL plus publication and retrieval dates; chats and mail by workspace or account plus thread or message ID. `d` names what was read (files, line ranges, sections) and what was not.
- A claim cites every supporting source in `source`. `ref` on a claim is only for one primary URL a reader should open.
- The overview states the document's knowledge boundary: the commit, release, date or period its conclusions hold for. Version-bound claims repeat it in `d` ("as of v2.5.0").

## 5. Identity, terms and documents

- One entity, one record: reuse sources by identity, claims by proposition, components by responsibility, metrics by definition and unit. Search the document (`vault_browse {path, select}`, `vault_search {query, documents:[path]}`) before adding.
- Define each important term once (a `knowledge` record or a terms section), keep its aliases and original-language term in `d`, then use that term consistently. No synonyms for variety.
- Each subject has one home document. Another document that needs the same entity or claim cites it (`path#id` in prose, or the record's openable Thegram URL in `ref`) instead of redefining or copying it; links stay within a document.
- When the person asks for an overview of several subjects (an organization, a product line, a portfolio), write one map document that explains how the subjects relate and cites each home document. It summarizes with citations and holds no duplicate claims.

## 6. Governance: destination, audience, ownership and review

- The destination follows SKILL.md section 1 and [person-vault.md](person-vault.md). A team or shared vault is used only when the person names it or an established document lives there.
- Everyone who can read the destination can read every record and revision. Write what that audience may see: claims at the abstraction the purpose needs, sources cited by neutral title and identifier. Material with a narrower audience (direct messages, private mail, personal conversations, restricted repositories) enters a shared vault only when the request makes that disclosure intended; otherwise leave the claim out and record a nonsensitive coverage gap. Never store secrets, credentials, tokens in URLs, third parties' personal data or raw message bodies.
- Never invent owners, stewards, approvers or due dates; set `owner` only as a source or the person states it.
- An AI recommendation stays a `proposed` decision until the person, or a source recording someone's acceptance, accepts it. An inferred claim stays `basis:inferred` until evidence is inspected.
- Comments are reviewer input. When `vault_comments` is exposed, read the open threads before rewriting the sections they anchor, investigate them like any lead and keep them coherent with the new text. Open a thread with `vault_comment` only when the person asks for review requests, and never resolve a thread someone else opened.
- Corrections keep the audit trail (section 7). Never remove a once-true record or rewrite prose to hide an earlier mistake.

## 7. Currency, corrections and re-verification

- On every update, compare each scope source's watermark (writing.md section 6) with the current source. Where material that claims rely on has changed, re-inspect it and re-verify those claims: find them through the source IDs in their `source` fields, then update `d`, basis and links.
- A finding that narrows a claim rewrites its `d` and links the new evidence. A finding that makes it false adds the corrected claim with `replaces` to the old one, links the evidence `contradicts` the old one, and rewrites the old `d` to say what superseded it and why. Decisions also move to `superseded`.
- Time-bound claims (current version, latest release, present CI state) carry their as-of date or version and are rewritten, not appended to, when the state changes.
- A source that can no longer be read does not erase its claims: state the gap and keep the basis established when it was inspected.

## 8. Document shape

Organize by the subject's questions, not by source, connector or date. Adapt this order to the subject:

1. Overview: purpose, scope, knowledge boundary and the current judgment in a few sentences that mention the key claims.
2. Terms and concepts the rest relies on.
3. One section per major question or mechanism: the conclusion first, then reasons and evidence, conditions and exceptions, alternatives and tradeoffs.
4. Evidence and verification boundaries: what was observed, what is reported, what was inferred, and which checks were not done.
5. Evolution, only where history explains the present.
6. Risks, contradictions and open questions, each tied to its claims.
7. Scope, coverage and update criteria: what was inspected, the gaps, and which change should trigger the next update.

Each conclusion in prose mentions its record (`[label](@id)`) where the reader meets it; place a record card only where it helps. Prose and links must tell the same argument.

## 9. Logic audit before reporting completion

Run this on the saved document (records with `vault_browse {path, select}` and `vault_read`, edges with `vault_read {refs, relations}`) together with writing.md section 7, and fix failures before reporting:

1. Each conclusion in the body has a claim record mentioned in place; each claim is reachable from the root and explained or placed in the body.
2. Each claim-bearing record has exactly one basis tag; observed and reported claims cite sources; inferred claims have `supports` from their premises.
3. Each source a claim relies on has a pinned locator.
4. Known counterevidence and open questions appear in both prose and records.
5. Decisions are `accepted` only on recorded acceptance; each risk names its mechanism.
6. One source per locator and one claim per proposition.
7. A natural "why" question finds the claim through `vault_search`, and `vault_read` with `relations` shows its supporting chain.

Report the audit in a sentence or two (claims, basis mix, reasoning links, open contradictions and questions). Never add links or tags only to pass it.

## 10. Domain patterns

- Engineering: purpose, components and their contracts, guarantees and their limits, meaningful changes and unresolved work. Commits are provenance; code observations are not runtime observations.
- Operations: current risks, incident boundaries, thresholds and decisions with the evidence that verifies each; a changed threshold `replaces` the earlier choice.
- Research: attribution, disagreement and dates; evidence `supports` or `contradicts` scoped claims.
- Plans: accepted and replaced choices and remaining steps; acceptance is not implementation, which needs its own evidence.
- Metrics: quantity, denominator, unit, observation window and source; one metric ID per definition; disclose incomparable windows.
