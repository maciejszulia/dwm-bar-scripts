# ADR 0003: weather reads a local cache only

## Status

Accepted.

## Context

The weather block runs every second. A bar block must not reach the network.
A separate service writes the forecast cache (`pogoda.json`) and its history
log (`pogoda.jsonl`); the cache carries no sunrise or sunset times.

## Decision

- `weather` reads only the local cache and log. It does not call `pogoda` or
  fetch forecasts ([README.md](../../../README.md)).
- It rebuilds a facts file only when the cache is newer than the facts file
  or when `REBUILD` seconds (default 300) have passed (`fresh()`).
- It computes sunrise and sunset in `jq` with an almanac formula (`suntime()`
  in `FACTS_JQ`).

## Consequences

- A tick costs a file read and one `awk` call, except when a rebuild is due.
- The forecast is only as fresh as the external service keeps the cache.
- The commit message of `515096f` reports that the sun times were compared
  with Open-Meteo and the NOAA spreadsheet on four dates, with at most one
  minute of difference.

## Evidence

- Commit `515096f` ("feat(weather): sunrise and sunset on the bar, hours spelled h").
- Commit `cebe48f` ("docs: correct forecast flow and document isolated bar tests").
- `fresh()`, `rebuild()` and `FACTS_JQ` in [weather](../../../weather).
