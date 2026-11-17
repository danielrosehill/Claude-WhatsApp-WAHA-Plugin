# WhatsApp via WAHA

Agent skills for driving **your own personal WhatsApp account** through a self-hosted
[WAHA](https://waha.devlike.pro/) linked-device session exposed as an MCP server.
Account-neutral: names, numbers, aliases and routes live in a per-user **profile**
outside the plugin ([`references/profile.md`](references/profile.md)), so the same skills
serve several people's accounts, each with their own WAHA container and MCP server.

| Skill | Does |
|---|---|
| `send-message` | Text, quoted replies, photos and files to existing contacts and groups; read a chat; verify delivery by `ack`. |
| `message-new-number` | First message to a number you've never messaged: normalise local numbers, check it's on WhatsApp, intro line, text + photos, then save the contact (placeholder name if unknown). |
| `create-group` | New group with contacts and/or new numbers, opening message, invite link for anyone who couldn't be added; add/remove/promote members. |
| `leave-or-archive` | Archive, unarchive or leave chats and groups; bulk tidy of dead groups with confirmation. |
| `whatsapp-health` | Session state, WhatsApp message capping and reach-out lock, version, container restarts, live reads. |

## Requirements

- A WAHA instance (tested: 2026.10.1, Core tier, NOWEB engine) paired with your phone.
  Core runs one session per container: one container per account.
- An MCP server in front of it providing `session_status`, `send_chat_message`,
  `get_messages`, `list_chats`, `search_contacts`, `check_number`, `mark_seen`,
  `priority_chats`, `waha_get`, `waha_post` (optionally `waha_put`, `waha_delete`).
  Its name is the tool prefix you put in the profile's `gateway_server`.
- Optional: a contacts tool (e.g. a Google Contacts MCP) for saving new contacts, and a
  way to host a file at a temporary public URL for sending media.

## Setup

Create `~/.claude-user-data/whatsapp-waha/profiles/<you>.md` from the template in
[`references/profile.md`](references/profile.md). Several profiles → the skills pick the
account you name, else the `default: true` one.

## Caution

WAHA is an unofficial WhatsApp client; WhatsApp's terms don't allow it and accounts can be
banned, mostly for bulk or stranger messaging. The skills keep to one-at-a-time personal
use and check WhatsApp's own throttling signals before risky sends.

## History

Extracted 2026-11-17 from a private personal-WhatsApp plugin (send, new-number, group and
health skills written and live-tested there the same day), depersonalised so a second
household member could use the same skills with their own account.

License: MIT.
