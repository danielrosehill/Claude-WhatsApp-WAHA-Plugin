---
name: message-new-number
description: First WhatsApp message to a phone number you've never messaged, then save the person to your contact book, even when you don't know their name yet. Normalises local numbers, checks the number is on WhatsApp, adds an intro line, sends text and photos, verifies, saves the contact under a placeholder name if needed. Use on "WhatsApp this number", "message 05X… and save him", "send this to <number> and add her to my contacts". One number at a time.
---

# Message a new number, then save them

Read the account profile first: [`references/profile.md`](../../references/profile.md).
The number plus the message is the approval for both the send and the save.

## 1. Number

- Leading `0` → local: drop it, prefix the profile's `country_code`, strip spaces and
  dashes. `+` → strip it. Mobile numbers have a fixed length per country: one that's
  off is a transcription error, ask.
- `session_status` must be `WORKING`, and `me.reachoutTimelock` must be `null` (WhatsApp
  throttles messages to new numbers when it isn't: stop and tell the user).
- `check_number`. Not on WhatsApp → stop, say so. Otherwise use the returned `chatId`.
- Already known (in the contact book or `search_contacts`)? Say who, and just send
  (send-message); no intro needed.

## 2. Message

The recipient doesn't have the owner's number saved. The first line says who is writing
(`Hi, this is <owner full name>.`) and, if the user said, how they got the number. The
user's own wording is kept; if it lacks an intro, prepend one and say you did.
Match the language the user used or asked for.

## 3. Send and verify

Text via `send_chat_message` to the `chatId`; photos after it, as in send-message
"Photos and files", each with a short caption. Read back with `get_messages`: every item
`fromMe: true`, `ack` `SERVER` or better.

## 4. Save

Through the profile's `contact_book` route (a skill or a contacts MCP). Search it by number
first in both international and local forms; update rather than duplicate.

Name unknown → the profile's `placeholder_names` (male / female; unknown gender → the
male one), with the organisation or context as the surname, e.g. `Sam Acme Hardware`
for a warehouse manager at Acme Hardware; organisation and job title in their own
fields; a note `First messaged YYYY-MM-DD. <context>. Real name unknown; <placeholder> is a
placeholder.` When they reply with a name (or their WhatsApp push name shows one), update
the contact.

`contact_book: none` → skip and tell the user.

## 5. Report

Sent to +<number> (first words, ack); saved as "<name>" in <book>.

## Ban risk

Messaging strangers is the riskiest thing an unofficial client does. One new number per
request, only on the user's explicit instruction, never in a loop, never the same text to
several new numbers.

---
Validated 2026-11-17: first contact with an Israeli mobile (local `05…` form), one text
plus two captioned PNGs, all `ack: DEVICE` within ten seconds, saved to Google Contacts
under a placeholder name.
