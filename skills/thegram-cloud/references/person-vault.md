# The person root

Personal writing without an explicitly established document uses one root in the person's own storage, never a team vault unless the person names it or continues a previously authorized root there. Resolve that destination before any write.

## The name

Use the name the person goes by on this host: the account or profile name you know, or the name the person uses for themselves. Ask only when you know none of them, and keep working on everything that does not need the root while you wait. Keep the characters exactly (trim surrounding spaces, Unicode NFC, keep case, spaces and script). Reuse the name and destination evidenced by existing subject documents, their root records or earlier run records; do not create a record or document just to store the name.

## The root

1. Call `gram_projects`. It lists every vault this assistant may write, personal and team alike, without saying which is which, so only the vaults below count as personal.
2. The vault the person names in this request ("put it in Team Research") → a folder named after the person in it, `@mounts/<vault-id>/<name>`, reused when it exists. Naming a vault is the only way a team or shared vault is chosen. An established document for the same subject elsewhere in that vault is continued at its own path instead.
3. Otherwise continue an existing personal root: a folder named after the person, or an exact-name vault, whose existing subject documents, owner record or earlier run destination establishes it as the person's root. A previously authorized root in a named shared vault remains valid. If several roots match, prefer the one containing the established document for this subject; otherwise use the personal-vault rules below rather than an unrelated root. Never select an arbitrary team vault merely because it contains a similarly named folder.
4. A writable vault whose `name` equals the person's name (same trim and NFC, character-exact) → the root is that vault: `@mounts/<vault-id>`.
5. Otherwise the person's own vault `Thegram`: Thegram creates it for a person the first time an assistant connects and gives that assistant write access, so a first-time user always has it. The root is a folder named after the person in it, `@mounts/<vault-id>/<name>`; creating the first document under it creates the folder. Tell the person their storage is ready and where.
6. Every other vault (team, organization, shared, or of unknown owner) is never chosen by itself. When neither step 4 nor step 5 has a writable vault, write nothing and tell the person in one message: create a vault named `Thegram` or with their exact name in Thegram and allow this assistant to write it, or name a vault to use. Keep doing the reading and inventory that needs no root meanwhile.
7. Several exact vault matches, or a match that is read-only: use step 5 and say why.
8. A denial (`AGENT_SCOPE_DENIED`, `E_READ_ONLY`, `E_FORBIDDEN`): stop and tell the person to allow this assistant to write that vault in Thegram Settings → Connected agents.

Assistants cannot create vaults themselves; the server refuses it.

## One document per purpose

An explicitly named or established document keeps its exact path. Otherwise inspect the resolved root and its folders with `gram_list` and relevant recall before creating anything; a moved, renamed or older-format document is continued, not recreated.

| Path | Use |
|---|---|
| `<subject-slug>.gram` | a new subject document; its own sources, facts, explanation and run/coverage checkpoint |
| existing `projects/<slug>.gram` or another subject path | continue unchanged at that path; no migration required |
| `schedule.gram` | an explicitly requested personal schedule, reused when present |

New path slugs and record IDs use lower-case ASCII; reuse the exact existing path even when it follows a different convention. Every title, heading, description and sentence a person reads is in the person's language. Type names, field keys, status values, predicates and tag keys stay as the skill writes them. Do not rename or delete existing files merely to match this layout.

## Authorization and privacy

Asking to save a subject authorizes its scoped records under the resolved root, including the folder created by that first document. It does not authorize another document, unrelated sources or copies of raw content.

- Store structured, source-backed knowledge within the requested purpose: never transcripts, message bodies, attachments, credentials, government or account numbers, or unrelated third-party private details.
- Subjects the person excluded stay out of records and coverage titles as well as prose; note only a nonsensitive scope exclusion when needed.
- Keep overviews and summary sections of personal documents free of sensitive detail; saving a document does not authorize sharing it.
