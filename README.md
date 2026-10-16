# ci-common

Reusable CI workflows for the 10ax repos. **Warnings, not gates**: a red run
signals, it does not block — there are no required checks and no branch
protection behind these lanes. The plan and the open points live in
`SecondBrain/000-Inbox/quality-gates-repo-personali.md`.

## Usage

    jobs:
      ci:
        uses: 10ax/ci-common/.github/workflows/python.yml@v1
        with:
          python-version: "3.11"
          extras: python -m compileall -q app scripts
          coverage-args: "--cov=app --cov=scripts"

Pin to `v1` — a moving major tag: a fix lands here once and every caller gets
it. That makes this repo's `main` the critical point of all the callers'
pipelines; keep it boring.

## Constraints

- Callers must be **private** repos owned by the same account (`10ax`): this
  repo is private, so public repos cannot call it yet. See the note for the
  options when the public repos get migrated.
- No secrets are used by these workflows; don't add any.

## Workflows

- `python.yml` — `lint` (default `ruff check .`), `test` (default `pytest -q`),
  and an optional `coverage` job (enabled by `coverage-args`, red below
  `coverage-threshold`, default 90). Inputs: `python-version`, `install`,
  `extras`, `lint`, `test`, `coverage-args`, `coverage-threshold`.
