---
name: wendao-skill
description: |
  您的全时 AI 旅伴，由携程官方倾力打造，已累计为超 5000 万用户提供解答。无论是预订机酒火车票、定制旅行攻略，还是寻找当地特色玩乐，只需一句话，问道为您轻松安排
homepage: https://www.ctrip.com/wendao/openclaw
metadata:
  openclaw:
    emoji: ✈️
    requires:
      bins:
        - curl
        - jq
---

# 问道旅行技能 (wendao_skill)

## Setup

1. **获取 token（API key）** — 打开 [www.ctrip.com/wendao/openclaw](https://www.ctrip.com/wendao/openclaw)，按页面指引申请并复制你的 **API token**（仅保存在本人可信环境，勿截图含完整密钥发到公开渠道）。
2. **提供 token（二选一）** — **（1）设置环境变量**：配置 **`WENDAO_API_KEY`**；OpenClaw 在 **`~/.openclaw/.env`** 写入 `WENDAO_API_KEY=<你的 token>`（建议文件仅本人可读），保存后**重启 gateway**；也可用平台密钥、宿主机 env、CI secret。**（2）放在对话中**：由用户在**当前对话**里提供 API key，请求 JSON 的 **`token`** 使用该值。**若环境变量与对话同时有值，以环境变量为准**。勿在回复或日志中回显完整密钥，勿写入仓库。
3. **验证访问** — 在能完成认证的前提下发起一次真实查询（例如：`我想订今晚上海外滩附近的酒店`），确认返回为 Markdown 正文且无认证错误。

## Security & trust (before production use)

- **Endpoint**：确认请求发往文档中的官方域名（`https://wendao-skill-prod.ctrip.com`），勿在未核实的情况下改用未知域名。
- **Key scope / billing**：向提供方确认 key 权限、计费与 QPS/配额，避免误用或超额。
- **External content**：响应来自携程问道服务，可能含链接、营销文案或结构化信息；按你方产品策略决定是否展示、是否需过滤或摘要，**不要**假定第三方正文永远无害或永远准确。
- **Invocation**：本技能适合旅行类意图；若平台支持限制自动调用频率或范围，可按合规要求配置。

## 使用方法

**执行前，先确定 token 值（按优先级）：**

```bash
# 1. 优先用环境变量
# 2. 若环境变量为空，则使用用户在本次对话中提供的 key（直接赋值给 TOKEN）
export TOKEN="${WENDAO_API_KEY:-<用户在对话中提供的key>}"
```

### 通用查询

```bash
jq -n --arg token "$TOKEN" --arg query "$USER_QUERY" '{token: $token, query: $query}' | curl -s -X POST https://wendao-skill-prod.ctrip.com/skill/query -H "Content-Type: application/json" -d @- > /tmp/wendao-result.md
cat /tmp/wendao-result.md
```

**参数说明**

| 参数 | 必填 | 说明 |
|------|:----:|------|
| `token` | 是 | API 认证令牌，取值见 **Setup**（勿写入日志） |
| `query` | 是 | 用户的自然语言查询 |

## 适用场景

| 场景 | 示例查询 |
|------|----------|
| 酒店预订 | "预订北京三里屯附近的酒店" / "上海外滩五星级酒店，预算 800-1200 元" |
| 航班搜索 | "搜索明天从北京到上海的航班" / "去纽约的国际航班多少钱" |
| 火车票查询 | "查一下北京到上海的高铁票" / "明天成都到重庆的动车还有票吗" |
| 景点推荐 | "成都周边有什么好玩的景点" / "带孩子去迪士尼的推荐攻略" |
| 行程规划 | "我要去日本，帮我规划一个 7 天行程" |

## 注意事项

- 默认将 API 返回的 Markdown **如实展示给用户**
- 响应不完整或超时时，可重试或提示用户稍后再试；勿在日志中打印完整 `token`。
