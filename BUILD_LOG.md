# BUILD LOG — gta6-twitch-ccu-forecast-01 ("Forecast GTA VI's launch-day Twitch viewers hour by hour")

## 2026-11-18 ~01:07 WIB — task received
- Free entry, deadline 2026-11-19T01:00:00Z (08:00 WIB 19 Nov). Pot $250, top 20 paid.
- Account rapid-badger-43, payout wallet 3YZWsy7NA6rRByPkaUW5UGiRGbFDG8eK6anzAzj7NxYq.

## ~01:10 WIB — kit fetched, requirements read
- `curl` kit tarball 6,029,196 B (200) → `~/workspace/makefun/kits/gta6-twitch-ccu-forecast-01/`.
- competition.yaml + RULES.txt read. Task: program reads one case (`/input/<id>.in`),
  writes `/output/<id>.out` = 23 rows (hours 2..24) x 5 quantiles; pinball loss on
  log scale; 8 s/case; stdlib only; no network. House persistence baseline
  practice loss 0.387. Practice cases p00-p03 (Wolverine, RDR2, GoW Ragnarok,
  Spider-Man 2); no reference answers in kit (server grades).
- Starter main.py read for understanding only (baseline formula, already in yaml);
  NOT copied — entry written by hand below.

## ~01:15 WIB — offline model research (scripts in /tmp, NOT in entry)
- All 37 launch comparables have full 24 h release-day data. GTA VI pre-release
  comparable has no hour-1 print → excluded from fitting (also excluded at runtime).
- Key finding: corr(ln(hour-1 viewers), ln(peak/hour-1)) = -0.938. Hour-1 level
  predicts the launch-day explosion (rolling-midnight vs simultaneous unlock).
- Leave-one-launch-out pinball loss over 37 comparables (851 scored hours):
  - V0 persistence baseline: 0.9297 (0.3703 in 10k-500k hour-1 regime)
  - V1 median log-ratio + empirical quantiles: 0.7139
  - V3 per-hour OLS log-ratio ~ ln(hour-1), residual quantiles: 0.4377
  - V3 + spread x1.15 + predictor clamped to observed range +/-1.0 log: 0.4370
    (0.3256 in-regime); Theil-Sen 0.468; ln(hour-1/pre-release-baseline) 0.706;
    slope shrinkage / pooled residuals: worse. → Chose V3 + spread 1.15 + clamp +/-1.0.
- Model: per hour, q_tau(h) = p1 * exp(a_h + b_h*ln(p1) + 1.15*resid_q_tau(h)).

## ~01:20 WIB — `makefun init` BLOCKED (approval timeout)
- `git init -b main` OK. `makefun init --competition ... --wallet 3YZW...` hit a
  user-confirmation approval that timed out (~90 s) — nothing was written, no
  anchor line, no commits from init. Per instructions, NOT retried. Consequence:
  no `makefun anchor` line in this session → thinking_fresh will skip as a note
  (not a rejection). Parent/user may re-run init or accept the note. Repo built
  with plain local commits instead (LICENSE + code).

## ~01:25 WIB — entry written (by hand, in-session, 4 write messages)
- `main.py` (~250 lines): docstring + constants + helpers (write 1); regression
  forecast + fallback + main (append, write 2); overflow clamp fix (edit 3 —
  first version clamped the wrong quantity, corrected to clamp log of the
  absolute value); re-verified after edit (write 4 = this log, not code).
- `LICENSE` MIT (holder rapid-badger-43), `.makefun/owner.json` (payout wallet),
  `README.md` (method + LOO numbers).

## ~01:30 WIB — verification (all passed)
- Practice run: 4/4 cases, 0.28 s total, outputs valid (23 rows, hours 2..24,
  q strictly ordered, 0<q<=1e9, <=65,536 B). Deterministic: two runs byte-identical.
- Shipped-code LOO (through forecast_case itself): 0.4370 overall, 0.3256 in-regime
  — identical to the prototype, so the entry implements the validated model.
- Edge cases: no comparables → flat-median fallback valid; missing known → p1=1 valid.
- Practice medians sane: p00 peak ratio 8.0x (h21), p01 1.78x, p02 0.88x (already
  near peak in h1), p03 1.74x.
- No grade-local exists for this competition (kit: server grades only).

## ~01:35 WIB — commit + check
- Commit 1 f31193fbf6ee087475d501b774fca65fd941b190 (entry + LICENSE + README +
  owner.json + BUILD_LOG). Commit 2 d34d6dc6cada9dd026295e2a4d60ed1d522c95f0
  removed an accidentally committed __pycache__/main.cpython-312.pyc + .gitignore.
- `makefun check` (no transcripts yet): 3 passed (competition_open, snapshot,
  entrypoint), 2 failed — license_osi (LICENSE lacked the SPDX-License-Identifier
  line; fixed by adding `SPDX-License-Identifier: MIT` as line 1) and
  transcripts_present (expected: bundle is the parent's step after this run).
  13 skipped, 5 at-submission (repo_public, repo_owner, repo_created_after_open,
  commit_in_repo, duplicate_snapshot — all decided at submission; owner.json is
  committed, which covers repo_owner).
- Re-check after SPDX fix: (result recorded in final commit message / report).

## Status for parent (NOT submitted)
- Entry complete, verified, committed. DO NOT SUBMIT yet: needs (1) public GitHub
  repo created after 2026-11-17T06:00Z + push (gh not logged in — user-only),
  (2) transcript bundle via `makefun bundle` (parent step; needs --muse-usage
  rows from muse.db after this run ends + --system-prompt file + prompt file),
  (3) `makefun check --transcripts <bundle>` to pass, (4) user 'gas' + approval
  card for the authenticated submit, and note the @noval-amir connect is broken
  so submission would go under rapid-badger-43 unless the user approves otherwise.
- Known gap: no `makefun init` anchor (approval timed out, not retried) →
  thinking_fresh will skip as a note, not a rejection. The repo history starts
  with a plain local commit, not the drand anchor commit init would make.
- Deadline 2026-11-19T01:00:00Z (08:00 WIB 19 Nov). ~6.5 h left at ~01:35 WIB.
