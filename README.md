# ci-common

Reusable CI workflows for the 10ax repos. **Warnings, not gates**: a red run
signals, it does not block — there are no required checks and no branch
protection behind these lanes. The plan and the open points live in
`SecondBrain/000-Inbox/quality-gates-repo-personali.md`.

Public since 2026-10-16, so both private and public callers can use the lanes.

## Usage

    jobs:
      ci:
        uses: 10ax/ci-common/.github/workflows/python.yml@v1
        with:
          python-versions: '["3.11"]'
          lint: |
            python -m compileall -q app scripts
            ruff check .
          coverage-args: "--cov=app --cov=scripts"

Pin to `v1` — a moving major tag: a fix lands here once and every caller gets
it. That makes this repo's `main` the critical point of all the callers'
pipelines; keep it boring.

## Workflows

- `python.yml` — `lint` (default `ruff check .`), `test` (default `pytest -q`),
  optional `coverage` (enabled by `coverage-args`, red below
  `coverage-threshold`, default 90; also measures subprocesses — CLIs the suite
  runs through `subprocess` count). One leg per version in `python-versions`
  (JSON array). Inputs: `python-versions`, `cache` (default `pip`; empty
  disables it — repos without a manifest), `install`, `extras` (test/coverage
  only), `lint`, `test`, `coverage-args`, `coverage-threshold`.
- `node.yml` — install / lint / typecheck / test / build for pnpm repos
  (versions read from `packageManager` + `.node-version`); optional `coverage`
  command (the tool must already be a devDependency of the caller).
- `shell.yml` — `shellcheck` on the caller's `shellcheck-targets` plus
  `actionlint` (docker, pinned) on the caller's own workflows.

## Constraints

- No secrets are used by these workflows; don't add any.
