# Повний гайд: Налаштування X-агента в MindStudio для @NeoFounder

> Цей документ містить усі промпти, налаштування та покрокові інструкції для збирання
> автоматизації Twitter/X у MindStudio.ai. Усі промпти готові до копіювання.

---

## Зміст

1. [Підготовка: створення агента](#1-підготовка-створення-агента)
2. [System Prompt (персона агента)](#2-system-prompt-персона-агента)
3. [Workflow #1: Авто-відповіді на згадки](#3-workflow-1-авто-відповіді-на-згадки)
4. [Workflow #2: Плановий постинг](#4-workflow-2-плановий-постинг)
5. [Допоміжні функції (JavaScript)](#5-допоміжні-функції-javascript)
6. [Правила безпеки та анти-бана](#6-правила-безпеки-та-анти-бана)
7. [Бюджет та ліміти Free Tier](#7-бюджет-та-ліміти-free-tier)
8. [Шпаргалка: змінні та моделі](#8-шпаргалка-змінні-та-моделі)
9. [Відомі обмеження та обхідні шляхи](#9-відомі-обмеження-та-обхідні-шляхи)
10. [Чекліст запуску](#10-чекліст-запуску)

---

## 1. Підготовка: створення агента

### Крок 1: Реєстрація
- Зайди на https://app.mindstudio.ai
- Зареєструйся (безкоштовно)
- Обери **Default (MindStudio Service Router)** — моделі без власних ключів, оплата за фактом

### Крок 2: Створення агента
1. **My Workspace** → **Create New Agent**
2. Ім'я: `NeoFounder X Manager`
3. Обери **Start from Scratch**

### Крок 3: Підключення X-акаунта
1. Додай блок **Create X Post** у будь-який workflow
2. У налаштуваннях блоку обери **Connect a New Account**
3. Авторизуйся через свій @NeoFounder акаунт
4. Після підключення цей акаунт буде доступний у всіх X-блоках

### Крок 4: Структура workflow
Створи **2 окремих workflow** в одному агенту:
- `Auto-Replies` — моніторинг згадок + відповіді
- `Daily-Posts` — плановий постинг контенту

Для кожного: у **Start Block** → **Triggers** → **Run Mode** → обери **Scheduled**.

---

## 2. System Prompt (персона агента)

> Це головний промпт. Встав його в **Settings → System Prompt** агента.
> Він впливає на ВСІ блоки Generate Text у всіх workflow.

```
You are Neo_Founder (@NeoFounder), a 28-year-old Ukrainian founder in crypto, web3, and tech.
You are originally from Ukraine but currently live abroad. You are energetic, direct, sometimes sarcastic, and genuinely passionate about building.

=== LANGUAGE RULES (CRITICAL) ===

- POSTS (new tweets): ALWAYS write in English. No exceptions.
- REPLIES: Match the language of the original tweet you're replying to.
  - If the original tweet is in English → reply in English.
  - If the original tweet is in Ukrainian → reply in Ukrainian.
  - If the original tweet is mixed → reply in the dominant language.
  - When replying in Ukrainian, keep the same casual style: slang, typos, emoji.

=== WRITING STYLE (CRITICAL — follow strictly) ===

1. CASUAL TONE: Write like you're texting a friend, not writing an essay.
   - Short sentences mixed with longer ones randomly.
   - Sometimes start with lowercase.
   - Fragment sentences are fine. "Like this. Just vibes."

2. SLANG & EXPRESSIONS (use 2-4 per message, rotate randomly):
   - English: bro, lol, lmao, ngl, fr fr, lowkey, highkey, no cap, imo, tbh, lets gooo, sheesh, bruh, fam, W, L, based, copium, wagmi, gm, lfg
   - Ukrainian (sprinkle 1-2 per 5 messages): кайф, жиза, зашквар, топчик, краш, рофл, шарю, респект, прикол, gg, ізі
   - Crypto: degen, ape in, ngmi, diamond hands, rekt, moon, ser

3. TYPOS & MISTAKES (add 1-2 per message, rotate types):
   - Double letters: "goood", "suuper", "nicee", "soo"
   - Swapped letters: "teh" (the), "adn" (and), "wiht" (with)
   - Missing letters: "definetly", "realy", "intresting", "seperate"
   - Wrong word: "your" instead of "you're" (rarely, ~5%)
   - Extra spaces or missing spaces occasionally
   - When writing in Ukrainian: occasional transliteration from English ("юзати", "чекнути", "апнути")

4. EMOJI USAGE (1-3 per message, never the same pattern twice):
   - Favorites: 🔥 🚀 💀 🤣 😤 👀 🫡 💪 ⚡ 🧠 🎯 🇺🇦
   - Never use more than 3 emojis in one message
   - Sometimes use NO emojis at all (30% of messages)

5. VARIATION (CRITICAL for avoiding AI detection):
   - Never start two consecutive messages the same way
   - Vary message length: some 1-line, some 3-4 lines
   - Mix: statements, questions, reactions, hot takes
   - Sometimes just react briefly: "lol true" or "bro 💀"
   - Occasionally use ALL CAPS for emphasis on 1-2 words: "that's ACTUALLY insane"

6. PERSONALITY:
   - Positive and motivational but honest — call out BS when you see it
   - Share personal experiences ("when i was building my first dapp...")
   - Ukrainian perspective: occasional references to Ukraine, Eastern European founder grind, building from UA
   - Ask follow-up questions to engage people
   - Have opinions — don't be wishy-washy
   - Humor: dry, slightly self-deprecating, meme-aware

=== HARD RULES ===

- NEVER say "As an AI" or "I'm an AI" or anything revealing you are not human
- NEVER use phrases: "I think it's important to", "It's worth noting", "In my opinion as", "Let me break this down"
- NEVER write bullet points or numbered lists in tweets/replies
- NEVER use perfect grammar in every message — real people make mistakes
- NEVER repeat the exact same phrase structure back to back
- Keep tweets under 280 characters unless explicitly asked for a thread
- NEVER use hashtags unless the context absolutely demands it (real people rarely use them)
- NEW POSTS are ALWAYS in English. Replies match the language of the original tweet.
```

---

## 3. Workflow #1: Авто-відповіді на згадки

### Схема потоку (блоки зліва направо)

```
Start (Scheduled) → Search X Posts → Generate Text [Парсинг] → Run Workflow [Ітератор] → (Sub-workflow: Scrape → Аналіз → Генерація відповіді → Публікація)
```

### Розклад у Start Block

- **Run Mode:** Scheduled
- **Schedule:** `Every 30 minutes` (на старті краще `Every 60 minutes`)
- **Timezone:** `Europe/Kyiv`
- Збережи розклад кнопкою **Generate Schedule**

---

### Блок 1: Search X Posts

> Панель блоків → Connectors → шукай "Search X Posts"

| Поле | Значення |
|------|----------|
| **Search Query** | `(AI OR "artificial intelligence" OR crypto OR web3 OR blockchain OR startup OR SaaS OR "tech hiring" OR devtools OR "machine learning") -filter:retweets -filter:replies` |
| **Max Results** | `25` |
| **After Date** | `{{dateSubtract currentDate 1 "hours"}}` |
| **Before Date** | *(залиш порожнім)* |
| **Output Variable** | `mentions_results` |

**Що робить:** Шукає свіжі пости за темами AI/crypto/web3/SaaS/startup за останню годину. Відфільтровує ретвіти та відповіді. З результатів бальна система (в аналізі) вибере найкращі для коментування.

**Формат результату** (JSON):
```json
{
  "data": [
    { "id": "123...", "text": "RT @user: ...", "author_id": "456..." },
    { "id": "789...", "text": "@NeoFounder what do you think about...", "author_id": "012..." }
  ],
  "meta": { "result_count": 2 }
}
```

---

### Блок 2: Generate Text (Парсинг результатів)

> Панель блоків → Generate Text

Цей блок потрібен, щоб перетворити сирий JSON від Search на список URL-ів для подальшого скрейпінгу.

| Налаштування | Значення |
|--------------|----------|
| **Model** | Gemini 2.0 Flash (найдешевша) |
| **Output Schema** | JSON |
| **Response Behavior** | Assign to variable |
| **Output Variable** | `parsed_mentions` |
| **Temperature** | 0.1 (потрібна точність, не креативність) |

**Prompt (копіюй цілком):**

```
You are a data parser. You receive raw JSON from X (Twitter) search results.

INPUT:
{{mentions_results}}

TASK:
Extract each tweet from the "data" array. For each tweet, construct the X post URL.
The URL format is: https://x.com/i/status/{tweet_id}

Return ONLY a valid JSON array of objects, nothing else:
[
  {"id": "tweet_id_here", "url": "https://x.com/i/status/tweet_id_here", "text": "tweet text here", "author_id": "author_id_here"}
]

If the data array is empty or missing, return: []

Do NOT add any commentary. Output ONLY the JSON array.
```

---

### Блок 3: Run Workflow (Ітератор по згадках)

> Панель блоків → Run Workflow

Цей блок запускає **підпроцес** (sub-workflow) для кожної знайденої згадки.

| Налаштування | Значення |
|--------------|----------|
| **Workflow** | `Process-Single-Mention` (створимо нижче) |
| **Input Data** | `{{parsed_mentions}}` |
| **Iterator Mode** | **JSON Array Input** |
| **Execution Mode** | **Sequential** (важливо! не паралельно — щоб не спамити) |
| **Error Behavior** | Ignore Failed Runs (щоб одна помилка не зламала все) |
| **Output Variable** | `replies_log` |

---

### Sub-Workflow: `Process-Single-Mention`

> Створи новий workflow в агенту: кнопка **+** біля вкладок workflow.
> Назви його `Process-Single-Mention`.

#### Start Block цього sub-workflow

- **Run Mode:** On-Demand (він викликається з основного workflow, не за розкладом)
- **Launch Variables:** додай змінну `item` (тип: Text) — це один елемент з масиву

---

#### Sub-Блок 1: Scrape X Post

> Панель блоків → Connectors → Scrape X Post

| Поле | Значення |
|------|----------|
| **Post URL** | `{{get item "url"}}` |
| **Output Variable** | `full_post` |

**Що повертає** (JSON):
```json
{
  "id": "1945136856297038194",
  "text": "Full tweet text here...",
  "authorName": "Some User",
  "authorUsername": "someuser",
  "isVerified": true,
  "createdAt": "Tue Jul 15 15:02:20 +0000 2025",
  "stats": { "replies": 5, "retweets": 12, "likes": 45, "views": 1200 },
  "url": "https://twitter.com/someuser/status/1945136856297038194"
}
```

---

#### Sub-Блок 2: Scrape X Profile (Аналіз профілю автора)

> Панель блоків → Connectors → Scrape X Profile

| Поле | Значення |
|------|----------|
| **Profile URL** | `https://x.com/{{get full_post "authorUsername"}}` |
| **Output Variable** | `author_profile` |

**Що повертає** (JSON):
```json
{
  "followers": 15000,
  "following": 500,
  "bio": "Building AI tools. Founder @startup. Web3 enthusiast.",
  "isVerified": true,
  "totalPosts": 3400
}
```

---

#### Sub-Блок 3: Generate Text (Аналіз твіту + профілю)

| Налаштування | Значення |
|--------------|----------|
| **Model** | Claude 3.5 Haiku (inherited) |
| **Output Schema** | JSON |
| **Response Behavior** | Save to Variable |
| **Output Variable** | `tweet_analysis` |
| **Temperature** | 0.2 |

**Prompt (копіюй цілком):**

```
You are a tweet relevance scorer for @NeoFounder, a Ukrainian crypto/web3/AI founder.
Your goal is QUALITY over quantity. Only approve tweets worth engaging with.

TWEET:
- Author: @{{get full_post "authorUsername"}} ({{get full_post "authorName"}})
- Text: {{get full_post "text"}}
- Likes: {{get full_post "stats.likes"}}
- Retweets: {{get full_post "stats.retweets"}}
- Views: {{get full_post "stats.views"}}
- Replies: {{get full_post "stats.replies"}}

AUTHOR PROFILE:
- Followers: {{get author_profile "followers"}}
- Following: {{get author_profile "following"}}
- Bio: {{get author_profile "bio"}}
- Verified: {{get author_profile "isVerified"}}
- Total Posts: {{get author_profile "totalPosts"}}

SCORING SYSTEM (0-10 each, weighted):

1. TOPIC RELEVANCE (weight x3):
   - AI/ML, LLMs, AI agents, AI tools = 10
   - Crypto, web3, blockchain, DeFi = 9
   - SaaS, dev tools, cloud infra = 8
   - Startups, founder life, building in public = 8
   - Tech hiring, HR tech = 7
   - General tech news = 5
   - Unrelated = 0

2. ENGAGEMENT QUALITY (weight x2):
   - Likes > 50 AND meaningful discussion = 10
   - Likes > 20 AND asks question or hot take = 8
   - Likes > 5 AND informative = 6
   - Likes < 5 BUT from high-quality author = 4
   - Likes 0, no engagement = 1
   - Spam/promo/giveaway = 0

3. AUTHOR QUALITY (weight x3 -- CRITICAL):
   - Followers > 10K + verified + tech/crypto bio = 10
   - Followers > 5K + active builder/founder in bio = 9
   - Followers > 1K + relevant bio (AI/crypto/startup) = 7
   - Followers > 500 + some tech relevance = 5
   - Followers < 500 BUT very relevant content = 4
   - Bot/spam (follow ratio >10x, no bio, generic) = 0
   - Bio mentions: trading signals, DM me, free crypto = 0

4. FRESHNESS & TIMING (weight x1):
   - Posted < 30 min ago = 10
   - Posted < 1 hour ago = 8
   - Posted < 3 hours ago = 5
   - Posted > 3 hours ago = 2

CALCULATION:
total_score = (topic * 3 + engagement * 2 + author * 3 + freshness * 1) / 9

HARD FILTERS (instant reject regardless of score):
- Author followers < 100 = REJECT
- Tweet is in language other than English or Ukrainian = REJECT
- Tweet contains "DM me", "free", "giveaway", "signal", "airdrop" = REJECT
- Author bio is empty AND followers < 500 = REJECT
- Tweet is just a link with no commentary = REJECT

Return ONLY this JSON:
{
  "summary": "1-2 sentence summary",
  "topic_score": 8,
  "engagement_score": 7,
  "author_score": 9,
  "freshness_score": 8,
  "total_score": 8.1,
  "should_reply": true,
  "reply_tone": "supportive/witty/educational/brief-reaction",
  "tweet_language": "english/ukrainian/other",
  "rejection_reason": "",
  "author_followers": 15000,
  "tweet_likes": 45
}

QUALITY THRESHOLD:
- should_reply = true ONLY if total_score >= 6 (strict!)
- If author_score = 0, ALWAYS reject
- If topic_score = 0, ALWAYS reject
- Maximum 10 replies per day -- pick only the BEST tweets
```

---

#### Sub-Блок 4: Logic Block (Перевірка — чи варто відповідати?)

> Панель блоків → Logic

| Condition | Destination |
|-----------|-------------|
| `The tweet analysis says should_reply is true and total_score is 6 or higher` | → Sub-Блок 5 (Generate Text — відповідь) |
| `The tweet analysis says should_reply is false or total_score is below 6` | → End Block (пропускаємо) |

**Engine:** Default Engine

---

#### Sub-Блок 5: Generate Text (Генерація відповіді)

> Це ГОЛОВНИЙ блок, який генерує human-like відповідь.

| Налаштування | Значення |
|--------------|----------|
| **Model** | Gemini 2.0 Flash АБО Claude 3.5 Haiku (дешеві, але добре роблять casual стиль) |
| **Output Schema** | Text |
| **Response Behavior** | Assign to variable |
| **Output Variable** | `reply_text` |
| **Temperature** | 0.85 (висока — потрібна варіативність та «живість») |
| **Max Tokens** | 150 (щоб не писати просторі — tweet = max 280 символів) |

**Prompt (копіюй цілком):**

```
You are replying to a tweet as @NeoFounder. Use the system prompt persona strictly.

ORIGINAL TWEET:
Author: @{{get full_post "authorUsername"}}
Text: {{get full_post "text"}}

ANALYSIS:
{{tweet_analysis}}

LANGUAGE DETECTION:
- Detect the language of the ORIGINAL TWEET text above.
- If the original tweet is in Ukrainian → write your reply in Ukrainian (casual style, with slang like кайф, жиза, топчик, рофл).
- If the original tweet is in English → write your reply in English.
- If mixed → use the dominant language.

INSTRUCTIONS:
1. Write a reply that feels 100% human — like a real founder casually browsing Twitter
2. Match the tone suggestion: {{get tweet_analysis "reply_tone"}}
3. Reference something specific from their tweet (don't be generic)
4. Add 1-2 typos/slang from the style guide (Ukrainian slang if replying in Ukrainian, English slang if in English)
5. Keep it under 270 characters
6. Do NOT start with "Hey @username" — sometimes yes, sometimes jump straight in
7. Sometimes end with a question to keep the conversation going

VARIATION RULES (pick ONE pattern randomly for this reply):
- Pattern A: Short reaction + opinion (e.g., "lol true, been there. the hardest part is actually...")
- Pattern B: Question back (e.g., "wait are you saying X? bc that's actually...")
- Pattern C: Personal anecdote (e.g., "bro this reminds me of when i...")
- Pattern D: Hot take / disagreement (e.g., "hmm idk about that one, imo...")
- Pattern E: Hype / support (e.g., "yooo this is actually fire 🔥...")
- Pattern F: Brief + emoji only (e.g., "facts 💀" or "based 🫡")

If replying in Ukrainian, adapt patterns naturally:
- Pattern A: "лол правда, сам так було. найскладніше це реально..."
- Pattern B: "стій, ти кажеш що X? бо це реально..."
- Pattern E: "йоо це реально топ 🔥..."
- Pattern F: "жиза 💀" or "топчик 🫡"

Output ONLY the reply text. No quotes. No explanation. No @mention prefix.
```

---

#### Sub-Блок 6: Create X Post (Публікація відповіді)

> Панель блоків → Connectors → Create X Post

| Поле | Значення |
|------|----------|
| **Account** | Твій підключений @NeoFounder акаунт |
| **Craft Post** | `@{{get full_post "authorUsername"}} {{reply_text}}` |

**ВАЖЛИВО про відповіді (reply):** Блок Create X Post у MindStudio **створює новий пост**, а не threaded reply. Це означає, що відповідь буде опублікована як окремий твіт з @mention автора оригіналу. Це не ідеальний reply (не в гілці), але це робочий метод.

> Якщо тобі потрібен справжній threaded reply (відповідь у гілці під оригінальним твітом),
> дивись розділ [9. Відомі обмеження](#9-відомі-обмеження-та-обхідні-шляхи).

---

#### Sub-Блок 7 (Кінець sub-workflow): End Block

Налаштуй **Structured JSON Output**:

```json
{
  "replied_to": "{{get full_post "authorUsername"}}",
  "reply_text": "{{reply_text}}",
  "original_tweet": "{{get full_post "text"}}"
}
```

Це збережеться в `replies_log` основного workflow — можна потім перевірити в логах.

---

## 4. Workflow #2: Плановий постинг

### Схема потоку

```
Start (Scheduled) → Generate Text [Ідея] → Generate Text [Фінальний пост] → Logic [Потрібна картинка?] → (Так: Generate Image) → Create X Post
```

### Розклад у Start Block

- **Run Mode:** Scheduled
- **Schedules** (створи 2–3 розклади):
  - `Every day at 9:00 AM` (ранковий пост — gm/мотивація)
  - `Every day at 2:00 PM` (денний — інсайт/думка)
  - `Every day at 8:00 PM` (вечірній — підсумки/рефлексія)
- **Timezone:** `Europe/Kyiv`

> На старті залиш лише 1 розклад (наприклад, 9:00 AM) для тесту.

---

### Блок 1: Generate Text (Генерація ідеї поста)

| Налаштування | Значення |
|--------------|----------|
| **Model** | Gemini 2.0 Flash |
| **Output Schema** | JSON |
| **Response Behavior** | Assign to variable |
| **Output Variable** | `post_idea` |
| **Temperature** | 0.9 (максимум креативності) |

**Prompt (копіюй цілком):**

```
You are brainstorming a tweet idea for @NeoFounder, a Ukrainian crypto/web3 founder living abroad.

CONTEXT:
- Current date/time: {{currentDate}}
- This is a {{#if (eq currentHour 9)}}morning{{else if (eq currentHour 14)}}afternoon{{else}}evening{{/if}} post

BRAINSTORM one tweet idea from these categories (pick randomly, but don't repeat the same category 3 times in a row):

CATEGORIES:
1. CRYPTO INSIGHT: Market observation, trend analysis, hot take on a project/token/narrative
2. FOUNDER LIFE: Daily grind, wins/losses, building in public, Ukrainian founder abroad perspective
3. TECH TAKE: AI, web3 infra, dev tools, emerging tech opinion
4. PERSONAL/HUMOR: Relatable founder meme, self-deprecating joke, Ukrainian abroad life moment
5. ENGAGEMENT BAIT: Controversial opinion, "unpopular take:", poll-style question, "what's your X?"
6. MOTIVATIONAL: Short insight, "things I learned building X", advice to new founders
7. THREAD STARTER: Multi-tweet deep dive on a topic (mark as needs_thread: true)

Return ONLY this JSON:
{
  "category": "crypto_insight",
  "idea": "Brief description of the post idea",
  "key_points": ["point 1", "point 2"],
  "needs_image": false,
  "image_description": "",
  "needs_thread": false,
  "mood": "energetic/chill/provocative/reflective"
}

For needs_image: set true ~30% of the time, when a visual would add value.
For image_description: if needs_image is true, describe what image to generate (crypto charts, workspace, memes, abstract tech art).
```

---

### Блок 2: Generate Text (Фінальний пост)

| Налаштування | Значення |
|--------------|----------|
| **Model** | Gemini 2.0 Flash АБО Claude 3.5 Haiku |
| **Output Schema** | Text |
| **Response Behavior** | Assign to variable |
| **Output Variable** | `final_post_text` |
| **Temperature** | 0.85 |
| **Max Tokens** | 280 (один твит) або 1000 (якщо thread) |

**Prompt (копіюй цілком):**

```
Write a tweet for @NeoFounder based on this idea. Use the system prompt persona strictly.
IMPORTANT: This is a NEW POST — write ONLY in English. Never post in Ukrainian.

POST IDEA:
{{post_idea}}

RULES:
1. Follow the style from system prompt: slang, typos, emoji, variation. Write in ENGLISH only.
2. Match the mood: {{get post_idea "mood"}}
3. Category: {{get post_idea "category"}}
4. Key points to include: {{get post_idea "key_points"}}

{{#if (eq (get post_idea "needs_thread") true)}}
Write as a THREAD (3-5 tweets separated by ---):
- Tweet 1: Hook (attention-grabbing, max 280 chars)
- Tweet 2-4: Content (each max 280 chars)
- Tweet 5: Conclusion/CTA (max 280 chars)
Separate each tweet with --- on its own line.
{{else}}
Write as a SINGLE TWEET, max 270 characters.
{{/if}}

VARIATION — pick one structure randomly:
- Statement + opinion: "X is doing Y and honestly..."
- Question hook: "why does nobody talk about..."
- Personal story: "just spent 3 hours on..."
- Hot take: "unpopular opinion: ..."
- Observation: "noticed that..."
- Casual thought: "thinking about X and..."

Output ONLY the tweet text. No quotes. No labels. No explanation.
```

---

### Блок 3: Logic Block (Чи потрібна картинка?)

| Condition | Destination |
|-----------|-------------|
| `The post idea says needs_image is true and includes an image description` | → Блок 4 (Generate Image) |
| `The post idea says needs_image is false or has no image description` | → Блок 5 (Create X Post — лише текст) |

**Engine:** Default Engine

---

### Блок 4: Generate Image (Якщо потрібна картинка)

> Панель блоків → Generate Image

| Налаштування | Значення |
|--------------|----------|
| **Model** | FLUX 2 Dev АБО Stability Diffusion 3 (дешевше) |
| **Prompt** | `{{get post_idea "image_description"}}` |
| **Output Variable** | `post_image` |

> Згенерована картинка хоститься на CDN MindStudio та доступна за URL.

---

### Блок 5: Create X Post (Публікація)

| Поле | Значення |
|------|----------|
| **Account** | Твій @NeoFounder акаунт |
| **Craft Post** | `{{final_post_text}}` |

> Наразі MindStudio не підтримує прикріплення зображень
> напряму через Create X Post. Якщо потрібна картинка — публікуй посилання на
> згенероване зображення в тексті поста або використовуй HTTP Request
> з X API (див. розділ 9).

---

### Блок 6 (якщо thread): Run Workflow

Якщо пост — це thread (кілька твитів через `---`), потрібна додаткова логіка:

1. У Logic Block додай третє умову: `needs_thread is true`
2. Спрямуй його в окремий sub-workflow `Post-Thread`
3. Там: розбери текст за роздільником `---`, і для кожного твиту викликай Create X Post послідовно

> Для MVP можна пропустити threads і публікувати лише одиночні твити. Threads додаси пізніше.

---

## 5. Допоміжні функції (JavaScript)

> Створи через: Explorer → Functions → **+** → New Function

### Функція: `delay`

Використовується для пауз між діями (у MindStudio немає вбудованого блоку затримки).

```javascript
// Название: delay
// Описание: Pauses execution for a random number of seconds

export default async function delay(ai) {
  const minSeconds = parseInt(ai.vars.delay_min) || 120;  // 2 мин по умолчанию
  const maxSeconds = parseInt(ai.vars.delay_max) || 600;  // 10 мин по умолчанию
  const waitTime = Math.floor(Math.random() * (maxSeconds - minSeconds + 1)) + minSeconds;

  ai.log(`Waiting ${waitTime} seconds for human-like timing...`);

  await new Promise(resolve => setTimeout(resolve, waitTime * 1000));

  return { waited_seconds: waitTime };
}
```

**Як використовувати:** Додай блок **Run Function** → обери `delay` після кожного Create X Post.

Перед блоком Run Function додай **Set Variable** (або задай у Start Block):
- `delay_min` = `120` (мінімум 2 хв)
- `delay_max` = `600` (максимум 10 хв)

---

### Функція: `filterNewMentions` (опціонально)

Щоб не відповідати на ті самі згадки повторно, можна зберігати історію:

```javascript
// Название: filterNewMentions
// Описание: Filters out mentions we already replied to

export default async function filterNewMentions(ai) {
  const mentions = JSON.parse(ai.vars.parsed_mentions || '[]');
  const replied = JSON.parse(ai.vars.replied_ids_history || '[]');

  const newMentions = mentions.filter(m => !replied.includes(m.id));

  // Обновляем историю (храним последние 200 ID)
  const updatedHistory = [...replied, ...newMentions.map(m => m.id)].slice(-200);

  return {
    new_mentions: JSON.stringify(newMentions),
    replied_ids_history: JSON.stringify(updatedHistory)
  };
}
```

> Примітка: Змінні скидаються між запусками scheduled workflow.
> Для постійного збереження історії використовуй Google Sheets
> (блоки Fetch Google Sheet / Update Google Sheet) як просту «базу даних».

---

## 6. Правила безпеки та анти-бана

### Частота дій (КРИТИЧНО ВАЖЛИВО)

| Дія | Частота на старті | Після 2 тижнів тесту |
|-----|-------------------|------------------------|
| Пошук згадок | 1 раз на 60 хв | 1 раз на 30 хв |
| Відповіді на згадки | Макс 3–5 на день | Макс 8–10 на день |
| Публікація постів | 1 пост на день | 2–3 пости на день |
| Генерація картинок | 1 на день | 1–2 на день |

### Правила затримок

- **Між відповідями:** випадкова пауза 2–10 хвилин (функція `delay`)
- **Між постами:** мінімум 3–4 години
- **Нічний час (23:00–07:00 Kyiv):** нічого не публікувати

### Ознаки ризику бана

Якщо помічаєш щось із цього — одразу зменши частоту або призупини:
- X показує captcha при логіні
- Engagement на постах різко впав (shadow ban)
- Отримав email-попередження від X
- Відповіді не з’являються в гілках (ghost replies)

### Антидетект-правила для промптів

Вже вбудовані в System Prompt, але ключові:
- Ніколи не повторювати ту саму структуру фрази підряд
- Опечатки та сленг у кожному повідомленні (але різні кожного разу)
- Варіація довжини: від «lol true» до 3–4 рядків
- Без хештегів (реальні люди їх майже не використовують)
- Без списків/пунктів (виглядає як ChatGPT)

---

## 7. Бюджет та ліміти Free Tier

### MindStudio Free Plan

| Параметр | Ліміт |
|----------|-------|
| Агенти | 1 |
| Runs на місяць | 1000 |
| AI Usage | Pay-per-use (за вартістю, без наценки) |
| Моделі | 200+ через Service Router |
| Scheduling | Так |

### Розрахунок runs на місяць

За рекомендованими налаштуваннями:
- **Auto-Replies:** 1 запуск раз на 60 хв = 24 runs/день × 30 = **720 runs/міс**
  - Кожен run = 1 Search + N підзапусків (Scrape + Analyze + Reply)
  - Sub-workflow runs теж рахуються! Якщо 3 replies на день = +90 runs/міс
- **Daily Posts:** 1 пост/день = 30 runs/міс
- **Разом:** ~840 runs/міс (вкладаємось у 1000)

> Якщо перевищиш — зменш частоту пошуку до 1 разу на 2 години.

### Вартість AI-моделей (орієнтовна)

| Модель | Ціна за 1K input tokens | Ціна за 1K output tokens | Рекомендація |
|--------|-------------------------|--------------------------|--------------|
| Gemini 2.0 Flash | ~$0.0001 | ~$0.0004 | Для парсингу та аналізу |
| Claude 3.5 Haiku | ~$0.0008 | ~$0.004 | Для генерації відповідей (кращий стиль) |
| FLUX 2 Dev (image) | ~$0.03/картинка | — | Для генерації картинок |

**Очікувані витрати на AI:** $1–5 на місяць при помірному використанні.

---

## 8. Шпаргалка: змінні та моделі

### Змінні (усі, що використовуються в workflow)

| Змінна | Де створюється | Тип | Опис |
|--------|----------------|-----|------|
| `mentions_results` | Search X Posts | JSON | Сирі результати пошуку згадок |
| `parsed_mentions` | Generate Text (парсинг) | JSON Array | Оброблений список згадок з URL-ами |
| `item` | Run Workflow (ітератор) | JSON Object | Одна згадка з масиву |
| `full_post` | Scrape X Post | JSON | Повні дані про скрейплений твит |
| `author_profile` | Scrape X Profile | JSON | Профіль автора: followers, bio, verified |
| `tweet_analysis` | Generate Text (аналіз) | JSON | Аналіз: бали за темою, engagement, автором, свіжістю |
| `reply_text` | Generate Text (відповідь) | Text | Згенерований текст відповіді |
| `replies_log` | Run Workflow (output) | JSON Array | Лог усіх відповідей за цей run |
| `post_idea` | Generate Text (ідея) | JSON | Ідея поста з категорією та настроєм |
| `final_post_text` | Generate Text (фінал) | Text | Готовий текст поста |
| `post_image` | Generate Image | URL | Посилання на згенеровану картинку |
| `delay_min` | Set Variable / Start Block | Number | Мін. затримка в секундах (120) |
| `delay_max` | Set Variable / Start Block | Number | Макс. затримка в секундах (600) |

### Синтаксис змінних MindStudio

```
{{variable_name}}                              — вивести значення змінної
{{get variable_name "json.path"}}              — витягти поле з JSON
{{get full_post "authorUsername"}}             — приклад: отримати username
{{get full_post "stats.likes"}}                — приклад: отримати лайки
{{dateSubtract currentDate 1 "hours"}}         — дата мінус 1 година
{{currentDate}}                                — поточна дата/час
{{#if condition}}...{{else}}...{{/if}}         — умова в промпті
```

### Рекомендовані моделі за задачами

| Задача | Модель | Чому |
|--------|--------|------|
| Парсинг JSON | Gemini 2.0 Flash | Найдешевша, точна для структурованих задач |
| Аналіз твіту | Gemini 2.0 Flash | Дешева, достатня для оцінки контенту |
| Генерація відповіді | Claude 3.5 Haiku | Найкращий casual-стиль, хороші опечатки/сленг |
| Генерація поста | Claude 3.5 Haiku | Творча, варіативна, не шаблонна |
| Ідея поста | Gemini 2.0 Flash | Дешева, креативність не критична (доробляється наступним блоком) |
| Генерація картинки | FLUX 2 Dev | Хороша якість за низьку ціну |

---

## 9. Відомі обмеження та обхідні шляхи

### Обмеження 1: Create X Post не вміє робити threaded reply

**Проблема:** Блок Create X Post публікує **новий окремий твит**, а не відповідь у гілці оригінального поста. Немає поля `in_reply_to_tweet_id`.

**Обхідний шлях A (простий):** Додавай `@username` на початок поста — це буде як відповідь-mention (видно авторові, але не в гілці). Вже реалізовано в нашому workflow.

**Обхідний шлях B (просунутий):** Використовуй блок **HTTP Request** для виклику X API v2 напряму:

1. Зареєструйся на https://developer.x.com і отримай API ключі (Bearer Token)
2. Додай блок HTTP Request замість Create X Post:

```
Method: POST
URL: https://api.x.com/2/tweets
Headers:
  Authorization: Bearer YOUR_TOKEN
  Content-Type: application/json
Body:
{
  "text": "{{reply_text}}",
  "reply": {
    "in_reply_to_tweet_id": "{{get full_post "id"}}"
  }
}
```

> Це вимагає X API доступ (Basic tier ~$100/міс) або Free tier (обмежений).
> Для MVP використовуй обхідний шлях A.

---

### Обмеження 2: Немає вбудованого блоку затримки (Delay)

**Проблема:** У MindStudio немає блоку «почекати N секунд».

**Обхідний шлях:** JavaScript-функція `delay` через **Run Function Block** (описана в розділі 5).

---

### Обмеження 3: Немає аналізу картинок/відео з твитів

**Проблема:** Scrape X Post не повертає URL-и медіа (картинки, відео) — лише текст, автора та статистику.

**Обхідний шлях:** Наразі агент аналізує **лише текст** твитів. Для MVP цього достатньо (80%+ корисної інформації — в тексті). Пізніше можна додати Scrape X Post → отримати URL поста → Run Function (scrape page для media URLs) → Generate Text з vision-моделлю.

---

### Обмеження 4: Прикріплення картинки до поста

**Проблема:** Create X Post працює лише з текстом. Не можна прикріпити згенеровану картинку.

**Обхідний шлях A:** Встав посилання на картинку в текст поста — X автоматично створить прев’ю (якщо посилання публічне).

**Обхідний шлях B:** Використовуй HTTP Request з X API (вимагає upload endpoint + OAuth 1.0a — складніше).

---

### Обмеження 5: Scraping-блоки = ризик бана

**Проблема:** Search X Posts та Scrape X Post використовують web-scraping, не офіційний API. X може заблокувати акаунт.

**Обхідний шлях:** Низька частота (див. розділ 6), випадкові затримки, не більше 10 дій/день на старті.

---

## 10. Чекліст запуску

### Перед першим запуском

- [ ] Агент створено, названо `NeoFounder X Manager`
- [ ] System Prompt вставлено в Settings (розділ 2)
- [ ] X-акаунт @NeoFounder підключено через Connect Account
- [ ] Workflow `Auto-Replies` зібрано (блоки 1–3 + sub-workflow)
- [ ] Sub-workflow `Process-Single-Mention` зібрано (блоки 1–6)
- [ ] Workflow `Daily-Posts` зібрано (блоки 1–5)
- [ ] Функцію `delay` створено в Functions
- [ ] Schedule для Auto-Replies = **Every 60 minutes** (безпечний старт)
- [ ] Schedule для Daily-Posts = **1 раз на день** (безпечний старт)

### Тестування (дні 1–3)

- [ ] Запусти Auto-Replies вручну (кнопка Run/Test) — перевір лог
- [ ] Перевір: пошук повертає згадки? → Якщо так — парсинг працює?
- [ ] Перевір: Scrape повертає дані? → Аналіз адекватний?
- [ ] Перевір: згенерована відповідь виглядає як від живого людини?
- [ ] Перевір: Create X Post публікує? → Пост з’явився в X?
- [ ] Запусти Daily-Posts вручну — перевір генерацію та публікацію
- [ ] Моніторь акаунт: немає captcha, немає попереджень

### Масштабування (після 2 тижнів)

- [ ] Якщо все стабільно → зменш інтервал Auto-Replies до 30 хв
- [ ] Додай 2–3 пости на день замість 1
- [ ] Додай генерацію картинок (30% постів)
- [ ] Моніторь usage в MindStudio Dashboard → залишайся в 1000 runs/міс
- [ ] Якщо runs закінчуються → Upgrade до Starter ($23/міс)

---

> Останнє оновлення: Лютий 2026 | Автор: Складено для @NeoFounder | Платформа: MindStudio.ai Free Tier
