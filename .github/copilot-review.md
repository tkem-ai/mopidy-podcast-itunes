# Code Review — Mopidy-Podcast-iTunes 4.0.0

**Date:** 2026-11-10
**Reviewed:** `develop`, i.e. `master` at `a710852` ("Bump codecov/codecov-action from
6.0.0 to 7.0.0", v4.0.0 plus dependency bumps) plus further dependency bumps, the
build environment update, the `search()` and `browse()` fixes, "Exclude AGENTS.md from
source distributions.", "Add system site packages setting as comment to tox.ini." and
this review commit; `__version__` 4.0.0.

**CI status:** All default `tox` environments pass locally on CPython 3.13.18 (`py`:
42 passed) with Mopidy 4.0.5, Mopidy-Podcast 4.0.0 and requests 2.34.2; not run on
3.14 locally. GitHub Actions is green on 3.13 and 3.14 for `develop` before this
review commit. `tox -e pyright` fails with 37 errors (Code #1). **Coverage:** 94 %
with branches; gaps in `library.py` (93 %: `refresh()`, skipped chart items) and the
`translator.query()` errors.

## Code — Potential Issues

| # | Priority | Location | Finding |
|---|----------|----------|---------|
| 1 | Low | whole package, `tests/` | pyright `standard` reports 37 errors: Mopidy 4's typed models (`Uri`, `ReleaseDate`, `DurationMs`, `frozenset` artists, `tuple` search results) reject plain `str`, `int` and `list` arguments, `self.backend.client` is unknown on `backend.Backend`, and the `root_directory` property overrides a class attribute. Blocks adding `pyright` to the envlist. |

## Tests — Gaps

| # | Priority | Finding |
|---|----------|---------|
| 1 | Low | `refresh()` and the charts path that skips items with missing keys (lines 81–86) are untested. |
| 2 | Info | `translator.query()` is tested only for supported queries; the three `NotImplementedError` branches and `search()` returning `None` for them are not. |
| 3 | Info | The `config` fixture still carries the removed `charts` key; two `DeprecationWarning`s come from `mopidy.types` at import time, not from this package. |

## Changelog

| # | Priority | Finding |
|---|----------|---------|
| 1 | Medium | The next release's entry needs "Fix ``UnboundLocalError`` in ``search()`` if the query cannot be translated." and "Fix HTTP, timeout and connection errors propagating from ``search()``.", "Fix ``browse()`` errors for invalid URIs and failed requests." and "Fix podcasts without ``trackName`` being skipped.", plus "Update build environment." for the ruff, `tox` and CI changes and the removal of `setup.cfg` and `setup.py`. |
