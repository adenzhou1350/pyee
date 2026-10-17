# Contributing to pyee with an AI assistant

Read [DEVELOPMENT.md](DEVELOPMENT.md) before changing this repository. It is
the source of truth for setup, QA and contribution policy; this file is a
navigation aid, not a replacement. Read the relevant implementation docstrings
and existing tests before assuming that behavior from another emitter applies.

## Find the affected behavior

| Area | Implementation | Tests |
| --- | --- | --- |
| Listener registration, emission and removal | `pyee/base.py` | `tests/test_sync.py` |
| asyncio scheduling and task completion | `pyee/asyncio.py` | `tests/test_asyncio.py` |
| Trio nursery lifecycle | `pyee/trio.py` | `tests/test_trio.py` |
| Twisted coroutines and Deferreds | `pyee/twisted.py` | `tests/test_twisted.py` |
| Executor-backed handlers | `pyee/executor.py` | `tests/test_executor.py` |
| Class decorators | `pyee/cls.py` | `tests/test_cls.py` |
| Bridging two emitters | `pyee/uplift.py` | `tests/test_uplift.py` |

API documentation in `docs/api.md` is generated from docstrings. Update the
docstring when a public behavior changes, rather than copying its API text into
a second document.

## Implement and check a change

- For a bug, reproduce it through the affected emitter's public API. Where
  practical, confirm that the regression test fails on unchanged production
  code and passes with the fix. Include a nearby supported case that must keep
  working, and keep the repair focused.
- Preserve the distinction between synchronous `EventEmitter` callbacks and
  the scheduling/error handling of each backend. In particular, an async
  emitter's `emit()` is not a coroutine to await. Check the backend's docstrings
  rather than assuming that a handler return value or error is treated the same
  by asyncio, Trio and Twisted.
- Tests must finish or cancel their tasks and close their nurseries/executors.
  Use the existing async pytest markers and test patterns; do not rely on sleeps
  to prove ordering or on garbage collection for cleanup.
- Follow `pyproject.toml` for supported Python versions and formatter/type-check
  configuration. Inspect `.github/workflows/qa.yaml` for the current CI matrix;
  the version list in `tox.ini` can lag behind the supported versions.

Use `just install` to set up dependencies and `just` for the development QA
loop. Individual recipes are `just format`, `just check`, `just test` and
`just lint`. `just check` runs Pyright; `just lint` includes both Flake8 and
`validate-pyproject`. CI additionally runs mypy. For a focused test during
development, for example:

```sh
uv run pytest tests/test_asyncio.py
```

If a tool, dependency or platform is unavailable, report which check could not
run and why. List the actual commands, Python version and results in the PR;
one targeted test run is not the full QA loop or Python-version matrix. Do not
change compatibility requirements or weaken a test merely to get a local pass.

## Before submitting

Use `.github/pull_request_template.md`. Explain the behavior, fix and test
coverage, add an entry under the current unreleased section of `CHANGELOG.md`,
and add new contributors to `CONTRIBUTORS.md` (email is optional).

For AI-assisted contributions, prefix relevant **commit messages** and
**changelog entries** with **🤖**, and disclose AI assistance in the PR as
required by `DEVELOPMENT.md`. Do not claim that a human reviewed the code or
that a check passed unless that actually happened. Package publishing, release
tags and version bumps are separate release tasks, not part of an ordinary fix.
