---
name: create-group
description: Create a WhatsApp group from your own account with existing contacts, new numbers or both, post an opening message and send the invite link to anyone who couldn't be added. Also add or remove members, promote admins, rename (where supported) and get a group's invite link. Use on "make a WhatsApp group with X and Y", "start a group for <topic>", "add Z to the <group>", "remove Z", "get the invite link". Not for leaving or archiving (leave-or-archive).
---

# Create or change a group

Read the account profile first: [`references/profile.md`](../../references/profile.md).
Raw calls are `<gateway_server>__waha_post` / `waha_get`; `{session}` is the profile's
session. Asking for the group is the approval. Membership is visible to everyone in it,
so each participant must be certain: resolve as in send-message, ask about any name with
several matches or none.

## 1. Participants

Each is `<digits>@c.us`; local `0…` numbers get the profile's `country_code`. Run
`check_number` on any new number and drop (and report) those not on WhatsApp. The owner
is added automatically as admin.

Adding many people who don't have the owner's number looks like spam to WhatsApp. For
strangers, prefer a 1:1 invite link with an intro (message-new-number) to adding them,
and keep new numbers per creation small (under ~10).

## 2. Create

```json
{"path": "/api/{session}/groups",
 "body": {"name": "<group name>", "participants": [{"id": "<n>@c.us"}]}}
```

Take the group id (`…@g.us`) from the response. Read back
`waha_get /api/{session}/groups/<gid>/participants` and compare: someone whose privacy
settings block being added will be missing; send them the link (step 4).

## 3. Opening message

Send the user's opening text (or a one-line hello saying what the group is for) with
send-message to the group id, and verify.

## 4. Invite link

`waha_get /api/{session}/groups/<gid>/invite-code` → `https://chat.whatsapp.com/<code>`.

## 5. Change an existing group

| Do | Method and path | Body |
|---|---|---|
| Add | POST `/api/{session}/groups/<gid>/participants/add` | `{"participants": [{"id": "…@c.us"}]}` |
| Remove | POST `…/participants/remove` | same |
| Promote / demote admin | POST `…/admin/promote`, `…/admin/demote` | same |
| Rename | PUT `/api/{session}/groups/<gid>/subject` | `{"subject": "…"}` |
| Description | PUT `…/description` | `{"description": "…"}` |

The owner must be admin. PUT needs a `waha_put` tool on the server; without one, tell the
user to do it from the phone. Read the participant list back after every change and
name removed people in the report.

## Report

Group name and id, who's in, who got the link instead, first words of the opening message.

---
Endpoints per WAHA 2026.10 (Core, NOWEB) documentation. Creation and participant changes
had not been exercised live when this was written (2026-11-17): the first real run
should confirm them and update this line.
