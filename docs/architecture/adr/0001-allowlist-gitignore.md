# ADR 0001: Allowlist .gitignore

## Status

Accepted.

## Context

The repository is the directory `~/.local/bin` itself, not a copy, so the
tracked scripts are the scripts the bar runs. That directory also holds
vendored binaries, symbolic links and helpers that touch credentials.

## Decision

[.gitignore](../../../.gitignore) ignores everything (`*`) and then names each
tracked file and directory with `!`.

## Consequences

- A stray `git add` cannot stage an unrelated file from `~/.local/bin`.
- A new script must be added to the allowlist before staging; otherwise
  `git add` skips it without a message (commit `9082671`).
- The allowlist is not a secret scanner ([README.md](../../../README.md)).

## Evidence

- Commit `2a743c2` ("Track the dwm status bar chain").
- Commit `9082671` ("Document the silent failure mode of the whitelist").
- [.gitignore](../../../.gitignore).
