# deCloud · Quickstart

**One API key to every frontier AI model** — chat, image, video, speech, transcription, embeddings and rerank
behind one OpenAI-compatible protocol; existing code changes only the base URL and the key.

> Models are the variable. The endpoint is the constant.

![OpenAI compatible](https://img.shields.io/badge/OpenAI-compatible-245e43?style=flat-square)
![Anthropic Messages](https://img.shields.io/badge/Anthropic-Messages-245e43?style=flat-square)
![OpenAI Responses](https://img.shields.io/badge/OpenAI-Responses-245e43?style=flat-square)

📖 Docs: **https://macdecloud.com/docs** · 🌐 Website: https://macdecloud.com

[English](README.en.md) | [简体中文](README.md)

---

## What it solves

| What you run into | How deCloud handles it |
|---|---|
| Retest on every switch | **Decoupled from models** — write the integration once; model churn stops being your problem |
| Every API is different | **One unified protocol** — OpenAI-compatible + Anthropic + Responses |
| A new key per vendor | **One aggregated catalog** — direct vendors and aggregator sources in one place, each upstream labeled |
| Month-end across consoles | **Usage and access together** — permissions, usage and per-call detail in one place |

```text
your client (Claude Code / Codex / Cherry Studio / your own code)
      │  base_url → https://macdecloud.com/v1
      ▼
   deCloud ── /v1/chat/completions · /v1/messages · /v1/responses ──▶ upstream model vendors
```

## 30-second setup

**① In code (OpenAI SDK)** — change two things:

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://macdecloud.com/v1",   # ← this
    api_key="dcld-sk-...",                  # ← and this
)
r = client.chat.completions.create(
    model="deepseek/deepseek-v4-pro",       # full ID: vendor/model (check the model page)
    messages=[{"role": "user", "content": "hello"}],
)
print(r.choices[0].message.content)
```

**② GUI clients** (NextChat / Chatbox / LobeChat / Open WebUI / Cherry Studio …) — add a new provider:

| Field | Value |
|---|---|
| Provider type | OpenAI (or "OpenAI-compatible") |
| Base URL | `https://macdecloud.com/v1` |
| API key | your `dcld-sk-…` |
| Model | the full ID |

> ⚠️ **Don't edit the built-in "OpenAI" provider** — its URL is pinned to `api.openai.com` and you'll get "incorrect API key". **Add** a custom provider instead.

**③ Claude Code (CLI)**:

```bash
export ANTHROPIC_BASE_URL="https://macdecloud.com"   # note: no /v1
export ANTHROPIC_AUTH_TOKEN="dcld-sk-..."
export ANTHROPIC_MODEL="<full model ID>"
```

**④ Codex (CLI)**: uses `/v1/responses`; configure it in the `provider` block of `config.toml`.

**How you know it worked** — after the two changes, call once: **if the response contains `content`, it works.**

## What you can use

| | |
|---|---|
| Seven model classes | chat · image · video · speech · transcription · embeddings · rerank |
| Three protocols | `/v1/chat/completions` (OpenAI-compatible) · `/v1/messages` (Anthropic) · `/v1/responses` (Responses) |
| Other endpoints | `/v1/images/generations` · `/v1/videos` · `/v1/audio/speech` · `/v1/audio/transcriptions` |
| Verifiable upstream | the catalog mixes first-party vendors and aggregator sources; **each model's upstream is labeled on its model page** |
| Key scope | each key can be limited to a set of models; the model list is filtered by key permissions |
| Usage | per-call usage in the console, **exportable as a table** |

> ⚠️ Video is **asynchronous**: submit returns `202` + a `polling_url`; the endpoint is `/v1/videos` (**not** `/v1/video/generations`).

## Billing

1. **Prepaid usage** — top up once, pay per token; no monthly tiers;
2. **Priced per token** — input and output priced separately; unit prices are on each model page;
3. **Verifiable detail** — per-call usage in the console, exportable as a table;
4. **Failures are not charged** — requests that **fail upstream** are not billed;
5. **Zero balance returns 402** — an explicit error, not a silent failure.

Worth spelling out:

- chat is **settled by usage**, not pre-authorized up to a token cap — so a **balance can go negative**;
- video is the opposite: **reserved** on submit, **settled** on completion; an insufficient reserve is rejected (402);
- the boundary of point 4 is "**failed upstream**" — **an interrupted request is not automatically free**;
- balance is a **prepaid accounting unit**: not withdrawable, not transferable;
- trial credits are available on request.

## Errors

Every status code has its own docs page — the right-hand column links straight to it:

| Response | What it means / what to do | Docs |
|---|---|---|
| `401` | Invalid key — check for extra spaces, a full `dcld-sk-` prefix, or a rotated key | [401](https://macdecloud.com/docs/errors/401-invalid-key) |
| `402` | Insufficient balance — even free models are rejected at zero balance | [402](https://macdecloud.com/docs/errors/402-insufficient-balance) |
| `403` | Model not enabled for this key / quota exhausted — try another model | [not enabled](https://macdecloud.com/docs/errors/403-model-not-allowed) · [quota](https://macdecloud.com/docs/errors/403-quota-exhausted) |
| `404` | Model name does not exist — use the **full ID** (with the vendor prefix); a short name will 404 | [404](https://macdecloud.com/docs/errors/404-model-not-found) |
| `429` | Too many requests / upstream rate limit — retry in a few seconds, lower concurrency | [429](https://macdecloud.com/docs/errors/429-rate-limited) |
| `400` + `upstream provider rejected` | The model does not support this kind of request — usually missing tool calling | [tool calling](https://macdecloud.com/docs/errors/400-tools-not-supported) |
| `400` endpoint mismatch | An image / video model was sent to the wrong endpoint | [mismatch](https://macdecloud.com/docs/errors/400-model-endpoint-mismatch) |
| `502` / `504` | Upstream-side problem — retry in a few seconds; contact support if it persists | [502](https://macdecloud.com/docs/errors/502-upstream-model-missing) · [504](https://macdecloud.com/docs/errors/504-upstream-timeout) |
| `200` with empty content | The request went through but no answer came back | [empty content](https://macdecloud.com/docs/errors/200-empty-content) |

> More: **https://macdecloud.com/docs/errors**

## Support

- 📖 Docs: https://macdecloud.com/docs
- 💬 Telegram support: https://t.me/DeCloud_CS
- 🐛 Open an issue in this repository

## License

MIT — see [LICENSE](LICENSE). Sample code is free to reuse.
