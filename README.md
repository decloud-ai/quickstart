# deCloud · 快速接入

**一个 API Key 调用全球顶级 AI 模型** —— 对话、图像、视频、语音、转写、嵌入、重排七类模型,
对外统一 OpenAI 兼容协议;原有代码只改「地址 + Key」两处。

> One API key to every frontier AI model.
> Models are the variable. The endpoint is the constant.

![OpenAI compatible](https://img.shields.io/badge/OpenAI-compatible-245e43?style=flat-square)
![Anthropic Messages](https://img.shields.io/badge/Anthropic-Messages-245e43?style=flat-square)
![OpenAI Responses](https://img.shields.io/badge/OpenAI-Responses-245e43?style=flat-square)

📖 完整文档:**https://macdecloud.com/docs** · 🌐 官网:https://macdecloud.com

[English](README.en.md) | 简体中文

---

## 它解决的四件事

| 你会遇到的 | deCloud 的做法 |
|---|---|
| 换个模型就要重测 | **接入与模型解耦** —— 接入写一次,之后模型换代与它无关 |
| 各家接口都不一样 | **统一协议** —— OpenAI 兼容 + Anthropic + Responses |
| 每接一家就多一个 Key | **目录聚合** —— 直连与聚合目录收在一处,来源逐个标注 |
| 月底对账要登多个后台 | **权限与用量合一** —— 权限、用量、明细逐笔可核 |

```text
客户端(Claude Code / Codex / Cherry Studio / 你的代码)
      │  base_url 指向 https://macdecloud.com/v1
      ▼
   deCloud ── /v1/chat/completions · /v1/messages · /v1/responses ──▶ 上游模型厂商
```

## 30 秒接入

**① 写代码(OpenAI SDK)** —— 只改两处:

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://macdecloud.com/v1",   # ← 改这里
    api_key="dcld-sk-...",                  # ← 和这里
)
r = client.chat.completions.create(
    model="deepseek/deepseek-v4-pro",       # 完整 ID:厂商标识/模型名(以模型页为准)
    messages=[{"role": "user", "content": "hello"}],
)
print(r.choices[0].message.content)
```

**② 图形客户端**(NextChat / Chatbox / LobeChat / Open WebUI / Cherry Studio 等)—— 新加一个服务商:

| 字段 | 填什么 |
|---|---|
| 服务商类型 | OpenAI(或"OpenAI 兼容") |
| 接口地址 | `https://macdecloud.com/v1` |
| API Key | 你的 `dcld-sk-…` |
| 模型名 | 完整 ID |

> ⚠️ **别改客户端自带的「OpenAI」那一项** —— 它的地址固定是 `api.openai.com`,会报"密钥不正确"。要**新加**一个自定义服务商。

**③ 命令行 Claude Code**:

```bash
export ANTHROPIC_BASE_URL="https://macdecloud.com"   # 注意:不带 /v1
export ANTHROPIC_AUTH_TOKEN="dcld-sk-..."
export ANTHROPIC_MODEL="<完整模型 ID>"
```

**④ 命令行 Codex**:走 `/v1/responses`,配置写在 `config.toml` 的 provider 里。

**怎么算通了** —— 照上面换掉两处再调用一次:**返回里带 `content` 就说明通了**。

## 能用什么

| | |
|---|---|
| 七类模型 | 对话 · 图像 · 视频 · 语音 · 转写 · 嵌入 · 重排 |
| 三种协议 | `/v1/chat/completions`(OpenAI 兼容) · `/v1/messages`(Anthropic) · `/v1/responses`(Responses) |
| 其它端点 | `/v1/images/generations` · `/v1/videos` · `/v1/audio/speech` · `/v1/audio/transcriptions` |
| 上游可核 | 目录同时收录直连厂商与聚合目录来源,**每个模型的上游在模型页逐个标注** |
| Key 范围 | 每把 Key 可限定可用模型,模型列表随 Key 权限过滤 |
| 用量 | 控制台用量页逐笔可核,**支持表格导出** |

> ⚠️ 视频是**异步**的:提交返回 `202` + `polling_url`;端点是 `/v1/videos`(**不是** `/v1/video/generations`)。

## 计费怎么算

1. **统一按量** —— 充一次余额,按 token 用量扣,无包月档位;
2. **按 token 计价** —— 输入输出分别计价,单价见模型页;
3. **明细可核** —— 控制台用量页逐笔可核,支持表格导出;
4. **失败不计费** —— **上游出错导致的失败**不扣余额;
5. **余额为 0 报 402** —— 直接报错,不会静默失败。

几点说清:

- 聊天**按用量结算**,不是按 token 上限预扣 —— 所以**余额可能出现负数**;
- 视频**相反**:提交时**预留**、完成才**结算**,预留不够直接拒(402);
- 第 4 条的边界是「**上游出错导致的失败**」—— **请求中断不能一律解释为免费**;
- 余额是**预付款记账单位**,不可提现、不可转让;
- 可申请免费额度。

## 报错对照

每个状态码都有一页文档,点右边那列直接跳:

| 返回 | 什么意思 / 怎么办 | 文档 |
|---|---|---|
| `401` | Key 无效 —— 检查多空格、`dcld-sk-` 前缀是否完整、是否已被轮换 | [401](https://macdecloud.com/docs/errors/401-invalid-key) |
| `402` | 余额不足 —— 余额为 0 时免费模型也会被拒 | [402](https://macdecloud.com/docs/errors/402-insufficient-balance) |
| `403` | 该模型未开通 / 额度用尽 —— 换个模型 | [模型未开通](https://macdecloud.com/docs/errors/403-model-not-allowed) · [额度用尽](https://macdecloud.com/docs/errors/403-quota-exhausted) |
| `404` | 模型名不存在 —— 要写**完整 ID**(带厂商前缀),只写简称会 404 | [404](https://macdecloud.com/docs/errors/404-model-not-found) |
| `429` | 请求太频繁 / 上游限流 —— 等几秒重试,降低并发 | [429](https://macdecloud.com/docs/errors/429-rate-limited) |
| `400` + `upstream provider rejected` | 该模型不支持这类请求 —— 多半是不支持工具调用 | [工具调用](https://macdecloud.com/docs/errors/400-tools-not-supported) |
| `400` 端点不匹配 | 图像 / 视频类模型发错了端点 | [端点不匹配](https://macdecloud.com/docs/errors/400-model-endpoint-mismatch) |
| `502` / `504` | 上游侧的问题 —— 隔几秒重试;持续出现请联系客服 | [502](https://macdecloud.com/docs/errors/502-upstream-model-missing) · [504](https://macdecloud.com/docs/errors/504-upstream-timeout) |
| 返回 `200` 但内容为空 | 请求通了、答案没出来 | [200 空内容](https://macdecloud.com/docs/errors/200-empty-content) |

> 更多说明:**https://macdecloud.com/docs/errors**

## 支持

- 📖 文档:https://macdecloud.com/docs
- 💬 Telegram 客服
- 🐛 问题提到本仓 Issues

## License

MIT —— 见 [LICENSE](LICENSE)。示例代码可自由取用。
