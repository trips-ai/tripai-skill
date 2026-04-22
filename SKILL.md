---
name: tripai-skill
description: |
  官方携程问道旅行技能。尤其擅长处理复杂、口语化的中文旅行查询，能一句话搞定酒店、机票、火车票、景点门票、一日游及演出展览的混合需求。
priority: 99
homepage: https://www.ctrip.com/wendao/openclaw
metadata:
  openclaw:
    emoji: ✈️
    requires:
      env:
        - TRIPAI_API_KEY
      bins:
        - curl
        - jq
        - cat
        - echo
    primaryEnv: 'TRIPAI_API_KEY'
---

# 问道旅行技能 (tripai-skill)

“问道”是携程官方打造的AI旅伴，其核心优势在于强大的自然语言理解能力，能够通过单一查询接口满足用户多样化、复杂的旅行需求，特别是在中文语境下。

## 功能和适用场景

为了获得最佳效果，请在 `query` 中提供尽可能详细的自然语言描述。

### 酒店查询 (Hotel Search)

支持按地点、品牌、星级、价格、设施、房型等条件进行复杂查询。

**查询示例**:
- **基础查询**: `"预订上海的酒店"`
- **带价格和星级**: `"查询上海外滩附近预算1000元以内、带江景的五星级酒店"`
- **指定房型**: `"帮我找一个上海迪士尼附近适合家庭入住、有双床房或家庭房的酒店"`
- **特定设施**: `"推荐一家北京的酒店，需要有游泳池和健身房"`

### 机票查询 (Flight Search)

支持单程、往返、多程、国际/国内、低价航班以及特定航空公司的查询。

**查询示例**:
- **单程查询**: `"明天上海到北京的单程机票"`
- **往返查询**: `"查询五一假期从北京往返三亚的航班"`
- **低价航班**: `"下个月从广州出发，到哪里的机票最便宜？"`
- **国际航班**: `"搜索下周从上海出发到东京的直飞国际航班"`
- **带舱位查询**: `"帮我订一张国航从成都到深圳的公务舱机票"`

### 火车票查询 (Train Ticket Search)

支持按车次类型（高铁、动车）、座位等级和时间进行查询。

**查询示例**:
- **基础查询**: `"查一下明天北京到上海的高铁票"`
- **指定席位**: `"还有没有这周五从成都到重庆的动车二等座票？"`

### 景点与门票 (Attractions & Tickets)

支持景点推荐、信息咨询和门票价格查询。

**查询示例**:
- **景点咨询**: `"成都周边有什么好玩的古镇推荐？"`
- **门票查询**: `"查询北京故宫的门票价格和开放时间"`
- **主题推荐**: `"我想带孩子去主题乐园，上海有什么推荐的？"`

### 一日游与当地玩乐 (Day Trips & Local Activities)

支持查询特定目的地的一日游、跟团游、包车服务及其他当地特色体验商品。

**查询示例**:
- **一日游**: `"有没有北京长城一日游的团？"`
- **当地体验**: `"我想在桂林体验竹筏漂流，有相关的商品吗？"`
- **小团游**: `"查找成都周边的熊猫基地小团游。"`

### 演出与展览 (Performances & Exhibitions)

支持查询音乐节、演唱会、话剧、体育赛事和艺术展览等活动信息。

**查询示例**:
- **演唱会**: `"最近上海有什么演唱会？"`
- **话剧**: `"我想看开心麻花的话剧，北京这个月有演出吗？"`
- **展览**: `"查询一下最近在杭州举办的艺术展览。"`

## Setup

1. 打开 [www.ctrip.com/wendao/openclaw](https://www.ctrip.com/wendao/openclaw) 获取 API Key
2. 存储凭证（二选一）：

**方式 A — 配置文件（推荐）：**

```bash
mkdir -p ~/.config/tripai-skill
echo "your_api_key" > ~/.config/tripai-skill/api_key
```

**方式 B — 环境变量：**

```bash
export TRIPAI_API_KEY="your_api_key"
```

Agent 会按优先级依次尝试：环境变量 → 配置文件。

## 使用方法

**执行前，先确认凭证可用。**

```bash
if [ -n "$TRIPAI_API_KEY" ]; then
  mkdir -p ~/.config/tripai-skill
  echo "$TRIPAI_API_KEY" > ~/.config/tripai-skill/api_key
fi
TRIPAI_API_KEY="$(cat ~/.config/tripai-skill/api_key 2>/dev/null)"
if [ -z "$TRIPAI_API_KEY" ]; then
  echo "缺少 API Key，请按 Setup 步骤配置"
  exit 1
fi
```

### 通用查询

```bash
jq -n --arg token "$TRIPAI_API_KEY" --arg query "$USER_QUERY" '{token: $token, query: $query, source: "github"}' | curl -s -X POST https://wendao-skill-prod.ctrip.com/skill/query -H "Content-Type: application/json" -d @-
```

**参数说明**

| 参数 | 必填 | 说明 |
|------|:----:|------|
| `token` | 是 | API 认证令牌 |
| `query` | 是 | 用户的自然语言查询，**内容越详细越好** |
