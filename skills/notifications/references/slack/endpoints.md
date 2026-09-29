# Endpoints — Slack chat.postMessage

Literal wiring layer. `procedure.md` steps reference these by name; bind them to whatever HTTP
client/adapter is assumed available. Source: Slack's Web API reference for `chat.postMessage`
(https://docs.slack.dev/reference/methods/chat.postMessage, retrieved 2026-09-29).

## Post a Message

**Method/URL:**

```
POST https://slack.com/api/chat.postMessage
```

**Authentication:** `Authorization: Bearer <token>` header, where `<token>` is a bot or user token
carrying the `chat:write` scope (add `chat:write.customize` if overriding `username`/`icon_emoji`/
`icon_url` on the call — see `procedure.md` step 4).

**Body fields:**

| Field | Required | Notes |
|---|---|---|
| `channel` | Yes | Channel ID, private group ID, or a user ID (for a DM / App Home target). |
| `text` | Conditionally | Plain-text body/fallback; whether it's strictly required depends on whether `blocks` is also present — always populate it regardless (see `procedure.md`). |
| `blocks` | No | JSON array of Block Kit layout blocks for rich formatting. |
| `attachments` | No | Legacy structured attachment array (max 100 entries per call). |
| `thread_ts` | No | Parent message timestamp to post this as a threaded reply. |
| `reply_broadcast` | No | Boolean; also surfaces a threaded reply in the main channel view. |
| `mrkdwn` | No | Boolean, default true; set false to disable Slack's markdown parsing of `text`. |
| `parse` | No | Changes link/mention parsing behavior (`none` or `full`). |
| `unfurl_links` / `unfurl_media` | No | Booleans controlling automatic link/media preview expansion. |
| `username` / `icon_emoji` / `icon_url` | No | Sender identity overrides; require `chat:write.customize` scope to take effect. |

**Success response** (HTTP 200 and `ok: true`):

```json
{
  "ok": true,
  "channel": "C123ABC456",
  "ts": "1503435956.000247",
  "message": { "...": "the posted message object" }
}
```

**Failure response** (HTTP 200 or 4xx, with `ok: false`):

```json
{
  "ok": false,
  "error": "channel_not_found"
}
```

Documented error strings worth pattern-matching on: `channel_not_found`, `not_in_channel`,
`no_permission`, `invalid_blocks`, `too_many_attachments`, `no_text`, `rate_limited`.

**Rate limiting:** HTTP 429 response includes a `Retry-After` header (seconds to wait). Example from
Slack's own documentation:

```
HTTP/1.1 429 Too Many Requests
Retry-After: 30
```

`chat.postMessage` is in Slack's "Special" rate-limit tier rather than the general Tier 1–4 scheme —
Slack's own guidance describes roughly one message per second per channel as the sustained limit,
with short bursts tolerated.
