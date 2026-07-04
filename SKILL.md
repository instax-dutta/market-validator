---
name: market-validator
description: |
  Validate SaaS/product ideas by scraping real user complaints from Reddit,
  Twitter, G2, Quora, HackerNews, ProductHunt, V2EX, XiaoHongShu, Bilibili,
  and YouTube using agent-reach.

  Use this whenever the user wants to:
  - Validate a product or SaaS idea
  - Do market research for a new project or feature
  - Check if people actually have a problem worth solving
  - Find market gaps and opportunities
  - Evaluate demand vs competition
  - Decide whether to build something
  - Understand what users are complaining about in a space
  - Get real quotes and evidence for a problem space
  - "Is this a good idea?", "should I build this?", "validate this"
  - Do any form of idea validation or market sounding

  This skill REPLACES guesswork with evidence from actual user complaints
  across 10+ platforms. It does NOT generate ideas — it VALIDATES them.
metadata:
  openclaw:
    homepage: https://github.com/Panniantong/Agent-Reach
---

# Market Validator

Validate product ideas by finding real people complaining about real problems across 10+ platforms via agent-reach. Replaces endless brainstorming with evidence.

## Prerequisites

Requires **agent-reach** to be installed and available. agent-reach is the gateway to all platforms. Run at the start:

```bash
agent-reach doctor --json
```

This tells you which backends are active for Reddit, Twitter, XiaoHongShu, etc. All platform commands route through agent-reach. If a platform is unavailable, skip it and note it.

## Core Process — 4 Phases

Do them in order. Do not skip data collection. The research drives the score, not the other way around.

---

### Phase 1: Target Definition

Narrow the problem space. One question at a time.

**Establish:**
- **Target user** — be specific ("SMB salespeople" not "everyone")
- **Core problem** — "cold email personalization" not "sales"
- **Success metric** — what would prove this works?

If the user says "an AI for everyone", help them focus: "Let's pick one vertical. What user group did you have in mind?"

**Output:** A single `<problem_space>` and `<target_user>`. If the idea is already narrow, confirm and proceed.

---

### Phase 2: Multi-Platform Data Collection

Search every available platform. Dispatch all searches — do not wait for one to finish before starting another. Collect results as they arrive.

#### Universal Query Patterns

For every platform, generate queries from these patterns:

```
"[problem_space] sucks"
"[problem_space] problem"
"worst part about [problem_space]"
"[problem_space] frustrating"
"[problem_space] alternative"
"I wish [problem_space]"
"[problem_space] pain point"
"why [problem_space] is hard"
"[problem_space] struggling with"
"[problem_space] review"
"[problem_space] complaint"
"[target_user] [problem_space] issue"
```

Pick 3-5 queries per platform. Prioritize queries that surface complaints and venting over marketing content.

#### Platform Commands

**Reddit** (via agent-reach):
```bash
opencli reddit search "[problem_space] sucks" -f yaml
opencli reddit search "[problem_space] problem" -f yaml
opencli reddit search "[target_user] [problem_space]" -f yaml
# Subreddit-specific
opencli reddit subreddit [subreddit_name] -f yaml
# Read the most relevant threads
opencli reddit read POST_ID -f yaml
```

**Twitter/X** (via agent-reach):
```bash
twitter search "[problem_space] sucks" -n 10
twitter search "[problem_space] problem frustrating" -n 10
twitter search "[problem_space] fixed" -n 10
twitter search "I wish [problem_space]" -n 10
```

**G2 Reviews** (Exa web search via agent-reach):
```bash
mcporter call 'exa.web_search_exa(query: "site:g2.com [problem_space] review complaint", numResults: 10)'
```

**Quora** (Exa web search):
```bash
mcporter call 'exa.web_search_exa(query: "site:quora.com [problem_space] problem solution", numResults: 10)'
```

**HackerNews** (Exa web search):
```bash
mcporter call 'exa.web_search_exa(query: "site:news.ycombinator.com [problem_space]", numResults: 10)'
```

**ProductHunt** (Exa web search):
```bash
mcporter call 'exa.web_search_exa(query: "site:producthunt.com [problem_space] review", numResults: 10)'
```

**Capterra** (Exa web search):
```bash
mcporter call 'exa.web_search_exa(query: "site:capterra.com [problem_space]", numResults: 10)'
```

**General web** (Exa web search):
```bash
mcporter call 'exa.web_search_exa(query: "[problem_space] review complaint problem sucks", numResults: 10)'
```

**Read full page content** for the most promising results (Jina Reader via agent-reach):
```bash
curl -s "https://r.jina.ai/URL"
```

**V2EX** (developer focus, via agent-reach):
```bash
curl -s "https://www.v2ex.com/api/topics/show.json?node_name=python" -H "User-Agent: agent-reach/1.0"
# Or search via reader
curl -s "https://r.jina.ai/https://www.v2ex.com/search?q=[problem_space]" 2>/dev/null
```

**XiaoHongShu** (Chinese market, via agent-reach):
```bash
opencli xiaohongshu search "[problem_space] 吐槽" -f yaml
opencli xiaohongshu search "[problem_space] 问题" -f yaml
opencli xiaohongshu search "[problem_space] 好用吗" -f yaml
# Read full note
opencli xiaohongshu note NOTE_URL -f yaml
```

**Bilibili** (Chinese market, via agent-reach):
```bash
bili search "[problem_space] 吐槽" --type video -n 5
bili search "[problem_space] 问题" --type video -n 5
```

**YouTube** (via agent-reach + yt-dlp):
```bash
# Find relevant videos via web search
mcporter call 'exa.web_search_exa(query: "site:youtube.com [problem_space] review problems", numResults: 5)'
# Get subtitles for relevant videos (no video download)
yt-dlp --write-sub --skip-download -o "/tmp/%(id)s" "VIDEO_URL"
```

#### Data Collection Rules

1. **Cast a wide net first** — broad queries on all platforms, then deepen on high-signal sources
2. **Collect REAL QUOTES** — verbatim user complaints are the evidence. Save them with source attribution (platform + URL/thread)
3. **Look for FREQUENCY** — 5 mentions = pattern. 50 = market. Count them.
4. **Note WILLINGNESS TO PAY** — "I'd pay for this" signals are gold. Also "I built a shitty script to do this myself"
5. **Check EXISTING SOLUTIONS** — competitors mentioned, their pricing, what people complain about regarding them
6. **Time-box to 5-10 minutes** of real effort. First 3 searches returning nothing relevant = pivot or flag low signal

---

### Phase 3: Synthesize Findings

Organize everything into a structured analysis:

```
## Complaint Clusters

Group related pain points. For each cluster:
- THEME: Short label (e.g. "Templates feel generic")
- FREQUENCY: How many times users mentioned this across platforms
- QUOTES: 2-3 verbatim user quotes with source attribution
- INTENSITY: Low/Medium/High (how frustrated are they?)

## Existing Solutions Map

- Competitors mentioned and what users say about them
- What people still complain about despite existing solutions (your wedge)
- Pricing signals (what people pay, what they think is too expensive)

## Willingness to Pay Signals

- Direct: "I'd pay for X", "take my money"
- Indirect: "I'm using Y but it's terrible", looking for alternatives
- Behavioral: "I built a shitty version myself" (STRONG signal)
```

---

### Phase 4: Opportunity Assessment

Score and recommend.

```
## Opportunity Score: X/10

### Demand Signal (X/4)
How clear, frequent, and intense is the user pain?

### Competition Gap (X/3)
How well do existing solutions address the pain? Is there a clear wedge?

### Market Accessibility (X/3)
Can you reach these users? Are they willing to pay? Is the market growing?

## Assessment Guide

SCORE | MEANING
------|--------
9-10  | BUILD NOW. Market is screaming for this.
7-8   | Strong opportunity. Minor gaps to verify.
5-6   | Interesting but needs refinement. Pivot or narrow focus.
3-4   | Weak demand or saturated market. Consider alternatives.
1-2   | Dead end. Pick a different problem.

## Recommendation

- **BUILD** — proceed with suggested positioning
- **PIVOT** — 1-2 alternative directions based on complaint patterns
- **SKIP** — alternative problem spaces that showed more signal
```

## Handling Low Signal

If searches return minimal or irrelevant results:
1. Try broader/narrower query variations first
2. Check if the problem space genuinely doesn't exist — low signal IS data
3. Report honestly: "Searched 6 platforms for [X], found minimal complaints. Either this isn't a widespread problem or users call it something else. Suggest rephrasing."

## Quality Checklist

Before presenting the report:
- [ ] Searched at least 3 platforms (ideally 5+)
- [ ] Collected 5+ real user quotes with source attribution
- [ ] Identified 3+ distinct pain point clusters
- [ ] Checked existing solutions and their gaps
- [ ] Provided scored assessment with evidence
- [ ] Gave clear recommendation (BUILD/PIVOT/SKIP)
- [ ] Opened with executive summary (1-3 sentences)
