<p align="center">
  <h1 align="center"> AI Agent Travel Skills — TripGenie & TripAI</h1>
  <p align="center">
    <strong>Travel search powered by Trip.com and Ctrip — right inside OpenClaw and other skill-compatible agents.</strong>
  </p>
  <p align="center">
    Search flights, hotels, trains, attractions, and trip planning with natural language.<br/>
    No browser tabs. No app switching. Just ask.
  </p>
  <p align="center">
    <a href="https://clawhub.ai/trips-ai/trip-tripgenie-skill"><img src="https://img.shields.io/badge/TripGenie-OpenClaw-0086F6?labelColor=1a1a1a" alt="TripGenie OpenClaw"></a>
    <a href="https://clawhub.ai/trips-ai/wendao-skill"><img src="https://img.shields.io/badge/TripAI-OpenClaw-2563EB?labelColor=1a1a1a" alt="TripAI OpenClaw"></a>
    <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-green" alt="MIT License"></a>
  </p>
  <p align="center">
    <a href="#overview">Overview</a> ·
    <a href="#features">Features</a> ·
    <a href="#setup--configuration">Setup</a> ·
    <a href="#usage-examples">Usage</a> ·
    <a href="#security--privacy">Security</a> ·
    <a href="#license">License</a>
  </p>
</p>

---

## Overview

- **TripGenie**: Powered by [Trip.com](https://www.trip.com/tripgenie), this skill offers broad, international travel search capabilities, including flights and general-purpose travel questions.

- **TripAI**: Powered by [Ctrip (携程)](https://www.ctrip.com/), this skill provides a comprehensive travel assistant for booking hotels, flights, trains, and getting travel recommendations, with a focus on the Chinese market.

## Features

- **Natural Language Interface**: Just ask your agent for travel information in plain English or Chinese.
- **Comprehensive Search**: Access a wide range of travel services:
    - Flights (International and Domestic)
    - Hotels
    - Train Tickets
    - Attraction Recommendations
- **Direct Integration**: No need to switch to a browser or another app. Get results directly in your chat.
- **Powered by Industry Leaders**: Leverages the robust APIs of Trip.com and Ctrip.

## Setup & Configuration

To use these skills, you need to install them in your agent's environment and configure the necessary API keys.

### 1. Skill Installation

Copy the skill directories into your agent's designated skills folder. For example, for Claude Code:

```bash
# Copy TripGenie skill
cp -r tripgenie/ ~/.openclaw/workspace/skills/tripgenie

# Copy TripAI skill
cp -r tripai-skill/ ~/.openclaw/workspace/skills/tripai-skill
```
*(Please adapt the path to your specific agent's skill directory if it differs.)*

### 2. API Key Acquisition

Both skills require an API key (token) to function.

- **For TripGenie**: Obtain your API key from [www.trip.com/tripgenie/openclaw](https://www.trip.com/tripgenie/openclaw).
- **For TripAI**: Obtain your API key from [www.ctrip.com/wendao/openclaw](https://www.ctrip.com/wendao/openclaw).

**Important**: Treat your API keys like passwords. Do not share them publicly or commit them to version control.

### 3. API Key Configuration (Recommended)

The most secure way to configure the keys is by setting them as environment variables. The skills will automatically detect and use them.

```bash
export TRIPGENIE_API_KEY="your-tripgenie-key-here"
export TRIPAI_API_KEY="your-tripai-key-here"
```

You can add these lines to your shell's startup file (e.g., `~/.bashrc`, `~/.zshrc`) to make them persistent.

Alternatively, you can provide the key in-chat when prompted by the agent, but this is less secure and is not persisted. The environment variable will always take precedence.

## Usage Examples

Once installed and configured, you can invoke the skills by asking your agent travel-related questions.

### TripGenie

#### Scenario 1: Pre-trip precise booking

**Core point:** Clear time, route, and transactional intent.

> "Help me find a flight from Shanghai to Hong Kong for next Monday"
>
> "Book a hotel near Hyde Park in London"

This triggers the `tripgenie` skill. A flight search may run a command like:

```bash
# Example underlying command for a flight search
jq -n --arg token "$TRIPGENIE_API_KEY" --arg query "flight from SHA to HKG" --arg departure "SHA" --arg arrival "HKG" --arg date "2026-04-06" --arg flight_type "0" \
'{token: $token, query: $query, departure: $departure, arrival: $arrival, date: $date, flight_type: $flight_type}' | \
curl -s -X POST https://tripgenie-openclaw-prod.trip.com/openclaw/airline -H "Content-Type: application/json" -d @-
```

#### Scenario 2: In-destination exploration

**Core point:** Immediate services and local exploration based on Location-Based Services (LBS).

> "I'm visiting the Louvre this afternoon. Are there any highly-rated French restaurants nearby to recommend?"
>
> "Are there any good places to buy souvenirs near Shinjuku in Tokyo?"

#### Scenario 3: Audience-specific and thematic travel

**Core point:** Vague time or location intent with strong attribute tags (e.g., family, business, budget).

> "I want to take my family to Phuket. Are there any recommended family-friendly resorts with a water park?"
>
> "I want to visit Iceland this winter to see the Northern Lights. Are there any cheap flights?"

### TripAI

#### Scenario 1: Pre-trip precise booking

**Core point:** Clear time, route, and transactional intent.

> "Book a hotel near Sanlitun in Beijing"
>
> "Check tomorrow's high-speed train tickets from Beijing to Shanghai"

This triggers the `tripai-skill` skill. An example underlying command:

```bash
# Example underlying command for a general query
jq -n --arg token "$TRIPAI_API_KEY" --arg query "预订北京三里屯附近的酒店" \
'{token: $token, query: $query}' | \
curl -s -X POST https://wendao-skill-prod.ctrip.com/skill/query -H "Content-Type: application/json" -d @-
```

#### Scenario 2: In-destination exploration

**Core point:** Immediate services and local exploration based on Location-Based Services (LBS).

> "I'm visiting the Forbidden City this afternoon. Are there any highly-rated time-honored restaurants nearby to recommend?"
>
> "Are there any good places for shopping near Wukang Road in Shanghai?"

#### Scenario 3: Audience-specific and thematic travel

**Core point:** Vague time or location intent with strong attribute tags (e.g., family, business, budget).

> "I want to take my kids to Sanya. Are there any recommended family-friendly resorts with a water park?"
>
> "I'm planning to visit Harbin during the May Day holiday. Are there any cheap flights?"

## Security & Privacy

- **API Keys**: Keys are your responsibility. Use environment variables for security. The skills are designed to not log, echo, or persist keys provided in chat.
- **Third-Party Content**: All responses are generated by external services (Trip.com, Ctrip). Do not assume the content is always accurate or safe. The agent should present the information, but use discretion.
- **Endpoints**: The skills are hardcoded to use the official production endpoints:
    - `https://tripgenie-openclaw-prod.trip.com`
    - `https://wendao-skill-prod.ctrip.com`
- **Rate Limits**: Be aware of any rate limits, billing, or quotas associated with your API keys.

## License

[MIT](LICENSE) — Copyright (c) 2026 trip
