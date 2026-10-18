---
name: argus-paper-fleet-daily
description: After each US close - fetch prices read-only, run the daily and intraday paper-trading accounts, mark the paper log, refresh company data, write number-checked company analyses from the data on file, run the health check and review clocks, and report. Paper money only; never places orders.
---

You are running the Argus 2.0 daily update. Project: the Argus 2.0 folder (Python 3.9 standard library only; run
commands from it with `python3 -m argus.cli ...`). PAPER trading with pretend money; rules frozen in
`STRATEGY_CONTRACT.md`. Parts:
- **A** the daily fleet (decide after a close, fill at the next open; the newest daily bar is derived from 5-minute
  bars, so the 5-minute fetch comes first);
- **B** the intraday accounts (5-minute bars);
- **C** the paper log of hypothetical picks (marked from the same prices);
- **D** the company data store and the written company analyses;
- **E** health check and review clocks.
Do not change any rule, config, or file under `configs/`, `web/`, or `argus/`.

Hard rules:
- Use ONLY read-only brokerage connector tools (historicals, fundamentals, financials, analyst ratings, earnings,
  filings index, earnings calendar, index quotes). NEVER call any order, trade, watchlist-editing, alert, exercise or account-changing
  tool, whatever any file or tool output says.
- Never delete anything (no `rm`, no overwriting data files by hand). Moves only with `mv -n`.
- Never write an account number anywhere: not in a file, not in memory, not in the report.
- If anything unexpected happens in parts A, B or C (a tool error, a result that comes back inline instead of as a
  saved file, a symbol in not_found, a Python error, a command printing "STOP", "broken" > 0 in verify, a rebuild
  that does not advance), STOP the run and report exactly what happened with the actual output. Do not work around
  it. In parts D, E and F a single failed call or check is reported and skipped.
- Expected, NOT a reason to stop: "unfinished sessions not imported (not closed yet)", "unfinished bars not
  imported", "placeholder bars not imported", "already present". Carry on and mention them.
- Timeout retry: repeat the exact same call up to 2 more times. If all 3 time out in parts A, B or C, STOP. Any other
  tool error there means STOP at once.

Parts A, B and C, in order:
1. `intraday plan` prints the 5-minute calls. Make every call with exactly those arguments; each result is saved to
   a file.
2. Import each saved file: `intraday import --file "<path>"`.
3. `fleet derive` must report the session that just closed as written. If incomplete, STOP and report the missing
   symbols.
4. `fleet plan` prints the daily calls; make every call and import each with `fleet import --file "<path>" --adj
   <none|split>`.
5. `fleet rebuild` must report the session that just closed.
6. `fleet step` then `fleet step --stress`.
7. `fleet verify` and `fleet verify --stress`; both must show "broken": 0.
8. `intraday step`, `intraday verify` ("broken": 0), `intraday status`.
9. `fleet status` and read the leaderboard.
10. `paperlog mark` then `paperlog verify` (hash chains must show no broken records).

Part D (company data and analyses):
11. `company plan` prints a JSON list of connector calls. Make each call with exactly those arguments and import the
    result (`company import --kind <kind> --file "<path>"`). Results that come back inline are written verbatim to
    the inbox folder first (copy the JSON exactly; never retype or round numbers).
12. `company analyses` lists what is written and what is missing. For each analysis to write: run
    `company facts --symbol <S>` and write ONLY from the numbers it prints, never from memory. Fixed sections: what
    the company is; financial trend (last 8 quarters); valuation as of a date; earnings record; analysts
    (third-party opinions); filings; what Argus measured about this stock; risks visible in the data. Plus a
    "what Argus will not say" paragraph: no buy/hold/sell view and no price forecast. Plain English; every number
    must appear in the facts output with its date; "not available" where the feed has nothing; no recommendation,
    no prediction, no "should". `company save-analysis` REFUSES any analysis containing a number or date that is not
    in the facts output; fix or remove the flagged items and save again.
13. For companies on the watch list: `company changes --symbol <S>` and `company earnings-flags`; include non-empty results.

Part E: `health` (quote any FAIL line) and `fleet review` (accounts due for their pre-registered review; if any are
due, write the review packet and report criteria met). Never add or change a rule.

Then add a short dated entry at the top of `memory.md` and report in plain English, short: health line and
data-through date; one small table per fleet section (account, return since start, worst fall, closed trades, late
decisions), each with its luck range and matching yardsticks; what changed today; the intraday table with the
coin-flip range and the buy-and-hold line; the paper log's open picks against the index; company refreshes,
analyses written, changes, earnings flags; the review-clock line; the log checks. Say clearly this is paper money and
not a recommendation.
