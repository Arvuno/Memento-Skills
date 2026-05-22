# 01_REPO_MAP.md — Memento-Skills Repository Map

## Top-Level Structure

```
memento-skills/
├── README.md              # Full documentation (36KB, v0.3.0 release notes)
├── howto.md                # Quick start guide
├── pyproject.toml          # Python 3.12+, hatchling build, flet desktop
├── requirements-prod.txt   # Full production dependencies
├── requirements-dev.txt    # Dev dependencies
├── bootstrap.py            # Bootstrap/initialization script (26KB)
├── version.py              # Version info
│
├── 3rd/                    # Third-party integrations (weixin_sdk, etc.)
├── assets/                 # Static assets
├── builtin/                # Built-in skills
│   └── skills/
│       ├── docx/          # Word document skill
│       ├── filesystem/    # File operations skill
│       ├── pdf/           # PDF processing skill
│       ├── pptx/          # PowerPoint skill
│       ├── skill-creator/ # Skill builder/creator
│       ├── uv-pip-install/# Package installer skill
│       ├── web-search/    # Web search skill
│       └── xlsx/          # Excel skill
│
├── cli/                    # CLI entry point (typer-based)
├── core/                   # Core agent logic
│   ├── __init__.py
│   ├── agent_profile/     # Persistent agent/user profiles (NEW in v0.3.0)
│   ├── context/           # Context management (scratchpad, blocks, etc.)
│   ├── memento_s/         # MementoSAgent core
│   ├── prompts/           # Prompt templates
│   ├── protocol/          # AG-UI event protocols
│   └── skill/            # Skill system (gateway, market, loader, builder)
│
├── daemon/                 # Background processes
│   ├── agent_profile/     # Background profile evolution (NEW in v0.3.0)
│   └── dream/            # Dream consolidation daemon (NEW in v0.3.0)
│
├── docs/                  # Architecture docs, API spec, design notes
├── Figures/               # README figures/diagrams
├── gui/                   # Flet-based GUI application
├── im/                    # IM platform integrations (Feishu, DingTalk, WeCom, WeChat)
├── infra/                 # Infrastructure layer (NEW in v0.3.0)
│   ├── memory/           # Long-term and session memory
│   ├── context/          # Context providers
│   ├── compact/           # Context compaction pipeline
│   └── service.py        # InfraService entry
│
├── middleware/            # LLM client, config, storage, IM gateway
├── scripts/               # Build/deployment scripts
├── server/                # HTTP API server (FastAPI-based)
├── shared/               # Cross-cutting utilities
│   ├── chat/             # Chat/session/conversation management
│   ├── fs/               # Filesystem helpers
│   ├── hooks/            # Lifecycle hooks
│   ├── schema/           # Schema definitions
│   ├── security/         # Path/argument security
│   └── tools/            # Tool dispatch helpers
│
├── tests/                # Test suite (mixed pass/fail)
│   ├── conftest.py
│   ├── context_gateway/
│   ├── prompts/          # 105 tests, all passing
│   ├── tool_dispatcher_gateway/
│   ├── tool_security/
│   ├── conftest.py
│   ├── test_*.py         # Various tests (many failing due to refactor lag)
│
├── tools/                # Unified Tool Registry (NEW in v0.3.0)
│   ├── atomics/         # Atomic tools: bash, file_ops, grep, glob, list, web, python_repl, js_repl
│   ├── mcp/             # MCP client integration
│   ├── registry.py      # Single ToolRegistry surface
│   └── tests/           # Tool tests
│
└── utils/                # Runtime utils, logging, string helpers
```

## Key Module Relationships

```
bootstrap.py
  └── Creates initial config at ~/memento_s/config.json

cli/main.py (memento/memento-gui entry)
  ├── core/memento_s/agent.py → MementoSAgent
  ├── core/skill/gateway.py → SkillGateway
  ├── middleware/llm/llm_client.py → LLMClient
  ├── middleware/config/ → ConfigManager
  ├── shared/chat/ → ChatManager
  └── utils/event_bus → EventBus

core/skill/
  ├── gateway.py       # Skill discovery, retrieval, execution
  ├── market.py        # Cloud skill marketplace
  ├── loader.py        # Skill loading pipeline
  ├── builder/         # Programmatic skill creation
  ├── downloader/      # Download pipeline
  └── ...

infra/ (v0.3.0 NEW - replaced core/context, core/shared/compact, core/shared/memory)
  ├── memory/          # Memory implementations
  ├── context/         # Context providers
  └── compact/         # Context compaction (BM25 + vector hybrid)

tools/ (v0.3.0 NEW - replaced builtin/tools/ and tool_bridge/)
  ├── atomics/         # Atomic tool implementations
  ├── mcp/             # MCP client
  └── registry.py      # ToolRegistry.get_registry()
```

## Built-in Skills (10)

| Skill | Path | Description |
|-------|------|-------------|
| web-search | builtin/skills/web-search/ | Web search capability |
| pdf | builtin/skills/pdf/ | PDF processing |
| docx | builtin/skills/docx/ | Word document operations |
| xlsx | builtin/skills/xlsx/ | Excel spreadsheet operations |
| pptx | builtin/skills/pptx/ | PowerPoint operations |
| filesystem | builtin/skills/filesystem/ | File system operations |
| skill-creator | builtin/skills/skill-creator/ | Build new skills |
| uv-pip-install | builtin/skills/uv-pip-install/ | Python package installation |
| im-platform | (code in middleware/im/) | IM platform operations |

## Test Status Summary

| Test Suite | Status | Notes |
|------------|--------|-------|
| tests/prompts/ | 105 PASS | All passing |
| tools/tests/ | Mixed | Some failures |
| tests/context_gateway/ | ERROR | Missing module core.context.scratchpad |
| tests/tool_dispatcher_gateway/ | ERROR | Missing module core.memento_s.policies |
| tests/test_context_manager.py | 10 FAIL | Missing module / refactor lag |
| tests/test_skill_executor.py | 9 FAIL | ValueError: no active event loop |
| tests/test_middleware_llm.py | 1 FAIL | async def functions not supported |
| tests/test_prompt_components.py | SYNTAX ERROR | IndentationError line 389 |
| tests/test_storage_*.py | 3 ERROR | Missing imports from middleware.storage |
| tests/test_search_execute_flow.py | ERROR | Missing module core.skill.provider |
| tests/test_storage_models.py | ERROR | Import error |
| tests/test_platform_utils.py | ERROR | FileNotFoundError |
| tests/test_refactored_architecture.py | 1 FAIL | async def functions not supported |

## Git History (Recent)

```
07b530e Update README.md
2fb9762 ci: remove flet native build workflow
71ac933 release: v0.3.0 - infrastructure layer, unified tool registry, agent profile
f05f9bc release: v0.2.0 - major architecture upgrade
c8f91b6 update readme
adc2080 update Readme
52c75eb Initial commit
```