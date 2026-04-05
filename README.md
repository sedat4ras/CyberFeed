# CyberFeed

**Automated Cybersecurity News Aggregation & Intelligence Pipeline**

CyberFeed is an n8n-based workflow that automatically collects, summarizes, categorizes, and publishes daily cybersecurity news to a Notion database.

---

## How It Works

```
Schedule Trigger (Daily @ 12:00)
        |
        v
  +-----------+    +-----------------+    +------------------+    +---------------+    +----------------+
  | RSS Feeds | -> | Merge & Filter  | -> | GPT-4.1 Summary  | -> | Tag & Enrich  | -> | Notion Publish |
  | (5 sources)|   | (last 24h, top 10)  | (title, summary,  |   | (categories,   |   | (DB page +     |
  +-----------+    +-----------------+    |  hook, importance)|   |  CEO brief)    |   |  article blocks)|
                                          +------------------+    +---------------+    +----------------+
```

### Pipeline Stages

| Stage | Node | Description |
|-------|------|-------------|
| **1. Trigger** | `Schedule Trigger` | Fires daily at 12:00 PM |
| **2. Collect** | 5x `RSS Feed Read` | Pulls latest articles from The Hacker News, BleepingComputer, Krebs on Security, Dark Reading, SecurityWeek |
| **3. Merge** | `Merge` | Combines all feeds into a single stream |
| **4. Filter & Clean** | `Code (JS)` | Filters to last 24 hours, strips HTML, limits to top 10 articles |
| **5. AI Summarize** | `OpenAI (GPT-4.1)` | Generates structured JSON per article: concise title, 100-200 word summary, CEO hook, importance score (1-10) |
| **6. Tag & Categorize** | `Code (JS)` | Auto-tags articles across 10 categories (Ransomware, Malware, Phishing, Vulnerability, Data Breach, Nation-State, AI Security, Cloud Security, Network Security, Endpoint). Generates a CEO-level brief from top 3 stories |
| **7. Publish** | `Notion API` | Creates a dated database page with properties (title, CEO text, categories, date, status) and appends full article blocks with bold formatting |

---

## RSS Sources

| Source | Feed URL |
|--------|----------|
| The Hacker News | `https://feeds.feedburner.com/TheHackersNews` |
| BleepingComputer | `https://www.bleepingcomputer.com/feed/` |
| Krebs on Security | `https://krebsonsecurity.com/feed/` |
| Dark Reading | `https://www.darkreading.com/rss.xml` |
| SecurityWeek | `https://feeds.feedburner.com/Securityweek` |

---

## Category Taxonomy

The workflow auto-classifies articles using keyword matching across these categories:

- **Ransomware** -- ransomware, ransom
- **Malware** -- malware, trojan, virus, worm, spyware, rootkit, backdoor
- **Phishing** -- phishing, spear phishing, social engineering
- **Vulnerability** -- vulnerability, CVE, zero-day, patch, exploit
- **Data Breach** -- data breach, data leak, leaked, exposed data, stolen data
- **Nation-State** -- nation-state, APT, state-sponsored, China, Russia, North Korea, Iran
- **AI Security** -- artificial intelligence, machine learning, LLM, deepfake, AI model
- **Cloud Security** -- cloud, AWS, Azure, Google Cloud, S3, Kubernetes
- **Network Security** -- firewall, DDoS, botnet, DNS, VPN
- **Endpoint** -- endpoint, Windows, Linux, macOS, Android, iOS

---

## Integration

CyberFeed writes daily digests to a Notion database. Any frontend application can consume the same database via the Notion API to display articles publicly.

---

## Setup & Configuration

### Prerequisites
- [n8n](https://n8n.io/) instance (self-hosted or cloud)
- OpenAI API key (GPT-4.1 access)
- Notion integration token + database

### Steps

1. **Import the workflow**: In n8n, go to *Settings > Import Workflow* and upload `CyberFeed.json`
2. **Configure credentials**:
   - Create an OpenAI credential and link it to the "Message a model" node
   - Create a Notion credential and link it to the "Create a database page" and "HTTP Request" nodes
3. **Set up Notion database** with these properties:
   - `Title` (title)
   - `CEO Text` (rich text)
   - `Multi-select` (multi-select)
   - `Date` (date)
   - `Status` (status)
4. **Update the database ID** in the "Create a database page" node
5. **Activate the workflow** -- it will run daily at 12:00 PM

---

## Workflow Screenshot

![CyberFeed n8n Workflow](CyberFeed%20SS.png)

---

## Files

| File | Description |
|------|-------------|
| `CyberFeed.json` | n8n workflow export (importable) |
| `CyberFeed SS.png` | Visual screenshot of the workflow |
| `README.md` | This documentation |

---

## Security Notes

- No API keys, tokens, or secrets are stored in this repository
- The JSON file contains only n8n credential reference IDs (not actual secrets)
- All sensitive credentials are managed within n8n's encrypted credential store
- Notion database IDs have been redacted

---

## Author

**Sedat Aras**
- GitHub: [sedat4ras](https://github.com/sedat4ras)
