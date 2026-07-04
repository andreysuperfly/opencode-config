# OpenCode — Полная настройка для Mac (AU/VST плагины + Telegram боты)

Оптимизированная конфигурация OpenCode для минимального расхода токенов.

## Быстрый старт

```bash
# 1. Скопируй конфиг
cp opencode.jsonc ~/.config/opencode/opencode.jsonc
# ⚠️ Подставь свой OpenRouter API ключ в поле "apiKey"

# 2. Глобальные правила поведения (Korpatuy)
cp AGENTS.md ~/.config/opencode/AGENTS.md

# 3. Telegram-скилл
cp -r skills/telegram-bot ~/.config/opencode/skills/

# 4. В каждом проекте:
cd project && opencode
/init
```

## Что внутри

### opencode.jsonc — модели и оптимизации
- **Build**: DeepSeek V4 Pro (основной кодинг)
- **Plan**: DeepSeek V4 Pro (планирование)
- **small_model**: Gemma 4 26B (фоновые задачи)
- `prune: true` — авто-очистка старых выводов инструментов (~30% экономии)
- `lsp: true` — авто-языковые серверы

### AGENTS.md — Korpatuy правила
- Думать перед кодом, не гадать
- Минимум кода, без спекуляций
- Хирургические правки
- Цель → проверка

### skills/telegram-bot — разработка Telegram-ботов
Структура aiogram 3.x, anti-spam паттерны, FSM, middleware, защита от инъекций.

## Дополнительные инструменты (экономия токенов)

| Инструмент | Установка | Команда |
|---|---|---|
| **GrapeRoot** (43-81%) | `curl -sSL https://raw.githubusercontent.com/kunal12203/Codex-CLI-Compact/main/install.sh \| bash` | `graperoot . --opencode` |
| **CTX** (56-72%) | `brew tap Alegau03/ctx && brew trust alegau03/ctx && brew install ctx` | `ctx init && ctx index && ctx opencode install` |
| **frontend-agent-skills** | `npx -y skills@latest add hueyexe/frontend-agent-skills --global --yes` | 9 дизайн-скиллов |
| **context-engineering-kit** | `npx -y skills@latest add NeoLabHQ/context-engineering-kit --global --yes` | 67 скиллов (1.2k ⭐) |

Полный гайд: см. `opencode-token-savers.md`

## JUCE плагины

17 JUCE-скиллов для разработки аудиоплагинов (от планирования до кодесайна и пакейджинга). Лежат отдельно в `~/.claude/skills/`.

## Модели

| Роль | Модель |
|---|---|
| Build | `openrouter/deepseek/deepseek-v4-pro` |
| Plan | `openrouter/deepseek/deepseek-v4-pro` |
| Small | `openrouter/google/gemma-4-26b-a4b-it` |
| Картинки | Переключиться на `z-ai/glm-5.2` через `/model` |