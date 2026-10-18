# Argus paper fleet: daily update

After each US market close, runs a paper-trading research fleet end to end: read-only price fetch, daily and intraday accounts, hash-chain checks, number-checked company write-ups and a plain-English report.

**Type:** Claude scheduled task · **Area:** Investing research (paper money)

## What it does

- Fetches exactly the calls the engine plans, imports them, rebuilds, steps the normal and stress fleets, and verifies the hash chains (`broken` must be 0).
- Marks a paper log of hypothetical picks against the same prices.
- Refreshes company data and writes company analyses only from a facts command's output; the save step refuses any number or date not in that output.
- Runs the health check and the pre-registered review clocks, then reports results with luck ranges and baseline yardsticks.

## Guardrails

- Paper money only. Read-only brokerage tools; never any order, trade, alert or account-changing call.
- Never writes an account number anywhere. Never deletes; moves only with `mv -n`.
- Stops on anything unexpected in the core parts instead of working around it.
- Analyses give no buy/hold/sell view and no forecast.

## When it triggers

> After each US close - fetch prices read-only, run the daily and intraday paper-trading accounts, mark the paper log, refresh company data, write number-checked company analyses from the data on file, run the health check and review clocks, and report. Paper money only; never places orders.

## What's in this repo

- `SKILL.md`: the skill itself, the file Claude or Codex reads.
- Not included: Argus 2.0 itself and its strategy contract are private and not published.
- Written to be generic: where the skill relies on the user's own files, it names them by role, not by content.

## Install

This is a Claude scheduled-task prompt. Create a scheduled task in the Claude desktop app and paste the body of `SKILL.md` as its prompt, or save it as `~/.claude/scheduled-tasks/argus-paper-fleet-daily/SKILL.md`. It expects the project files it names to exist.
