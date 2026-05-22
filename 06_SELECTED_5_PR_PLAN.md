# 06_SELECTED_5_PR_PLAN.md — Memento-Skills Top 5 PR Recommendations

## Selected Priority Candidates

Based on analysis, these 5 areas offer the best impact for OSS contribution:

---

## PR 1: Fix test_prompt_components.py IndentationError (Quick Win)

**File**: `tests/test_prompt_components.py`  
**Lines**: ~386-389 (inside `test_execute_skill_direct_local`)  
**Priority**: P0  
**Type**: Bug fix

### Problem
The function `test_execute_skill_direct_local()` at line 382 has malformed indentation:
- Lines 386-387 (`from core.memento_s...` and `from core.context...`) are imports but incorrectly indented as if inside the function
- Line 389 (`skills = _make_fake_skills()`) is at wrong indentation level
- This causes `IndentationError: unexpected indent` at parse time, preventing pytest from collecting the file

### Fix Required
```python
# BEFORE (broken):
async def test_execute_skill_direct_local():
    """验证本地 skill 可以不经 search_skill 直接 execute_skill"""
    print("\n【9. execute_skill 直接调用本地 skill（无需先 search）】")

    from core.memento_s.skill_dispatch import SkillDispatcher
from core.context.session_context import SessionContext

    skills = _make_fake_skills()

# AFTER (fixed):
async def test_execute_skill_direct_local():
    """验证本地 skill 可以不经 search_skill 直接 execute_skill"""
    print("\n【9. execute_skill 直接调用本地 skill（无需先 search）】")

    from core.memento_s.skill_dispatch import SkillDispatcher
    from core.context.session_context import SessionContext

    skills = _make_fake_skills()
```

### Effort
- **Time**: < 15 minutes
- **Risk**: Low — isolated fix to one test function

---

## PR 2: Fix pytest-asyncio Configuration for Async Tests

**File**: `pyproject.toml` and `tests/conftest.py`  
**Priority**: P1  
**Type**: Test infrastructure fix

### Problem
Tests in `test_skill_executor.py`, `test_middleware_llm.py`, and `test_refactored_architecture.py` fail with:
- "async def functions are not natively supported"
- "ValueError: No active event loop"

This indicates `pytest-asyncio` is not properly configured.

### Fix Required
Add to `pyproject.toml`:
```toml
[tool.pytest.ini_options]
asyncio_mode = "auto"
asyncio_default_fixture_loop_scope = "function"
```

### Effort
- **Time**: < 15 minutes
- **Risk**: Low — configuration change

---

## PR 3: Fix test Context Imports — Point to v0.3.0 Module Locations

**Files**:
- `tests/context_gateway/conftest.py` → line 15: `from core.context.scratchpad import Scratchpad`
- `tests/tool_dispatcher_gateway/conftest.py` → line 13: `from core.memento_s.policies import PolicyManager`
- `tests/test_search_execute_flow.py` → line 24: `from core.skill.provider import SkillProvider`
- `tests/test_storage_*.py` → imports from `middleware.storage`
- `tests/test_model_schema_consistency.py` → broken imports

**Priority**: P1  
**Type**: Refactoring / Import fixes

### Problem
v0.3.0 reorganized modules:
- `core/context/scratchpad.py` → now in `infra/memory/` or `core/context/session.py`
- `core.memento_s.policies` → now in `tools/registry.py` or removed
- `core.skill.provider` → now `core.skill/gateway.py` or `core/skill/registry.py`
- `middleware.storage.MessageService` → now in `middleware/storage/services/`

### Fix Approach
1. Investigate current module locations in v0.3.0
2. Update imports to point to correct modules
3. If modules were removed/renamed, either re-implement or update tests accordingly

### Effort
- **Time**: 2-4 hours
- **Risk**: Medium — may reveal incomplete refactoring

---

## PR 4: Fix test_context_manager.py — 10 Failing Tests

**File**: `tests/test_context_manager.py`  
**Priority**: P1  
**Type**: Bug fix / Test update

### Failing Tests
- `test_scratchpad_init_and_write` — ModuleNotFoundError
- `test_persist_tool_result_*` (5 tests) — ModuleNotFoundError
- `test_append_*` (2 tests) — TypeError
- `test_get_context_section_*` (2 tests) — AttributeError

### Root Cause
The `core.context` module was heavily refactored in v0.3.0:
- `Scratchpad` class moved/renamed
- `ContextManager` refactored into `infra/context/` and `infra/memory/`
- Session/context handling moved to `core/context/session.py`

### Fix Approach
1. Map old API to new API
2. Either:
   - Update tests to use new API, OR
   - Add compatibility shims in `core/context/` that re-export from new locations

### Effort
- **Time**: 2-4 hours
- **Risk**: Medium

---

## PR 5: Fix test_platform_utils.py FileNotFoundError

**File**: `tests/test_platform_utils.py`  
**Priority**: P2  
**Type**: Bug fix

### Problem
Test fails with `FileNotFoundError` — likely a path resolution issue for test resources.

### Fix Approach
1. Run the test in isolation to see full error
2. Check if paths use `Path(__file__).parent` correctly
3. Verify any test fixture files exist

### Effort
- **Time**: 30 minutes
- **Risk**: Low

---

## Implementation Notes

### Dependencies Between PRs
- PR 1 (IndentationFix) should be done first — it unblocks test collection
- PR 2 (pytest-asyncio) should be done second — it unblocks async test execution
- PR 3 (Import fixes) can be done in parallel with PR 4
- PR 4 and PR 5 depend on correct module structure established in PR 3

### Test Verification Command
After each fix, run:
```bash
.venv/bin/python -m pytest tests/prompts/ -v  # Should always pass (105 tests)
.venv/bin/python -m pytest tests/test_skill_executor.py -v  # Should improve after PR 2+3
```

### Upstream Issue #5 Reference
Issue/PR #5 "fix: add missing bootstrap.py and fix doctor command crash" may be related to some of the test failures (bootstrap.py was referenced in many old tests). Verify if bootstrap.py is properly integrated after fixing tests.