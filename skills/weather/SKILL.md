---
name: weather
description: Get current weather and forecasts (no API key required).
homepage: "https://wttr.in/:help"
metadata: "{\"clawdbot\":{\"emoji\":\"🌤️\",\"requires\":{\"bins\":[\"curl\"]}}}"
---

# Weather

Two free services, no API keys needed.

## wttr.in (primary)

Quick one-liner:
```bash
curl -s "wttr.in/London?format=3"
# Output: London: ⛅️ +8°C
```

Compact format:
```bash
curl -s "wttr.in/London?format=%l:+%c+%t+%h+%w"
# Output: London: ⛅️ +8°C 71% ↙5km/h
```

Full forecast:
```bash
curl -s "wttr.in/London?T"
```

Format codes: `%c` condition · `%t` temp · `%h` humidity · `%w` wind · `%l` location · `%m` moon

JSON API (for scripts / programmatic use):
```bash
curl -s "wttr.in/Tokyo?format=j1"
```
Returns a full JSON object with `current_condition`, `weather` (3-day forecast), and more. Use `jq` or Python to parse:
```bash
# Current temperature in Celsius
curl -s "wttr.in/Tokyo?format=j1" | jq '.current_condition[0].temp_C'
```
Key JSON paths:
- `.current_condition[0].temp_C` / `.temp_F` — temperature
- `.current_condition[0].humidity` — humidity %
- `.current_condition[0].weatherDesc[0].value` — condition text
- `.current_condition[0].windspeedKmph` — wind speed
- `.weather[0]` / `[1]` / `[2]` — today / tomorrow / day-after forecasts

Tips:
- URL-encode spaces: `wttr.in/New+York`
- Airport codes: `wttr.in/JFK`
- Units: `?m` (metric) `?u` (USCS)
- Today only: `?1` · Current only: `?0`
- PNG: `curl -s "wttr.in/Berlin.png" -o /tmp/weather.png`

## Open-Meteo (fallback, JSON)

Free, no key, good for programmatic use:
```bash
curl -s "https://api.open-meteo.com/v1/forecast?latitude=51.5&longitude=-0.12&current_weather=true"
```

Find coordinates for a city, then query. Returns JSON with temp, windspeed, weathercode.

Docs: https://open-meteo.com/en/docs
