# wdmjr-fixtures

Ground-truth fixtures for [Why didn't my job run?](https://github.com/barbarkaragul-oss/why-didnt-my-job-run),
the GitHub Actions job-condition simulator. This repository records what GitHub Actions actually does,
so the simulator is tested against real runs instead of against what the documentation seems to say.

Nothing here is a product. The commits are meaningless ("docs: y", "feat: d"); they exist to trigger
events on branches, tags, pull requests, releases and issues.

## What is in it

| Workflow | What it settles |
|---|---|
| `fixture-a.yml` | 31 jobs of deliberately tricky conditions: fork checks, label checks, `null == false`, `inputs.deploy == 'true'`, `needs` chains with a failing build, `always()`, `!cancelled()`, `needs.x.result == 'skipped'`, `run_attempt`, case-insensitive `ref_name`, matrix include/exclude, step-level ifs. Its `context` job dumps `toJSON(github)` (with the job token removed) so the simulator replays the run with the exact context GitHub used. |
| `fixture-chain.yml`, `fixture-chain2.yml` | Status functions across a `needs` chain three hops deep, behind a failing job and behind a job skipped by its own condition. |
| `fixture-steps.yml` | Step-level `success()` / `failure()` after a failed step, with `continue-on-error`, and after a failed need. |
| `fixture-hashfiles.yml` | A job-level `hashFiles()`: GitHub rejects the whole file (a failed run with no jobs). |
| `filter-*.yml` | `on:` filters: branches, branches-ignore, tags, paths-ignore, pull_request default types, `types: [labeled]`, `branches` together with `branches-ignore` (rejected file), a `paths` list with only negative patterns (no run at all). |

## How it is used

`scripts/record-fixtures.ts` in the simulator repository fetches every completed run through the
Actions API: the event, the head SHA, the run conclusion, each job's and step's conclusion, the
changed files (from the commits or pulls API, since Actions payloads carry no file lists) and the
`github` context dumped by the `context` job. The recordings live in the simulator's
`tests/fixtures/runs/` and `tests/replay.test.ts` requires the simulator to reproduce every decision.

To record against your own copy: fork or copy the workflows, push a few events, and run
`FIXTURES_REPO=you/your-copy npx tsx scripts/record-fixtures.ts`.

## Note on old run logs

Run logs from before 2026-09-12 were deleted before this repository was made public, because the
context dump included the run's (expired) job token in base64. The dump now strips the token. The
runs themselves, with every job's conclusion, are still visible under Actions.

MIT.
