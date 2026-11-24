# ADR 0004: Self-tests inside the scripts

## Status

Accepted.

## Context

The scripts are single files in `~/.local/bin`. `weather` and `cpu` contain logic
that can break silently (forecast alerts, rotation, fan-level steps).

## Decision

- `weather --test` builds synthetic forecast files in a temporary directory
  and checks the output through the `WEATHER_*` overrides.
- `cpu --test` runs the script in a temporary `HOME` and `XDG_RUNTIME_DIR`
  and checks the fan-level steps.
- GitHub Actions runs `TZ=Europe/Warsaw ./weather --test` on every push to
  `main` and on pull requests.

## Consequences

- The test travels with the script; no test framework is needed.
- `weather --test` calls `pkill`; on a desktop it must run in an isolated
  namespace ([README.md](../../../README.md)).
- `cpu --test` still reads hardware, so it does not run in CI.

## Evidence

- Commit `9a8f639` ("Add a CI workflow that runs the weather self-test").
- `--test` branches in [weather](../../../weather) and [cpu](../../../cpu).
- [.github/workflows/ci.yml](../../../.github/workflows/ci.yml).
