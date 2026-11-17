# Account profile

Every skill in this plugin acts for one WhatsApp account, described by a **profile**.
The plugin itself holds no names, numbers or chat ids: those live in the user's profile,
outside the plugin.

## Where profiles live

Look in this order and stop at the first that exists:

1. `$WHATSAPP_WAHA_HOME/profiles/`
2. `$CLAUDE_USER_DATA/whatsapp-waha/profiles/`
3. `~/.claude-user-data/whatsapp-waha/profiles/`
4. A memory/context record titled `whatsapp-waha profile <owner>` in whatever knowledge
   store the agent has (for hosted clients with no local files).

One file per account (`<owner>.md`). Pick the account the user names ("from my partner's
WhatsApp"), else the one with `default: true`, else the only one. None found: ask the
user for `gateway_server` and `owner`, run the task, then offer to save the profile.

## Format

YAML frontmatter, optional prose below it for anything the skills should know.

```yaml
---
owner: Alex               # whose account; messages go out as this person
own_number: "15551234567" # E.164 without +, for checks and "message myself"
default: true
gateway_server: whatsapp  # MCP server / tool prefix: whatsapp__send_chat_message, …
session: default          # WAHA session name (Core tier: always "default")
country_code: "1"         # assumed for local numbers with a leading 0
aliases:                  # short names the user says → chat ids (optional)
  me: "15551234567@c.us"
contact_book: none        # how to save a contact: a skill name, an MCP tool prefix, or none
staging: none             # how to get a public URL for an image/file (skill or script), or none
placeholder_names: {male: Sam, female: Sam}  # name to save an unknown first contact under
log: none                 # where to append a record of each send (path or none)
dashboard: http://<host>:<port>/dashboard    # where to re-pair when the link drops
---
```

## Tools

With `gateway_server: whatsapp` the tools are `whatsapp__session_status`,
`whatsapp__send_chat_message`, `whatsapp__get_messages`, `whatsapp__list_chats`,
`whatsapp__search_contacts`, `whatsapp__check_number`, `whatsapp__mark_seen`,
`whatsapp__priority_chats`, and the raw escape hatches `whatsapp__waha_get`,
`whatsapp__waha_post` (plus `waha_put` / `waha_delete` where the server has them).
Substitute the profile's `gateway_server` for `whatsapp`. They may be deferred: load them
in one batch, or call them through a toolbox `call_tool` if the client uses one.

The raw calls take WAHA REST paths. Where a path contains `{session}` below, use the
profile's `session`.
