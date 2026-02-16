# Twitter/X AI Assistant for @NeoFounder

Automated Twitter/X engagement agent built on [MindStudio.ai](https://mindstudio.ai) — monitors relevant posts, scores them with a weighted point system, and generates human-like comments from a Ukrainian crypto/web3/AI founder persona.

---

## How It Works (Step by Step)

The agent has **2 independent processes** running on autopilot:

### Process 1: Auto-Replies (runs every 60 minutes)
<img width="2273" height="944" alt="image" src="https://github.com/user-attachments/assets/b157cdc9-4af4-4a87-aa0d-65044f7c0b96" />


```
Every hour the agent does this:

1. SEARCH     → Searches all of X for fresh tweets (last 1 hour)
               containing keywords: AI, crypto, web3, blockchain,
               startup, SaaS, machine learning, tech hiring, devtools

2. PARSE      → Takes the raw search results and extracts a clean
               list of tweet IDs and URLs (max 10 tweets per cycle)

3. FOR EACH TWEET (one by one, not in parallel):

   3a. SCRAPE POST    → Goes to the tweet URL and grabs full data:
                        text, author name, likes, retweets, views

   3b. SCRAPE PROFILE → Goes to the author's profile and grabs:
                        followers count, bio, verified status,
                        total posts, following count

   3c. ANALYZE & SCORE → AI reads the tweet + author profile and
                         gives scores on 4 criteria:

                         Topic Relevance (x3 weight):
                           AI/ML = 10, Crypto = 9, SaaS = 8,
                           Startups = 8, Unrelated = 0

                         Engagement Quality (x2 weight):
                           50+ likes + discussion = 10,
                           Spam/promo = 0

                         Author Quality (x3 weight):
                           10K+ followers + tech bio = 10,
                           <100 followers = auto-reject

                         Freshness (x1 weight):
                           <30 min old = 10, >3 hours = 2

                         Final score = weighted average out of 10

   3d. DECIDE         → Score >= 6? → Write a reply
                        Score < 6?  → Skip, move to next tweet

                        Hard reject if:
                        - Author has < 100 followers
                        - Tweet not in English or Ukrainian
                        - Contains "DM me", "giveaway", "airdrop"
                        - Author bio is empty + low followers
                        - Tweet is just a link with no text

   3e. GENERATE REPLY → AI writes a casual comment as @NeoFounder:
                        - Matches the language of the original tweet
                        - Adds slang (bro, lol, fr fr, based)
                        - Adds 1-2 intentional typos (goood, definetly)
                        - Varies structure every time (question,
                          reaction, hot take, personal story, hype)
                        - Max 270 characters
                        - Never sounds like AI

   3f. POST REPLY     → Publishes the comment on X as @NeoFounder
                        with @mention of the original author

4. LOG        → Saves results: who was replied to, what was said,
               what score each tweet got
```

### Process 2: Daily Posts (1 tweet per day, different time each day)

```
At a scheduled time (varies by day of week):

1. BRAINSTORM → AI picks a random topic category:
                - Crypto insight (market observation, hot take)
                - Founder life (building, wins/losses, UA perspective)
                - Tech take (AI, web3, dev tools opinion)
                - Personal/humor (memes, self-deprecating jokes)
                - Engagement bait (controversial opinion, question)
                - Motivational (advice, lessons learned)

2. WRITE      → AI writes the tweet as @NeoFounder:
                - Always in English
                - Casual founder style with slang and typos
                - Max 270 characters
                - Different structure each time

3. POST       → Publishes on X

Schedule (Kyiv timezone):
  Monday    → 11:00 AM
  Tuesday   → 3:30 PM
  Wednesday → 10:15 AM
  Thursday  → 5:00 PM
  Friday    → 12:45 PM
  Saturday  → 2:00 PM
  Sunday    → 4:30 PM
```

---

## Scoring System

| Category | Weight | Scale | What It Measures |
|----------|--------|-------|-----------------|
| Topic Relevance | x3 | 0-10 | Does the tweet match AI/crypto/web3/SaaS/startup topics? |
| Engagement Quality | x2 | 0-10 | Likes count, discussion potential, is it spam or real content? |
| Author Quality | x3 | 0-10 | Followers, bio relevance, verified status, real person or bot? |
| Freshness | x1 | 0-10 | How recently was it posted? |

**Formula:** `total = (topic×3 + engagement×2 + author×3 + freshness×1) / 9`

**Threshold:** Only tweets scoring **6+** get a reply. Max **10 replies/day**.

---

## Persona: @NeoFounder

The agent acts as a 28-year-old Ukrainian founder in crypto/web3/AI, currently living abroad:

- **Language:** Posts always in English. Replies match the original tweet's language (EN or UA)
- **Tone:** Casual, energetic, direct, sometimes sarcastic
- **Slang:** bro, lol, fr fr, based, wagmi, lfg + Ukrainian: кайф, жиза, топчик
- **Typos:** Intentional 1-2 per message (goood, definetly, teh)
- **Emoji:** 1-3 per message, never the same pattern twice
- **Hard rules:** Never reveals it's AI, no bullet points, no hashtags, no perfect grammar

---

## File Structure

```
Twitter Assistent/
├── README.md          ← You are here
├── GUIDE.md           ← Full setup guide (Russian)
├── GUIDE_EN.md        ← Full setup guide (English)
├── GUIDE_UA.md        ← Full setup guide (Ukrainian)
└── .cursor/
    ├── rules/         ← Cursor IDE rules
    └── skills/        ← Cursor IDE skills
```

---

## MindStudio Workflow Structure

```
Agent: NeoFounder X Manager
│
├── Auto-Replies.flow (scheduled every 60 min)
│   ├── Start (trigger: every 60 min, timezone: Europe/Kyiv)
│   ├── Search X Posts (keywords: AI, crypto, web3...)
│   ├── Generate Text (parse JSON → list of URLs)
│   └── Run Workflow → Process-Single-Mention.flow (for each tweet)
│
├── Process-Single-Mention.flow (sub-workflow, on-demand)
│   ├── Start (receives: item with tweet URL)
│   ├── Scrape X Post (get full tweet data)
│   ├── Scrape X Profile (get author profile)
│   ├── Generate Text (score tweet: topic + engagement + author + freshness)
│   ├── Logic Block (score >= 6 → reply, else → skip)
│   ├── Generate Text (write human-like reply)
│   ├── Create X Post (publish reply with @mention)
│   └── End (return: score, summary, reply text)
│
└── Daily-Posts.flow (scheduled 1x/day, varying times)
    ├── Start (trigger: different time each day)
    ├── Generate Text (brainstorm tweet idea)
    ├── Generate Text (write final tweet in English)
    ├── Create X Post (publish)
    └── End
```

---

## Tech Stack

- **Platform:** [MindStudio.ai](https://mindstudio.ai) (no-code, free tier)
- **AI Models:** Claude 3.5 Haiku (replies & posts), logic blocks auto-select model
- **X Integration:** Search X Posts, Scrape X Post, Scrape X Profile, Create X Post
- **Cost:** ~$25-35/month (AI tokens + scraping compute)

---

## Anti-Ban Measures

- Intentional typos, slang, and emoji variation in every message
- No hashtags, no bullet points, no AI-like phrasing
- Sequential processing (one reply at a time, not parallel)
- Conservative frequency: 60 min polling, max 10 replies/day
- Night mode: no activity 23:00-07:00 Kyiv time
- Strict quality filter: only engage with real, high-quality accounts

---

## Guides

| File | Language | Contents |
|------|----------|----------|
| [GUIDE.md](GUIDE.md) | Russian | All prompts, block-by-block instructions, variables, models, safety rules |
| [GUIDE_EN.md](GUIDE_EN.md) | English | Same content, translated |
| [GUIDE_UA.md](GUIDE_UA.md) | Ukrainian | Same content, translated |

---

## License

Private project for @NeoFounder. Not for redistribution.
