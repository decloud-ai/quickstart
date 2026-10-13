<p align="right"><a href="README.md">中文</a> · <b>English</b></p>

<div align="center">

<img src="https://github.com/decloud-ai/.github/raw/main/assets/logo.png" width="48" alt="deCloud">

## deCloud · Quickstart

**One API key to every frontier AI model**<br>
<sub>一个 API Key 调用全球顶级 AI 模型 · Models are the variable. The endpoint is the constant.</sub>

![OpenAI compatible][s-1] ![Anthropic Messages][s-2] ![OpenAI Responses][s-3]

[![Website](https://img.shields.io/badge/Website-245e43?style=for-the-badge&logo=googlechrome&logoColor=white)](https://macdecloud.com/?utm_source=gh) [![Docs](https://img.shields.io/badge/Docs-245e43?style=for-the-badge&logo=readthedocs&logoColor=white)](https://macdecloud.com/docs?utm_source=gh) [![YouTube](https://img.shields.io/badge/YouTube-a13a2c?style=for-the-badge&logo=youtube&logoColor=white)](https://www.youtube.com/@decloud-AI) [![Support](https://img.shields.io/badge/Support-26A5E4?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/DeCloud_CS)

</div>

---

### `01` Quickstart

`In code (OpenAI SDK)` <sub>install once, then change two things</sub>

```bash
pip install openai
```

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://macdecloud.com/v1",   # change this
    api_key="dcld-sk-...",                  # and this
)
r = client.chat.completions.create(
    model="deepseek/deepseek-chat",         # full ID: vendor/model (check the model page)
    messages=[{"role": "user", "content": "hello"}],
)
print(r.choices[0].message.content)
```

---

`GUI client` <sub>NextChat / Chatbox / LobeChat / Open WebUI / Cherry Studio — add a new provider</sub>

| Field | What to enter |
|---|---|
| Provider type | OpenAI (or "OpenAI-compatible") |
| Base URL | `https://macdecloud.com/v1` |
| API key | your `dcld-sk-…` |
| Model | the full ID |

> [!WARNING]
> Do not edit the built-in "OpenAI" entry — its address is fixed at `api.openai.com` and you will get an "invalid key" error. Add a new custom provider instead.

---

`Claude Code`

```bash
export ANTHROPIC_BASE_URL="https://macdecloud.com"   # note: no /v1
export ANTHROPIC_AUTH_TOKEN="dcld-sk-..."
export ANTHROPIC_MODEL="<full model ID>"
```

<sub>To use an alias instead of the full ID, create one in the console under API Keys → Model aliases; avoid reserved short names such as `opus`/`sonnet`/`haiku`.</sub>

---

`Codex` <sub>uses `/v1/responses`; configure it in `~/.codex/config.toml`</sub>

```toml
model = "decloud-codex"
model_provider = "decloud"

[model_providers.decloud]
name = "deCloud"
base_url = "https://macdecloud.com/v1"
env_key = "DECLOUD_API_KEY"
wire_api = "responses"
```

```bash
export DECLOUD_API_KEY='dcld-sk-...'
codex
```

<sub>How you know it worked: after the two changes, call once — if the response contains `content`, it works.</sub>

---

### `02` What you can use

| Item | Detail |
|---|---|
| Seven model classes | chat · image · video · speech · transcription · embeddings · rerank |
| Three protocols | `/v1/chat/completions` (OpenAI-compatible) · `/v1/messages` (Anthropic) · `/v1/responses` (Responses) |
| Other endpoints | `/v1/images/generations` · `/v1/videos` · `/v1/audio/speech` · `/v1/audio/transcriptions` |
| Verifiable upstream | the catalog mixes first-party vendors and aggregator sources; each model's upstream is labeled on its model page |
| Key scope | each key can be limited to a set of models; the model list is filtered by key permissions |
| Usage | per-call usage in the console, exportable as a table |

> [!WARNING]
> Video is asynchronous: submit returns `202` + a `polling_url`; the endpoint is `/v1/videos` (not `/v1/video/generations`).

---

### `03` Errors

Every status code has its own docs page — the right-hand column links straight to it:

| Response | What it means / what to do | Docs |
|---|---|---|
| `401` | Invalid key — check for extra spaces, a full `dcld-sk-` prefix, or a rotated key | [401](https://macdecloud.com/docs/errors/401-invalid-key?utm_source=gh) |
| `402` | Insufficient balance — even free models are rejected at zero balance | [402](https://macdecloud.com/docs/errors/402-insufficient-balance?utm_source=gh) |
| `403` | Model not enabled for this key / quota exhausted — try another model | [not enabled](https://macdecloud.com/docs/errors/403-model-not-allowed?utm_source=gh) · [quota](https://macdecloud.com/docs/errors/403-quota-exhausted?utm_source=gh) |
| `404` | Model name does not exist — use the full ID (with the vendor prefix); a short name will 404 | [404](https://macdecloud.com/docs/errors/404-model-not-found?utm_source=gh) |
| `429` | Too many requests / upstream rate limit — retry in a few seconds, lower concurrency | [429](https://macdecloud.com/docs/errors/429-rate-limited?utm_source=gh) |
| `400` + `upstream provider rejected` | The model does not support this kind of request — usually missing tool calling | [tool calling](https://macdecloud.com/docs/errors/400-tools-not-supported?utm_source=gh) |
| `400` endpoint mismatch | An image / video model was sent to the wrong endpoint | [mismatch](https://macdecloud.com/docs/errors/400-model-endpoint-mismatch?utm_source=gh) |
| `502` / `504` | Upstream-side problem — retry in a few seconds; contact support if it persists | [502](https://macdecloud.com/docs/errors/502-upstream-model-missing?utm_source=gh) · [504](https://macdecloud.com/docs/errors/504-upstream-timeout?utm_source=gh) |
| `200` with empty content | The request went through but no answer came back | [empty content](https://macdecloud.com/docs/errors/200-empty-content?utm_source=gh) |

Full troubleshooting guide: [Docs · Errors](https://macdecloud.com/docs/errors?utm_source=gh).

---

### `04` Billing

1. Prepaid usage — top up once, pay per token; no monthly tiers;
2. Priced per token — input and output priced separately; unit prices are on each model page;
3. Verifiable detail — per-call usage in the console, exportable as a table;
4. Failures are not charged — requests that fail upstream are not billed;
5. Zero balance returns 402 — a hard error, never a silent failure.

A few things worth spelling out:

- Chat settles by usage, not by a token cap reserved up front — so the balance can go negative;
- Video is the opposite: reserved on submit, settled on completion; too small a hold is rejected with 402;
- The boundary in item 4 is "requests that fail upstream" — an interrupted request is not automatically free;
- Balance is a prepaid accounting unit: not withdrawable, not transferable;
- Trial credits are available on request.

---

### `05` Why deCloud

| What you run into | How deCloud handles it |
|---|---|
| Retest on every switch | Decoupled from models — write the integration once; model churn stops being your problem |
| Every API is different | One unified protocol — OpenAI-compatible + Anthropic + Responses |
| A new key per vendor | One aggregated catalog — direct vendors and aggregator sources in one place, each upstream labeled |
| Month-end across consoles | Usage and access together — permissions, usage and per-call detail in one place |

```text
your client (Claude Code / Codex / Cherry Studio / your own code)
      │  base_url → https://macdecloud.com/v1
      ▼
   deCloud ── /v1/chat/completions · /v1/messages · /v1/responses ──▶ upstream model vendors
```

---

### `06` Support

- Docs: <https://macdecloud.com/docs?utm_source=gh>
- Telegram support: <https://t.me/DeCloud_CS>
- Errors, suggestions, docs problems: <https://github.com/decloud-ai/quickstart/issues>

---

### `07` Get started

<div align="center">

[![Get started](https://img.shields.io/badge/Get%20started-245e43?style=for-the-badge&logo=googlechrome&logoColor=white)](https://macdecloud.com/?utm_source=gh) [![All docs](https://img.shields.io/badge/All%20docs-192b25?style=for-the-badge&logo=github&logoColor=white)](https://github.com/decloud-ai/.github/blob/main/DOCS.md) [![Email](https://img.shields.io/badge/Email-245e43?style=for-the-badge&logo=gmail&logoColor=white)](mailto:globaldecloud@gmail.com)

<sub>Connect to sign in → Create an API Key → Plug it in → make your first call.</sub>

</div>

---

### License

MIT — see [LICENSE](LICENSE). Sample code is free to reuse.

[s-1]: https://img.shields.io/badge/OpenAI-compatible-192b25?style=flat-square
[s-2]: https://img.shields.io/badge/Anthropic-Messages-192b25?style=flat-square
[s-3]: https://img.shields.io/badge/OpenAI-Responses-192b25?style=flat-square
