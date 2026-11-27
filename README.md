# dwm bar scripts

The block chain behind the dwm status bar on the T470p. Blocks are wired in
`~/src/dwmblocks/blocks.h`; the cheatsheet for the whole bar is
`~/sciagi/pasek-dwm.md`.

| Script | Block | Notes |
|---|---|---|
| `weather` | weather | Rotates facts from the forecast cache and blinks a red alert when the forecast turns. `weather --test` runs 20 checks; `weather --facts` prints the whole rotation; `weather --ack` clears an alert and is what a left click on the status bar spawns. |
| `ping-artix` | `PNG` | Prints a cached round-trip and forks the measurement. dwmblocks is single-threaded, so measuring inline froze the entire bar for 9 s out of every 12. |
| `cc-usage` | `CLD` | Claude rate-limit windows from `~/.claude/rate_limits.json`. |
| `codex-usage` | `CDX` | Codex rate limit, dug out of the newest rollout log that carries one. |
| `gpu` | `GPU` | Temperature and utilisation via `nvidia-smi`. |
| `cpu` | `CPU` | Package temperature, load since the last tick, mean core clock and fan `level:rpm`. Left click steps the fan floor up and wraps from max to auto, right click steps it down; the wheel steps up to max and down to auto, at most once per 300 ms, because one TrackPoint or touchpad gesture sends a burst of ticks. Clicks and the wheel reuse the last tick's readings. `cpu --test` runs 4 checks; refreshed by `RTMIN+14`. |
| `volume` | `VOL` | Reads PipeWire through `pactl`; refreshed by `RTMIN+10`. |
| `brightness` | `BRI` | Reads sysfs; `brightness-up` / `brightness-down` change it and signal `RTMIN+11`. |
| `pogoda` | — | Thin wrapper over the forecast CLI in `agrocamping-recepcja`, which `weather` reads through. |
| `energy` | weather | Home power cost from Energa "Mój Licznik" (G12w): today, this week, this month, this year, a forecast for the next 7 and 30 days from temperature, and how far each day strayed from the forecast made the day before. Its `energy facts` lines join the weather rotation. `energy report` prints the price breakdown, the model and the deviations; `energy --test` runs 10 checks. Login sits in `~/.config/energa/credentials` (2 lines, mode 600); the `energy` runit service runs `energy fetch` once an hour. |

Data the bar depends on comes from
`~/.local/state/agrocamping-recepcja/pogoda.json`, refreshed every 15 minutes by
the `agrocamping-pogoda` runit service.

## Why the repository sits inside `~/.local/bin`

To avoid a second copy that drifts from the running one. `.gitignore` ignores
everything and then names the files above, so the directory's binaries, symlinks
and credential-touching helpers stay out by construction.

The cost of that safety: **a new bar script has to be added to `.gitignore` as
well, or it will never be tracked.** `git add` skips an ignored file without a
word and the commit looks complete, so the miss is silent. Check `git ls-files`
against the block list in `~/src/dwmblocks/blocks.h` when adding one.
