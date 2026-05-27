---
name: python-web-scraper
description: Fetch and parse web content using Python. Build scrapers with requests and BeautifulSoup to extract structured data from web pages and APIs.
---

# Python Web Scraper

Fetch web pages or API endpoints and extract structured data using requests + BeautifulSoup.

## Basic Pattern

```python
import requests
from bs4 import BeautifulSoup

url = "https://example.com"
response = requests.get(url, timeout=10)
response.raise_for_status()

soup = BeautifulSoup(response.text, "html.parser")

# Extract data
items = []
for element in soup.select("div.item"):
    title = element.select_one("h2").get_text(strip=True)
    link = element.select_one("a")["href"]
    items.append({"title": title, "link": link})

print(f"Found {len(items)} items")
for item in items:
    print(f"  - {item['title']}: {item['link']}")
```

## JSON API Pattern

```python
import requests
import json

response = requests.get("https://api.example.com/data", timeout=10)
response.raise_for_status()
data = response.json()

# Process and save
with open("output.json", "w") as f:
    json.dump(data, f, indent=2)
print(json.dumps(data, indent=2))
```

## Best Practices

- Always set `timeout=10` on requests to avoid hanging
- Use `response.raise_for_status()` for error handling
- Add User-Agent header: `headers={"User-Agent": "Mozilla/5.0"}`
- Install deps: `python3 -m pip install requests beautifulsoup4`
- Save extracted data to JSON/CSV for downstream use
- Use `soup.select()` (CSS selectors) over `find_all()` for clarity
