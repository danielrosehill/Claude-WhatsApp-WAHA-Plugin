---
name: leave-or-archive
description: Archive, unarchive or leave WhatsApp chats and groups on your own account, one at a time or as a bulk tidy of dead groups. Archive is silent and reversible; leaving is visible to the group. Use on "archive the <group>", "get me out of <group>", "leave that group", "unarchive X", "tidy up my WhatsApp groups". Not for creating groups or changing members (create-group).
---

# Archive, unarchive or leave

Read the account profile first: [`references/profile.md`](../../references/profile.md).
Raw calls are `<gateway_server>__waha_post` / `waha_get`; `{session}` is the profile's
session. Resolve the chat by alias, `list_chats`, or `waha_get /api/{session}/groups`
(large: save to a file and parse). More than one match → ask.

| The user says | Do | Others see it? |
|---|---|---|
| archive, hide, tidy | Archive | No |
| leave, exit, get me out | Leave, then archive | Yes ("<owner> left") |
| mute | Needs PUT/unsupported on Core: archive instead and say so | |

A request naming one chat is the approval. A bulk leave gets the list shown first: leaving
can't be undone without someone re-adding the owner. If the owner is the only admin of a
group they're leaving, say so first (WhatsApp promotes someone at random).

## Calls (POST, no body)

| Do | Path |
|---|---|
| Archive | `/api/{session}/chats/<chatId>/archive` |
| Unarchive | `/api/{session}/chats/<chatId>/unarchive` |
| Leave | `/api/{session}/groups/<gid>/leave` |

They return `null`; that is not proof. Verify archive with
`waha_get /api/{session}/chats/overview?limit=1&ids=<chatId>` → `_chat.archived` (the reply
also carries the whole last message: read only that field). Verify leave with the group's
participant list no longer containing the owner's number.

## Bulk tidy

1. `waha_get /api/{session}/chats/overview?limit=200` to a file; with a script, list groups
   (`@g.us`) whose last message is older than N days (default 90).
2. Show name, last activity and proposed action.
3. Apply what the user confirms, one call per chat with a short pause, verifying each.

---
Verified 2026-11-17 (WAHA 2026.10.1 Core, NOWEB): archive then unarchive of a 1:1 chat,
`_chat.archived` flipped to `true` and back. Leave not yet exercised live.
