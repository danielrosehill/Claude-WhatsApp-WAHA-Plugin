---
name: whatsapp-health
description: Check that a WAHA-linked WhatsApp account is healthy and not being throttled: session state, WhatsApp's message capping and new-contact reach-out lock, WAHA version, container uptime and restarts, live reads. Read-only. Use on "is WhatsApp working", "check the WhatsApp connection", "why didn't my WhatsApp send", or before a batch of sends.
---

# WhatsApp health check

Read the account profile first: [`references/profile.md`](../../references/profile.md).
Read-only. Run every row, report a short table and anything that needs the user.

| Check | How | Healthy |
|---|---|---|
| Session | `session_status` | `WORKING`, `me.id` = profile `own_number@c.us` |
| Capping | same call, `me.messageCapping` | `cappingStatus: NONE`; `usedQuota` well under `totalQuota` (`-1` = no cap) |
| Reach-out lock | same call, `me.reachoutTimelock` | `null`. Otherwise WhatsApp is throttling messages to new numbers: stop new-number sends and group creation |
| Version | `waha_get /api/server/version` | Note version, tier, engine; a jump means an auto-updated `latest` image |
| Containers | if the agent has a shell on the host: `docker ps` status and `docker inspect -f '{{.RestartCount}}'` for the WAHA container | Up, restart count stable |
| Live reads | `list_chats limit=3`, `get_messages` on the `me` alias | Succeed; newest activity recent |

A real send (to the owner's own number) only if the user asks for a full test.

`SCAN_QR_CODE` / `FAILED`: the linked device dropped. The owner re-pairs from the
profile's `dashboard` (phone: WhatsApp → Settings → Linked Devices → Link a Device).
Don't retry sends. `STARTING` for minutes: restarting the WAHA container is safe when the
session store is persisted.

WAHA Core runs **one session per container**: a second account needs a second container
and its own gateway server, not a second session.

Keep a dated baseline in the profile's prose so the next check can compare.
