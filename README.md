## Mikan RSS 番剧订阅 Skill

This repository provides a Cursor Agent Skill for fetching **anime RSS subscription links** and **episode lists** from `mikanani.kas.pub` (蜜柑计划).

### What this skill does

- **Normalize user requests** about Mikan shows:
  - Understand year + season (e.g. `2025 秋季番`, `2026 冬季番`)
  - Parse show titles and optional subtitle group preferences
- **Fetch season lineups** from Mikan and locate a specific bangumi by name
- **Scrape bangumi detail pages** to:
  - List available subtitle groups
  - Build per‑group RSS links
  - Optionally list recent episodes (file name, size, updated time, episode page)

### Typical use cases

- Get **RSS links for all groups** for a given show and season  
  e.g. “2026 年冬季《优雅贵族的休假指南。》所有字幕组的 RSS”
- Get **RSS only for a specific group**  
  e.g. “只要 LoliHouse 的订阅链接”
- Explore a **season’s entire lineup** to find the correct bangumi and its groups

### High‑level workflow

1. **Parse user intent**
   - Extract year, season, bangumi title, and (optional) subgroup name
   - If season/year are omitted, assume the most relevant current/previous season and state that assumption
2. **Fetch seasonal bangumi list**
   - Call `https://mikanani.kas.pub/Home/BangumiCoverFlowByDayOfWeek?year=<YEAR>&seasonStr=<SEASON>`
   - Collect all `Bangumi` entries (`bangumiId`, title, etc.)
   - Match the requested title (exact match first, then fuzzy)
3. **Fetch bangumi detail**
   - Visit `https://mikanani.kas.pub/Home/Bangumi/<bangumiId>`
   - Extract all subtitle groups and their IDs
   - For each group, build RSS:
     - `https://mikanani.kas.pub/RSS/Bangumi?bangumiId=<BANGUMI_ID>&subgroupid=<SUBGROUP_ID>`
   - Optionally parse recent episodes for that group
4. **Filter and format results**
   - If the user specified a group, filter to that group; otherwise list all
   - Clearly show: bangumi info, each subtitle group, its RSS URL, and (optionally) latest few episodes
   - Mention any assumptions about season/year or ambiguous title matches

### Repository structure

- `SKILL.md` – Full, detailed skill specification and examples for the Cursor agent.
- `README.md` – High‑level overview (this file).

### Notes

- This skill is focused on **Mikan RSS and resource discovery**, not on video playback or general streaming.
- When unsure about the user’s exact show or season, the agent should:
  - Make a reasonable assumption,
  - Clearly state it,
  - And provide alternatives when multiple close matches exist.

