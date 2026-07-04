# OpenCode — Экономия токенов: 3 инструмента

Сохрани этот файл. Всё что нужно — скопипастить.

---

## 1. Встроенный `prune` — удаление старых выводов

**Что делает:** Вырезает старые результаты grep/glob/bash/ls из контекста сессии.
**Экономия:** ~30%, без установки.
**Где:** глобальный конфиг.

### Конфиг → `~/.config/opencode/opencode.json`

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "model": "deepseek/deepseek-v4-pro",
  "small_model": "deepseek/deepseek-v4-flash",
  "compaction": {
    "auto": true,
    "prune": true,
    "reserved": 10000
  },
  "agent": {
    "plan": {
      "model": "deepseek/deepseek-v4-flash"
    }
  }
}
```

**Ставить ничего не надо.** Просто добавь в конфиг.

---

## 2. GrapeRoot — семантический граф проекта

**Что делает:** Сканирует кодовую базу, строит граф (файлы → символы → импорты), пре-загружает в промпт только релевантный код. Запоминает что уже читали — не гоняет повторно.
**Экономия:** 43-81% (зависит от типа задачи).
**GitHub:** https://github.com/kunal12203/Codex-CLI-Compact
**Цена:** бесплатно (лаунчеры Apache 2.0, ядро проприетарное)

### Установка (macOS/Linux)

```bash
curl -sSL https://raw.githubusercontent.com/kunal12203/Codex-CLI-Compact/main/install.sh | bash
source ~/.zshrc
```

### Использование в любом проекте

```bash
cd /path/to/your/project
graperoot . --opencode
# или коротко:
dgo
```

GrapeRoot сам пропишет себя в MCP-конфиг проекта и запустит OpenCode с подключённым сервером.
Данные хранятся в `project/.dual-graph/` (авто-добавляется в `.gitignore`).

### Настройка (опционально, через env vars)

```bash
export DG_HARD_MAX_READ_CHARS=4000       # макс символов на файл
export DG_TURN_READ_BUDGET_CHARS=18000   # бюджет чтения на шаг
export DG_FALLBACK_MAX_CALLS_PER_TURN=1  # макс fallback grep на шаг
```

### Pro-версия ($ платно)

Добавляет: dead export detection, dependency cycle finder, cross-codebase search, undo shield.
https://graperoot.dev/graperoot-pro

---

## 3. CTX — графовая память и task packs (полностью опенсорс)

**Что делает:** Графовая память для правил проекта, компактные task packs вместо гигантских AGENTS.md, log pruning, graph retrieval.
**Экономия:** 56-72% (на бенчмарках).
**GitHub:** https://github.com/Alegau03/CTX
**Цена:** бесплатно, полностью открытый (macOS Apple Silicon — готовый бинарник, остальные — из исходников)

### Установка (macOS Apple Silicon)

```bash
# Скачай последний релиз с GitHub:
# https://github.com/Alegau03/CTX/releases

# Или собери из исходников (любая платформа):
git clone https://github.com/Alegau03/CTX.git && cd CTX
# Дальше по инструкции в README репозитория
```

### Использование в любом проекте

```bash
cd /path/to/your/project
ctx init              # создаёт конфиг CTX в проекте
ctx index             # индексирует файлы/символы
ctx opencode install opencode   # регистрирует CTX как MCP в OpenCode
```

### Команды внутри OpenCode

| Команда | Что делает |
|---|---|
| `/ctx` | Командный центр CTX |
| `/ctx-doctor` | Проверка что всё настроено |
| `/ctx-memory-bootstrap` | Импорт AGENTS.md и правил в граф |
| `/ctx-memory-search` | Поиск по правилам проекта |
| `/ctx-retrieve` | Найти релевантный код/символы под задачу |
| `/ctx-pack` | Собрать компакт-пак контекста |
| `/ctx-prune-logs` | Сжать шумный вывод команд |
| `/ctx-stats` | Статистика экономии токенов |

---

## Сравнение трёх инструментов

| | prune | GrapeRoot | CTX |
|---|---|---|---|
| **Установка** | Не нужна | curl + bash | git clone + сборка |
| **Открытость** | Встроен в OpenCode | Ядро закрытое | Полностью опенсорс |
| **Экономия** | ~30% | 43-81% | 56-72% |
| **Зрелость** | Стабильно | 911 ★, 304 чел в Discord | ~100 ★, свежий |
| **Где хранит данные** | Нигде | `.dual-graph/` в проекте | `.ctx/` в проекте |
| **Локально** | Да | Да | Да |

### Рекомендация

```
1. Включи prune в глобальном конфиге — 30 сек, 30% экономии
2. Поставь GrapeRoot — 1 команда, 43-81% дополнительно
3. CTX — если принципиален полный опенсорс или нужна графовая память правил
```

---

## 4. Скиллы — загружаются только по требованию

Скиллы НЕ попадают в контекст автоматически. Агент видит только название + описание (одна строка). Полный SKILL.md загружается только при вызове `skill({ name: "..." })`.

### 4a. telegram-bot — разработка Telegram-ботов

**Что делает:** Структура проекта aiogram 3.x, anti-spam паттерны, FSM, middleware, защита от инъекций.
**Где:** `~/.config/opencode/skills/telegram-bot/SKILL.md`
**Установка:** уже стоит (кастомный, готовых на GitHub нет)

### 4b. frontend-agent-skills — 9 дизайн-скиллов

**GitHub:** https://github.com/hueyexe/frontend-agent-skills
**Цена:** бесплатно, MIT

```bash
npx -y skills@latest add hueyexe/frontend-agent-skills --global --yes
```

| Скилл | Для чего |
|---|---|
| accessibility-inclusive-design | Доступность (клавиатура, screen reader, семантика) |
| design-systems-frontend-architecture | Дизайн-системы, компоненты, токены |
| forms-inputs-checkout | Формы, валидация, checkout |
| information-architecture-navigation | Навигация, таксономия, wayfinding |
| interaction-patterns-components | Паттерны взаимодействия, состояния |
| ui-visual-composition | Иерархия, типографика, цвет, глубина |
| ux-research-discovery-testing | Исследования, юзер-тесты |
| ux-usability-foundations | Affordances, обратная связь, предотвращение ошибок |
| ux-writing-content-design | Микрокопия, CTA, ошибки, тон |

### 4c. context-engineering-kit — 67 скиллов (1.2k ⭐)

**GitHub:** https://github.com/NeoLabHQ/context-engineering-kit
**Цена:** бесплатно, GPL-3.0

```bash
npx -y skills@latest add NeoLabHQ/context-engineering-kit --global --yes
```

**Ключевые скиллы (для экономии токенов):**

| Скилл | Что делает |
|---|---|
| **context-engineering** | Механика контекста: attention budget, progressive disclosure, lost-in-middle |
| **prompt-engineering** | Техники промптов, Anthropic best practices, Agent Persuasion Principles |
| **agent-evaluation** | LLM-as-Judge, multi-dimensional rubrics, bias mitigation |
| **reflect** | Рефлексия и самоисправление после каждого ответа |
| **memorize** | Извлечение уроков из рефлексии в память проекта |
| **thought-based-reasoning** | Chain of Thought, Tree of Thoughts, Self-Critique |
| **subagent-driven-development** | Чистый subagent на каждую задачу, code review между задачами |
| **test-driven-development** | TDD методология, anti-patterns |

**Остальные скиллы (полный список):**
critique, review-local-changes, review-pr, analyze-issue, commit, create-pr, git-notes, git-worktrees, load-issues, load-pr-comments, resolve-fixed-pr-comments, design-testing-strategy, fix-tests, test-coverage, write-tests, do-and-judge, do-competitively, do-in-parallel, do-in-steps, judge, judge-with-debate, launch-sub-agent, multi-agent-patterns, tree-of-thoughts, add-task, brainstorm, create-ideas, implement-task, plan-task, analyse, analyse-problem, cause-and-effect, kaizen, plan-do-check-act, root-cause-tracing, why, apply-anthropic-skill-best-practices, create-agent, create-command, create-hook, create-rule, create-skill, create-workflow-command, test-prompt, test-skill, update-docs, write-concisely, build-mcp, setup-arxiv-mcp, setup-codemap-cli, setup-context7-mcp, setup-serena-mcp, actualize, decay, propose-hypotheses, query, reset, status

---

## Итоговая картина

| Уровень | Что | Где |
|---|---|---|
| Глобальный | Модели + prune + LSP | `~/.config/opencode/opencode.jsonc` |
| Глобальный | Korpatuy правила поведения | `~/.config/opencode/AGENTS.md` |
| Глобальный | telegram-bot скилл | `~/.config/opencode/skills/telegram-bot/` |
| Глобальный | 17 JUCE скиллов | `~/.claude/skills/` |
| Глобальный | 9 дизайн-скиллов | `~/.agents/skills/` |
| Глобальный | 67 context-engineering скиллов | `~/.agents/skills/` |
| Per-Project | Структура/сборка | `project/AGENTS.md` (через `/init`) |
| Per-Project | GrapeRoot/CTX | `project/.dual-graph/` (опционально) |

```
Порядок действий:
1. Глобальный конфиг (prune + модели) — 1 раз
2. Глобальный AGENTS.md (korpatuy) — 1 раз
3. Скиллы (telegram, дизайн, context-engineering) — 1 раз
4. В каждом проекте: /init → AGENTS.md
5. GrapeRoot: dgo в проекте (опционально)
```