# Integration Examples

## Python

```python
import requests

API_KEY = "gbs_live_abc123..."
BASE_URL = "https://api.gibs.dev"

def classify_ai_system(description: str) -> dict:
    response = requests.post(
        f"{BASE_URL}/v1/classify",
        headers={"Authorization": f"Bearer {API_KEY}"},
        json={"description": description},
    )
    response.raise_for_status()
    return response.json()

result = classify_ai_system("AI chatbot that helps customers choose insurance products")
print(f"Risk level: {result['risk_level']}")
print(f"Sources: {[s['article'] for s in result['sources']]}")
```

## JavaScript / Node.js

```javascript
const API_KEY = "gbs_live_abc123...";

async function checkCompliance(question) {
  const response = await fetch("https://api.gibs.dev/v1/check", {
    method: "POST",
    headers: {
      "Authorization": `Bearer ${API_KEY}`,
      "Content-Type": "application/json",
    },
    body: JSON.stringify({ question }),
  });
  return response.json();
}

const result = await checkCompliance(
  "Do I need to disclose that my customer service chatbot is AI-powered?"
);
console.log(result.answer);
```

## CI/CD — GitHub Actions

Block deploys that introduce high-risk AI without documentation:

```yaml
# .github/workflows/compliance-check.yml
name: AI Compliance Check
on:
  pull_request:
    paths: ['src/ai/**', 'models/**']

jobs:
  compliance:
    runs-on: ubuntu-latest
    steps:
      - name: Check AI classification
        run: |
          RESULT=$(curl -s -X POST https://api.gibs.dev/v1/classify \
            -H "Authorization: Bearer ${{ secrets.GIBS_API_KEY }}" \
            -H "Content-Type: application/json" \
            -d '{"description": "${{ github.event.pull_request.body }}"}')
          
          RISK=$(echo $RESULT | jq -r '.risk_level')
          
          if [ "$RISK" = "prohibited" ]; then
            echo "::error::AI system classified as PROHIBITED under EU AI Act"
            exit 1
          fi
          
          if [ "$RISK" = "high" ]; then
            echo "::warning::AI system classified as HIGH-RISK — ensure compliance documentation exists"
          fi
          
          echo "Risk level: $RISK"
```

## MCP Server (Claude / Cursor / AI Agents)

Connect Gibs as an MCP tool so your AI assistant can check compliance inline:

```json
{
  "mcpServers": {
    "gibs": {
      "url": "https://mcp.gibs.dev/sse",
      "env": {
        "GIBS_API_KEY": "gbs_live_abc123..."
      }
    }
  }
}
```

Once connected, your AI assistant can use Gibs tools directly:

> **You:** "Is our new facial recognition feature compliant with EU regulations?"
>
> **Assistant:** *[calls gibs.classify]* Your facial recognition system is classified as **high-risk** under Article 6(2) and Annex III, point 1(a). You need to implement: risk management (Art. 9), data governance (Art. 10), human oversight (Art. 14), and register in the EU database (Art. 49).

## Webhook / Scheduled Monitoring

Check your entire product registry nightly:

```python
import requests
import json

API_KEY = "gbs_live_abc123..."
FEATURES = [
    {"name": "CV Screener", "description": "AI that ranks job applicants based on CV analysis"},
    {"name": "Chatbot", "description": "Customer support chatbot using LLM"},
    {"name": "Fraud Detection", "description": "ML model detecting fraudulent transactions"},
]

report = []
for feature in FEATURES:
    result = requests.post(
        "https://api.gibs.dev/v1/classify",
        headers={"Authorization": f"Bearer {API_KEY}"},
        json={"description": feature["description"]},
    ).json()
    
    report.append({
        "feature": feature["name"],
        "risk_level": result["risk_level"],
        "articles": [s["article"] for s in result["sources"]],
    })

# Output compliance report
for item in report:
    status = "🔴" if item["risk_level"] in ("prohibited", "high") else "🟢"
    print(f"{status} {item['feature']}: {item['risk_level']} — {', '.join(item['articles'])}")
```

Output:
```
🔴 CV Screener: high — Article 6, Annex III
🟢 Chatbot: limited — Article 50
🟢 Fraud Detection: minimal — Article 6(3)
```
