# Twitter/X AI Assistant for @NeoFounder

Automated Twitter/X engagement agent built on [MindStudio.ai](https://mindstudio.ai) — monitors relevant posts, scores them with a weighted point system, and generates human-like comments from a Ukrainian crypto/web3/AI founder persona.

## What It Does

- **Smart Tweet Discovery** — Searches X for posts about AI, crypto, web3, SaaS, startups every hour
- **Author Profiling** — Scrapes author profiles (followers, bio, verified status) to filter quality accounts
- **Weighted Scoring System** — Rates each tweet on 4 criteria: topic relevance (x3), engagement quality (x2), author quality (x3), freshness (x1)
- **Hard Spam Filters** — Auto-rejects bots, low-follower accounts, non-EN/UA languages, promo/giveaway posts
- **Human-Like Replies** — Generates casual, slang-filled comments with intentional typos and emoji variation to avoid AI detection
- **Scheduled Posting** — Publishes 1 original tweet per day at varying times (different hour each day of the week)
- **Bilingual** — Posts in English, replies match the language of the original tweet (English or Ukrainian)

## Architecture

```
Auto-Replies (every 60 min):
  Search X Posts → Parse JSON → [For Each Tweet] → Scrape Post → Scrape Profile → Score & Filter → Generate Reply → Post

Daily Posts (1/day, varying times):
  Generate Idea → Write Tweet → Post
```

## Scoring System

| Category | Weight | What It Measures |
|----------|--------|-----------------|
| Topic Relevance | x3 | AI/crypto/web3/SaaS/startup alignment |
| Engagement Quality | x2 | Likes, discussion potential, content type |
| Author Quality | x3 | Followers, bio relevance, verified status |
| Freshness | x1 | How recently the tweet was posted |

**Threshold:** Only tweets scoring 6+ out of 10 get a reply. Max 10 replies/day.

## Guides

| File | Language |
|------|----------|
| [GUIDE.md](GUIDE.md) | Russian |
| [GUIDE_EN.md](GUIDE_EN.md) | English |
| [GUIDE_UA.md](GUIDE_UA.md) | Ukrainian |

Each guide contains all prompts (copy-paste ready), block-by-block MindStudio setup instructions, variable references, model recommendations, safety rules, and launch checklist.

## Tech Stack

- **Platform:** MindStudio.ai (no-code, free tier)
- **AI Models:** Claude 3.5 Haiku (replies/posts), Gemini 2.0 Flash (parsing/analysis)
- **X Integration:** Search X Posts, Scrape X Post, Scrape X Profile, Create X Post blocks
- **Cost:** ~$25-35/month (AI tokens + scraping)

## Anti-Ban Measures

- Intentional typos, slang, and emoji variation in every message
- No hashtags, no bullet points, no AI-like phrasing
- Random delays between actions
- Sequential (not parallel) processing
- Conservative frequency: 60 min polling, max 10 replies/day
- Night mode: no activity 23:00-07:00 Kyiv time

## License

Private project for @NeoFounder. Not for redistribution.
