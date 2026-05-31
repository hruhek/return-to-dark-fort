<!-- SPECKIT START -->
Current plan: specs/001-dark-fort-rpg/plan.md
<!-- SPECKIT END -->

## Commands

| Command | What it does |
|---------|-------------|
| `make check` | Full quality gate: ruff check + ruff format --check + ty + pytest |
| `make test` | All tests (unit + integration + e2e) |
| `make test-unit` | `pytest tests/unit/` |
| `make test-integration` | `pytest tests/integration/` |
| `make test-e2e` | `pytest tests/e2e/` |
| `make lint` | `ruff check` |
| `make format` | `ruff format` |
| `make typecheck` | `ty` (strict mode) |
| `make run` | `uv run dark-fort` |
| `make clean` | Remove `__pycache__`, `.ruff_cache`, `.pytest_cache` |
| `make help` | Auto-generated from `##` comments in Makefile |

## Non-negotiable rules

- **TDD**: tests written first, must fail, then implement — never write implementation before tests
- **Quality gate**: `make check` must pass before advancing — lint → typecheck → test
- **Game rules**: `DARK_FORT.md` is the single source of truth for all mechanics — spec tables derive from it
- **No speculative code**: every feature must trace back to a spec/FR requirement
- **Package manager**: always `uv run`/`uv sync`, never `pip` or bare `python`
- **Save file**: `~/.dark_fort/save.json` — round-trip via `.model_dump()`/`.model_validate()`
