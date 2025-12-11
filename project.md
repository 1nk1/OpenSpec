# Project Context

## Purpose
Claude-Flow Docker - интегрированная система оркестрации AI-агентов с поддержкой MCP (Model Context Protocol).
Проект объединяет три ключевых компонента для создания полнофункциональной среды разработки с AI-ассистентами.

## Tech Stack
- **Runtime**: Node.js 22+, TypeScript
- **Container**: Docker, docker-compose
- **Package Manager**: pnpm (единственный разрешённый)
- **MCP**: mcp-proxy для SSE транспорта
- **Database**: SQLite (better-sqlite3) для hive memory
- **AI Framework**: claude-flow v2.7.47
- **Spec Management**: OpenSpec
- **Skills**: ai-labs-claude-skills (24 навыка)

## Architecture

```
claude-flow-docker/
├── claude-flow/          # Git submodule - AI orchestrator
├── openspec/             # Git submodule - Spec-driven development
├── skills/               # Git submodule - Agent skills (24 skills)
├── .claude/
│   ├── skills/           # Installed skills
│   └── commands/         # Slash commands including OpenSpec
├── lib/
│   └── log-formatter.sh  # Log post-processing (DO NOT EDIT claude-flow sources!)
├── scripts/              # Integration scripts
├── Dockerfile            # Node.js 22-slim + mcp-proxy
└── docker-compose.yml    # MCP SSE on port 8080
```

## Project Conventions

### Code Style
- TypeScript с strict mode
- Conventional Commits для сообщений коммитов
- Форматирование логов: `[DD.MM.YY|HH:MM] [LEVEL] [module] message`
- ANSI цвета: серый timestamp, цветной фон для уровней логирования

### Architecture Patterns
- **Submodules over npm**: Используем git submodules для лучшего контроля версий
- **Wrapper over modification**: НИКОГДА не модифицируй исходники npm пакетов
- **Post-processing**: Используй обёртки/пайпы для трансформации вывода
- **MCP Protocol**: SSE транспорт на порту 8080
- **Skills as slash commands**: Каждый skill доступен как `/<skill-name>`

### Testing Strategy
- Unit tests: vitest
- E2E tests: playwright
- Integration tests для Docker контейнера

### Git Workflow
- Main branch: `main`
- Submodules: автономные репозитории с собственными ветками
- Команды: `make submodule-pull`, `make push-all`

## Domain Context

### Swarm Orchestration
- Топологии: mesh, hierarchical, ring, star
- Агенты: coordinator, analyst, optimizer, documenter, researcher, coder, tester, reviewer
- Hive Memory: shared memory namespace с TTL и namespacing

### MCP Integration
- Endpoint: `http://localhost:8080/sse`
- Подключение: `claude mcp add --transport sse claude-flow http://localhost:8080/sse`
- 80+ MCP tools для оркестрации агентов

### Skills Categories
- Data Analysis: data-analyst, csv-data-visualizer, business-analytics-reporter
- Development: docker-containerization, cicd-pipeline-generator, test-specialist
- Documentation: codebase-documenter, research-paper-writer, document-skills
- Business: startup-validator, pitch-deck, finance-manager

## Important Constraints

### CRITICAL: Do NOT modify claude-flow sources
- Все форматирование через wrapper scripts
- При обновлении npm пакета изменения не потеряются
- Используй `lib/log-formatter.sh` для форматирования логов

### Package Manager
- ТОЛЬКО pnpm
- yarn и npm запрещены
- `.npmrc` содержит approved build dependencies

### Docker
- Container user: `appuser` (non-root)
- MCP port: 8080
- Dashboard port: 3001

## External Dependencies

### Git Repositories (submodules)
- `claude-flow`: https://github.com/1nk1/claude-flow.git
- `openspec`: https://github.com/1nk1/OpenSpec.git
- `skills`: https://github.com/1nk1/ai-labs-claude-skills.git

### npm Packages (global in container)
- `mcp-proxy` - SSE bridge for MCP
- `claude-flow` - AI orchestrator CLI

### Services
- Docker daemon via Colima (macOS)
- MCP SSE endpoint на localhost:8080
- Dashboard на localhost:3001
