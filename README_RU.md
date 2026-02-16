# Twitter/X ИИ-Ассистент для @NeoFounder

Автоматизированный агент вовлечения в Twitter/X на базе [MindStudio.ai](https://mindstudio.ai) — отслеживает релевантные посты, оценивает их по взвешенной системе баллов и генерирует естественные комментарии от персоны украинского основателя в крипто/web3/AI.

---

## Как это работает (шаг за шагом)

Агент имеет **2 независимых процесса**, работающих в автопилоте:

### Процесс 1: Авто-ответы (запуск каждые 60 минут)

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

### Процесс 2: Ежедневные посты (1 твит в день, разное время)

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

## Система оценки

| Категория | Вес | Шкала | Что измеряет |
|-----------|-----|-------|--------------|
| Релевантность темы | x3 | 0-10 | Соответствует ли твит темам AI/crypto/web3/SaaS/стартап? |
| Качество вовлечённости | x2 | 0-10 | Количество лайков, потенциал обсуждения, спам или реальный контент? |
| Качество автора | x3 | 0-10 | Подписчики, релевантность био, верификация, реальный человек или бот? |
| Свежесть | x1 | 0-10 | Насколько недавно опубликовано? |

**Формула:** `total = (topic×3 + engagement×2 + author×3 + freshness×1) / 9`

**Порог:** Ответ получают только твиты с оценкой **6+**. Максимум **10 ответов в день**.

---

## Персона: @NeoFounder

Агент действует как 28-летний украинский основатель в крипто/web3/AI, в настоящее время проживающий за границей:

- **Язык:** Посты всегда на английском. Ответы соответствуют языку оригинального твита (EN или UA)
- **Тон:** Неформальный, энергичный, прямой, иногда саркастичный
- **Сленг:** bro, lol, fr fr, based, wagmi, lfg + украинские: кайф, жиза, топчик
- **Опечатки:** Намеренные 1-2 на сообщение (goood, definetly, teh)
- **Эмодзи:** 1-3 на сообщение, никогда одинаковый набор дважды
- **Строгие правила:** Никогда не выдаёт, что это ИИ; без маркированных списков, хештегов и идеальной грамматики

---

## Структура файлов

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

## Технический стек

- **Платформа:** [MindStudio.ai](https://mindstudio.ai) (no-code, бесплатный тариф)
- **AI-модели:** Claude 3.5 Haiku (ответы и посты), блоки логики автоматически выбирают модель
- **Интеграция с X:** Search X Posts, Scrape X Post, Scrape X Profile, Create X Post
- **Стоимость:** ~$25–35 в месяц (AI-токены + вычисления парсинга)

---

## Меры против бана

- Намеренные опечатки, сленг и разнообразие эмодзи в каждом сообщении
- Без хештегов, маркированных списков, фраз в стиле ИИ
- Последовательная обработка (по одному ответу, не параллельно)
- Осторожная частота: опрос каждые 60 мин, максимум 10 ответов в день
- Ночной режим: без активности с 23:00 до 07:00 по киевскому времени
- Строгий фильтр качества: взаимодействие только с реальными качественными аккаунтами

---

## Гайды

| Файл | Язык | Содержание |
|------|------|------------|
| [GUIDE.md](GUIDE.md) | Русский | Все промпты, инструкции по блокам, переменные, модели, правила безопасности |
| [GUIDE_EN.md](GUIDE_EN.md) | Английский | Тот же контент, переведён |
| [GUIDE_UA.md](GUIDE_UA.md) | Украинский | Тот же контент, переведён |

---

## Лицензия

Приватный проект для @NeoFounder. Запрещено распространение.
