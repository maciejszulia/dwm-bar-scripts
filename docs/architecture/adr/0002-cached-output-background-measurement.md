# ADR 0002: Cached output, background measurement

## Status

Accepted.

## Context

dwmblocks runs its blocks one after another in one thread. A block command
that ran `ping -c10` blocked the whole status, including the clock, for about
9 seconds in every 12 (comment in [ping-artix](../../../ping-artix)).

## Decision

`ping-artix` prints the last cached result at once. When `PING_EVERY` seconds
(default 12) have passed since the last start, it records the start time and
runs the measurement in a background subshell. The subshell writes a
temporary file and moves it over the cache with `mv`.

## Consequences

- The block returns in the time of a `cat`, independent of the network.
- The shown value is up to one measurement period old.
- The pattern is the same as in `weather`: cheap tick, expensive work out of
  band (`ponytail:` comment in [ping-artix](../../../ping-artix)).

## Evidence

- [ping-artix](../../../ping-artix).
- Commit `2a743c2` ("Track the dwm status bar chain"), which added the script to this repository.
