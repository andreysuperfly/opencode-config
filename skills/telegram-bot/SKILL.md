---
name: telegram-bot
description: Telegram bot development with aiogram 3.x — async handlers, anti-spam filters, FSM, rate limiting, SQLite/PostgreSQL persistence
license: MIT
compatibility: opencode
metadata:
  audience: python-developers
  framework: aiogram-3.x
---

## Stack
- Python 3.12+, aiogram 3.x (async/await), SQLite or PostgreSQL
- Use `python-dotenv` for BOT_TOKEN — NEVER hardcode tokens in source
- Rate limiting: `aiogram-rate-limiter` or custom middleware

## Project structure
```
bot/
├── main.py          # entry point, Dispatcher setup
├── config.py        # env vars, settings
├── handlers/
│   ├── admin.py     # admin commands
│   ├── user.py      # public commands
│   └── antispam.py  # spam detection
├── middlewares/
│   └── rate_limit.py
├── filters/
│   ├── spam_patterns.py
│   └── user_reputation.py
├── db/
│   └── models.py    # SQLAlchemy or aiosqlite
└── keyboards/
    └── inline.py
```

## Anti-spam patterns
- Rate limit per user: max 5 messages / 10 sec
- Pattern matching: regex for links, flood, repeated text
- New user cooldown: block first 30 seconds after join
- Forwarded message check: spam bots often forward
- Reputation system: strikes → mute → ban
- NEVER use `eval()` or `exec()` — injection risk

## Code conventions
- All handlers async
- Use `FSMContext` for multi-step flows
- Type hints everywhere: `Message`, `CallbackQuery`, `FSMContext`
- Error handling: wrap handlers in try/except, log to file
- No global state — use database for persistence

## Commands
```
poetry run python bot/main.py    # run
poetry run pytest                # test
poetry run ruff check .          # lint
```