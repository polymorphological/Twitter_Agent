# Twitter/X ШІ-Асистент для @NeoFounder

Автоматизований агент взаємодії в Twitter/X на базі [MindStudio.ai](https://mindstudio.ai) — відстежує релевантні пости, оцінює їх за зваженою системою балів і генерує природні коментарі від персони українського засновника в крипто/web3/AI.

---

## Як це працює (крок за кроком)

Агент має **2 незалежні процеси**, що працюють у автопілоті:

### Процес 1: Авто-відповіді (запуск кожні 60 хвилин)

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

### Процес 2: Щоденні пости (1 твіт на день, різний час)

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

## Система оцінки

| Категорія | Вага | Шкала | Що вимірює |
|-----------|------|-------|------------|
| Релевантність теми | x3 | 0-10 | Чи відповідає твіт темам AI/crypto/web3/SaaS/стартап? |
| Якість залученості | x2 | 0-10 | Кількість лайків, потенціал обговорення, спам чи реальний контент? |
| Якість автора | x3 | 0-10 | Підписники, релевантність біо, верифікація, реальна людина чи бот? |
| Свіжість | x1 | 0-10 | Наскільки нещодавно опубліковано? |

**Формула:** `total = (topic×3 + engagement×2 + author×3 + freshness×1) / 9`

**Поріг:** Відповідь отримують лише твіти з оцінкою **6+**. Максимум **10 відповідей на день**.

---

## Персона: @NeoFounder

Агент діє як 28-річний український засновник у крипто/web3/AI, який зараз живе за кордоном:

- **Мова:** Пости завжди англійською. Відповіді відповідають мові оригінального твіта (EN або UA)
- **Тон:** Неформальний, енергійний, прямий, іноді саркастичний
- **Сленг:** bro, lol, fr fr, based, wagmi, lfg + українські: кайф, жиза, топчик
- **Помилки:** Навмисні 1-2 на повідомлення (goood, definetly, teh)
- **Емодзі:** 1-3 на повідомлення, ніколи однаковий набір двічі
- **Строгі правила:** Ніколи не видає, що це ШІ; без маркованих списків, хештегів і ідеальної граматики

---

## Структура файлів

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

## Структура MindStudio workflow

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

## Технічний стек

- **Платформа:** [MindStudio.ai](https://mindstudio.ai) (no-code, безкоштовний тариф)
- **AI-моделі:** Claude 3.5 Haiku (відповіді та пости), блоки логіки автоматично обирають модель
- **Інтеграція з X:** Search X Posts, Scrape X Post, Scrape X Profile, Create X Post
- **Вартість:** ~$25–35 на місяць (AI-токени + обчислення парсингу)

---

## Заходи проти бана

- Навмисні помилки, сленг і різноманітність емодзі в кожному повідомленні
- Без хештегів, маркованих списків, фраз на кшталт ШІ
- Послідовна обробка (по одній відповіді, не паралельно)
- Обережна частота: опитування кожні 60 хв, максимум 10 відповідей на день
- Нічний режим: без активності з 23:00 до 07:00 за київським часом
- Строгий фільтр якості: взаємодія тільки з реальними якісними акаунтами

---

## Посібники

| Файл | Мова | Зміст |
|------|------|-------|
| [GUIDE.md](GUIDE.md) | Російська | Всі промпти, інструкції по блоках, змінні, моделі, правила безпеки |
| [GUIDE_EN.md](GUIDE_EN.md) | Англійська | Той самий зміст, перекладено |
| [GUIDE_UA.md](GUIDE_UA.md) | Українська | Той самий зміст, перекладено |

---

## Ліцензія

Приватний проєкт для @NeoFounder. Заборонено поширення.
