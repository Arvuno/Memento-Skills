# 05_PR_CANDIDATES.md — Memento-Skills PR Candidate Analysis

## Issue Summary

**Upstream**: Memento-Teams/Memento-Skills  
**Fork**: okwn/Memento-Skills  
**Version**: 0.3.0  
**Test Baseline**: 105 passed, 23 failed, 8 collection errors

---

## Quality Issues Identified

### 🔴 P0 — Blocking Issues (prevent tests from running)

#### 1. test_prompt_components.py:389 — IndentationError (SYNTAX ERROR)
- **File**: `tests/test_prompt_components.py`
- **Line**: 389
- **Problem**: `skills = _make_fake_skills()` has wrong indentation — appears to be indented inside a function block but is at module level
- **Impact**: File cannot be collected by pytest; entire test suite may be affected
- **Fix**: Dedent line 389 to match surrounding context (appears to be inside `test_execute_skill_direct_local()`)

#### 2. Multiple test files reference removed/broken module paths (COLLECTION ERRORS)
- `tests/context_gateway/conftest.py` → `core.context.scratchpad` (doesn't exist)
- `tests/test_search_execute_flow.py` → `core.skill.provider.SkillProvider` (doesn't exist)
- `tests/tool_dispatcher_gateway/conftest.py` → `core.memento_s.policies.PolicyManager` (doesn't exist)
- `tests/test_storage_*.py` → `middleware.storage.MessageService`, `middleware.storage.models.Message` (don't exist)
- `tests/test_model_schema_consistency.py` → broken imports

### 🟡 P1 — Major Issues (test failures, broken functionality)

#### 3. test_context_manager.py — 10 failing tests
- **Problem**: `ModuleNotFoundError: No module named 'core.context.scratchpad'` and `TypeError` issues
- **Root Cause**: Context module was refactored in v0.3.0; tests reference old structure
- **Affected Tests**: test_scratchpad_init_and_write, test_persist_tool_result_*, test_append_*, test_get_context_section_*

#### 4. test_skill_executor.py — 9 failing tests
- **Problem**: `ValueError: No active event loop` in async tests
- **Root Cause**: pytest-asyncio not properly configured for these tests
- **Affected Tests**: test_agent_init, test_agent_init_with_policy_manager, test_get_tool_schemas_*, test_react_state_action_signature

#### 5. test_middleware_llm.py & test_refactored_architecture.py — async failures
- **Problem**: "async def functions are not natively supported" — missing pytest-asyncio plugin or configuration

### 🟢 P2 — Minor Issues

#### 6. test_platform_utils.py — FileNotFoundError
- Likely a path resolution issue

---

## Candidate PR Topics

### Candidate 1: Fix test_prompt_components.py IndentationError
**Priority**: P0  
**Files**: `tests/test_prompt_components.py`  
**Effort**: Low (1 line dedent)  
**Risk**: Low

### Candidate 2: Fix test collection errors — update broken import paths
**Priority**: P0  
**Files**: `tests/context_gateway/conftest.py`, `tests/test_search_execute_flow.py`, `tests/tool_dispatcher_gateway/conftest.py`, `tests/test_storage_*.py`, `tests/test_model_schema_consistency.py`  
**Effort**: Medium (5-8 files)  
**Risk**: Medium (refactor may be incomplete)

### Candidate 3: Fix test_context_manager.py failing tests
**Priority**: P1  
**Files**: `tests/test_context_manager.py`, possibly `core/context/`  
**Effort**: Medium  
**Risk**: Medium

### Candidate 4: Fix async test configuration (pytest-asyncio)
**Priority**: P1  
**Files**: `tests/conftest.py`, `pyproject.toml`  
**Effort**: Low  
**Risk**: Low

### Candidate 5: Fix test_skill_executor.py event loop errors
**Priority**: P1  
**Files**: `tests/test_skill_executor.py`  
**Effort**: Medium  
**Risk**: Low

---

## Open Upstream Issues (4 issues, 1 PR)

| # | Title | Priority | Topic |
|---|-------|----------|-------|
| 6 | 实验流程公开请求 | Low | Documentation |
| 5 | fix: add missing bootstrap.py and fix doctor command crash | High | Bug fix (also PR #5) |
| 3 | Clarification on self-evolving write-back loop status | Medium | Design question |
| 2 | 本地运行代码的问题总结 | Medium | Documentation |

**PR #5**: "fix: add missing bootstrap.py and fix doctor command crash" — addresses bootstrap.py missing and doctor command crash. Already open as both issue and PR.

---

## Recommended Priority Order for PRs

1. **Candidate 4** — Fix async test configuration (quick win, unblocks many tests)
2. **Candidate 1** — Fix IndentationError (unblocks test_prompt_components.py)
3. **Candidate 2** — Fix collection errors (unblocks 5+ test files)
4. **Candidate 3** — Fix test_context_manager.py (10 failing tests)
5. **Candidate 5** — Fix test_skill_executor.py event loop (9 failing tests)