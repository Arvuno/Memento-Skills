# 00_STATE.md — Memento-Skills Analysis State

## Repository Info
- **Name**: Memento-Skills (memento-s)
- **Version**: 0.3.0
- **Upstream**: Memento-Teams/Memento-Skills (https://github.com/Memento-Teams/Memento-Skills)
- **Fork**: okwn/Memento-Skills (https://github.com/okwn/Memento-Skills)
- **Description**: Let Agents Design Agents — Self-evolving agent framework with read-write reflective learning
- **License**: Unknown (not returned by API)
- **Archived**: No
- **Python**: >=3.12 required

## Repository Status
- [x] Forked from Memento-Teams/Memento-Skills → okwn/Memento-Skills
- [x] Cloned to /root/oss-pr-campaign/repos/memento-skills
- [x] Upstream remote added as `upstream`
- [x] README, pyproject.toml, requirements analyzed
- [x] Test suite baseline run (105 passed, 23 failed, 8 collection errors)

## Test Baseline Results
- **tests/prompts/**: 105 passed ✓
- **tests/test_middleware_llm.py**: 1 failed (async issue)
- **tests/test_context_manager.py**: 10 failed (module path issues)
- **tests/test_skill_executor.py**: 9 failed (ValueError: no active event loop)
- **tests/test_refactored_architecture.py**: 1 failed (async)
- **Collection errors**: 8 (missing/broken imports: core.context.scratchpad, core.skill.provider, core.memento_s.policies, middleware.storage, test_prompt_components syntax error)

## Open Issues (4)
1. #6: 实验流程公开请求 (open)
2. #5: fix: add missing bootstrap.py and fix doctor command crash (open, also a PR)
3. #3: Clarification on the current implementation status of the self-evolving write-back loop (open)
4. #2: 本地运行代码的问题总结 (open)

## Open PRs (1)
- #5: fix: add missing bootstrap.py and fix doctor command crash (open)

## Key Findings
- v0.3.0 is a major refactor introducing infra/, tools/, daemon/dream/, core/agent_profile/
- Many test failures are due to refactoring lag (tests reference old module paths)
- Python 3.12+ required (system Python 3.11.15 available, 3.12.3 also available)
- Rich project with 10 built-in skills, CLI+GUI, IM platform integrations
- No GitHub Actions CI workflow found (.github/workflows/ absent)
