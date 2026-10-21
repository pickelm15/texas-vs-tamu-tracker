# Texas vs. Texas A&M Tracker — Coaches & Quarterbacks

A personal tracker following the Texas Longhorns vs. Texas A&M Aggies rivalry — their head coaches and quarterbacks, with national efficiency rankings and series history.

**Live site:** https://pickelm15.github.io/texas-vs-tamu-tracker/

Current through **2026 Week 7 (Oct 17, 2026)**.

## What it tracks

Three views on one page:

1. **Head coaches** — Steve Sarkisian (Texas, since 2021) vs. Mike Elko (Texas A&M, since 2024) ranked by SP+ and FEI national efficiency ratings across every season of their tenure.
2. **2026 SEC quarterbacks** — all 16 starters, featuring Arch Manning (Texas) vs. Marcel Reed (Texas A&M) head-to-head, with a full 2025 baseline for comparison.
3. **A&M series history** — Texas A&M's all-time head-to-head record against each of its 15 SEC opponents.

## How it's built

One standalone HTML file (`index.html`) — no build step, no server, no dependencies. All data is inline as JavaScript constants near the top of the file. Charts are hand-built inline SVG. The whole thing runs by opening the file in a browser or hitting the GitHub Pages URL.

## How to update weekly

1. Open `index.html` in a text editor.
2. Find the constants near the top: `COACH`, `QBS`, `REED25`, `SEC_GAMES`, `SEC_TEAMS`, `UPDATED`.
3. Update the values with the latest numbers (sources below).
4. Change the `UPDATED` string to the new week label.
5. Commit and push — GitHub Pages redeploys the site within a minute.

```bash
git add index.html
git commit -m "Update to 2026 Week N"
git push
```

## Data sources

| Lens | Source |
|---|---|
| SP+ ratings | ESPN+ (Bill Connelly) |
| FEI ratings | BCF Toys (Brian Fremeau) |
| Game stats | ESPN gameId pages |
| A&M series history | Winsipedia |

## File layout

```
texas-vs-tamu-tracker/
├── index.html           ← the whole app (open in browser or hit the live URL)
├── README.md            ← you're reading it
├── .gitignore           ← files to keep out of git
└── docs/
    └── tracker-export.md ← full project handoff spec (sources, data model, update playbook)
```

## Known data gaps

- Some 2026 game sack counts are null (ESPN hadn't posted them at export time)
- FEI Week 7 values pending at export time
- BCF Toys rate-limiting required manual FEI entry for a few seasons

Documented in detail in `docs/tracker-export.md` §9.

## License

Personal project, no license. Not for redistribution.
