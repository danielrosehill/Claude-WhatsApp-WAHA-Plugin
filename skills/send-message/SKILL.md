---
name: send-message
description: Send a WhatsApp message, photo or file from your own personal account (linked through WAHA), or read a recent chat. Use on "WhatsApp <person>", "send this to the <group>", "send her the screenshot", "reply to that message", "what did <person> last say". Works on existing contacts and groups; for someone never messaged before use message-new-number.
---

# Send a WhatsApp message

Read the account profile first: [`references/profile.md`](../../references/profile.md).
Everything sent goes out **as the profile's owner, from their own number**.

## Who

Resolve the recipient in this order: profile `aliases`, then `search_contacts`, then
`list_chats` for groups. Several matches or none → one question about *who*. Never guess
a number. A local number with a leading `0` gets the profile's `country_code`
(`0524080050` with `972` → `972524080050`).

## Approval

The request is the approval: send, then report. Stop for review only when the user says
"show me first" / "draft only". When answering a specific message, reply to it
(`reply_to` = its message id) so it shows as a quoted reply.

## Text

`send_chat_message` with `chat` (alias, number or chat id), `text`, optional `reply_to`.
WhatsApp style: short, plain text, `*bold*` and `_italic_` render, Markdown headings and
tables don't. Write in the owner's voice, first person, no AI signature unless asked.

## Photos and files

`send_chat_message` is text only. For media:

1. Get the file on disk (a pasted image is saved by the client somewhere temporary: find
   it, copy to a plain name).
2. Get a public URL for it with the profile's `staging` route (the WAHA server fetches it;
   it can't read local paths). No staging route → tell the user and stop.
3. `waha_post`:
   ```json
   {"path": "/api/sendImage",
    "body": {"session": "<session>", "chatId": "<real chat id, not an alias>",
             "file": {"mimetype": "image/png", "filename": "x.png", "url": "<url>"},
             "caption": "<optional>", "reply_to": "<optional message id>"}}
   ```
   `/api/sendFile` with the same body for PDFs and other documents.
4. Remove the staged copy and the temp file.

If an order, list or instruction is in the images, spell it out in a text message too.

## Verify

Read the chat back (`send_chat_message` returns the newest messages; for media use
`get_messages limit=3`). Yours must be there with `fromMe: true`. `ack`: `SERVER` sent,
`DEVICE` delivered, `READ` read; `PENDING` right after a group send is normal. A 200 alone
is not delivery. Report one line: recipient and first words. Append to the profile's `log`
if it has one.

## Ban risk

WAHA is an unofficial client. Keep it to normal personal use: no broadcasts, no loops
sending the same text to many chats, no scheduled bulk sends.
