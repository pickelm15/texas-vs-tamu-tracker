# tracker-export.md

Complete export of the **Texas vs. Texas A&M football tracker** for migration from Claude (claude.ai, Personal project) to Claude Code and a GitHub repo.

- Export date: October 21, 2026
- Data current through: Week 7 of the 2026 season (games of Oct. 17, 2026), with the exceptions listed in Section 9
- Companion file: `tracker-index.html` (the full, working source of the live page, exported alongside this document). Every number below was extracted programmatically from that file, so the two match exactly.

---

## 1. What this tracker is about

**Subject.** A head-to-head comparison of the two Texas SEC programs, Texas (Longhorns) and Texas A&M (Aggies), told through three lenses:

1. **Head coaches over time:** Steve Sarkisian (Texas, since 2021) vs. Mike Elko (Texas A&M, since 2024), measured by national SP+ and FEI ranks (overall, offense, defense, special teams) for every season of each coach's tenure, plus records and head-to-head results.
2. **Quarterbacks:** Arch Manning (Texas) vs. Marcel Reed (Texas A&M) as the featured head-to-head, set inside a leaderboard of all 16 SEC starting quarterbacks for 2026, with full game logs. The 2025 Manning vs. Reed season is kept as a baseline.
3. **SEC series history:** Texas A&M's all-time, game-by-game record against all 15 SEC opponents, styled after Winsipedia's head-to-head pages.

**Why it was built (as recorded).** The owner (Michael) wanted:

- A weekly, data-driven way to compare where Texas and Texas A&M stand, grounded in hard statistics and opponent-adjusted ratings rather than message-board opinion or narrative.
- A coach comparison that answers whether Elko's A&M is closing the gap on Sarkisian's Texas, both in the overlapping seasons (2024 on) and when each coach's first three seasons are lined up against each other.
- A marketing-quality, shareable page (it started as a chart and an Excel workbook, then became one interactive site) that is refreshed every week of the season.
- All Texas A&M football work living at **one link** (that is why the SEC series history was added as a tab instead of a separate page).

> Note for Claude Code: no deeper personal motivation was recorded beyond the points above. Ask Michael directly if the purpose matters for a design decision; do not infer one.

**Live locations (claude.ai, will not move to GitHub automatically):**

- Live tracker (interactive HTML artifact): https://claude.ai/artifact/85mJs9YFSppUsuvfEQ6nBY
- Companion Claude Doc, "Sarkisian at Texas vs. Elko at Texas A&M: SP+ and FEI": https://claude.ai/artifact/7B4gHC42Kr15Hc1Q9zSpd6 (published slug `31fe892c-3f09-489d-b021-188e3434c801`). **Stale:** frozen at 2026 Week 4. See Section 6.6.
- An earlier Excel workbook (formula-driven QB comparison with a Sources tab) was built in a separate chat in early October 2026. It is superseded by the tracker and is not included here.

---

## 2. What the tracker contains (page structure)

The page is a single self-contained HTML file with a hero banner (Sarkisian vs. Elko: record at school and current overall SP+ rank) and three tabs.

### Tab 1: Coaches

- **Rank chart:** line chart of national rank by season (rank 1 at top). Toggles: rating system (SP+ / FEI), unit (Overall / Offense / Defense / Special teams), and alignment (by calendar year, or by tenure year so each coach's Year 1, 2, 3 line up). Hover tooltips. Elko's pre-A&M years are labeled (2021 A&M DC; 2022 to 2023 Duke head coach) and not plotted.
- **Season faceoff:** pick a season chip; shows both coaches' ranks in all four units for both systems, a category count, a verdict line, and team records. For 2026 only, a **"Reality check" note** explains that SP+/FEI flatter Texas's offense versus raw production (added Oct. 19, 2026, at Michael's request).
- **Every rank, side by side:** heat-grid of every season x unit x system with a color scale.
- **Tale of the tape:** summary comparisons (records, average ranks in shared seasons and first three seasons, head-to-head).

### Tab 2: Quarterbacks

- **Manning vs. Reed spotlight:** toggle 2025 (full season) / 2026 (to date). Ten categories with mirrored bars and a categories-won count: Completion %, Passing yards, Yards per attempt, Passing TD, Interceptions (lower better), Passer rating, ESPN QBR, Rushing yards, Total TD, Sacks taken (lower better). A caveat note explains that NCAA passer rating flatters low-volume games and that QBR is the truer measure.
- **Efficiency map:** scatter of all 16 QBs, y = ESPN season QBR, x toggle = Yards per attempt / Completion % / Passer rating; dot size scales with total TDs; dashed group-average lines; Manning (orange) and Reed (maroon) highlighted.
- **Leaderboard:** sortable table of 16 QBs. Columns: Quarterback, Record, Cmp %, Pass yds, Y/A, TD, INT, Rating, QBR, SEC QBR rank, Rush yds, Rush TD, Total TD. Click a row to expand that QB's game log (date, opponent, result, C/A, yards, Y/A, TD, INT, sacks, game passer rating, game QBR). Asterisk on rush yards when a week's rushing figure is missing.

### Tab 3: SEC Series

- **Opponent picker:** chips for 15 opponents; defaults to the next SEC opponent (currently Alabama, tagged "Next up").
- **Series header:** A&M wins (pct), ties, opponent wins (pct), stacked bar, current win streak, meetings since, last result, next meeting, vacated-win note.
- **Head-to-head margin chart:** one bar per game (A&M wins above the line), toggle All time / SEC era (2012 on); vacated wins drawn dashed; ties as gray ticks; tooltips.
- **Highlights:** biggest win each way, last 10 meetings, longest streak each way, A&M record at home / road / neutral.
- **Record by decade** tiles, **Every game** collapsible log, and **All 15 at a glance** table (sortable by Win %, Most meetings, A to Z).

---

## 3. Data model (every field)

### 3.1 Coach seasons (`COACH`)

Per coach (`tx` = Sarkisian/Texas, `am` = Elko/Texas A&M): `name`, `school`, `start` (first season), and `seasons[year]` with:

| Field | Meaning |
|---|---|
| `rec` | Team record that season (W–L, en dash) |
| `sp` | SP+ national ranks `[overall, offense, defense, special teams]` |
| `fei` | FEI national ranks `[overall, offense, defense, special teams]` |

Ranks are 1 = best, out of roughly 130 to 138 FBS teams. Seasons: Sarkisian 2021 to 2026; Elko 2024 to 2026.

Other constants: `H2H` = head-to-head wins `{tx: 2, am: 0}` (Texas won 17–7 in 2024 and 27–17 in 2025; 2026 game is Nov. 27 at Kyle Field). `ELKO_PRIOR` = `{2021: "Texas A&M defensive coordinator", 2022: "Head coach at Duke", 2023: "Head coach at Duke"}`. `UPDATED` = `{week: 7, date: "October 17, 2026", feiWeek: 6}`.

### 3.2 Quarterback records (`QBS`, 16 objects)

| Field | Meaning |
|---|---|
| `name` | Player |
| `team / ab` | School and abbreviation |
| `side` | `tx` for Manning, `am` for Reed, absent for the other 14 (controls highlight colors) |
| `rec` | Team record (games the team played, not QB starts) |
| `c, a` | Completions, attempts (season) |
| `y` | Passing yards |
| `td, int` | Passing TD, interceptions |
| `sk` | Sacks taken (sum of known game counts; see gaps) |
| `qbr` | ESPN season Total QBR (currently the post-Week 6 table) |
| `qbrRk` | Rank of that QBR among the 16 SEC starters |
| `ra` | Rush attempts (not displayed; partially maintained, do not trust) |
| `ry, rtd` | Rushing yards and rushing TD |
| `rushNote` | Optional string; when present, rushing yards are missing the named week(s) and an asterisk shows |
| `games[]` | Game log rows |

Game row fields: `d` (M/D date), `opp` ("vs"/"at" + AP rank at the time + opponent), `res` (W/L score, team score first), `c`, `a`, `y`, `td`, `int`, `sk` (null = unknown), `qbr` (game QBR, null = not published). Bye or did-not-play rows are `{d, bye: true, note}`.

**Derived values (computed in the page, never stored):** `pct = c/a`; `ypa = y/a`; `rtg` = NCAA passer rating `(8.4*y + 330*td + 100*c - 200*int) / a`; `tot = td + rtd`; `ratio = td/int`. Game passer rating uses the same formula per row.

### 3.3 2025 baseline (`REED25`)

Full-season 2025 lines for Manning and Reed with the same stat fields plus `qbr` and PFF grade text (`pff`, `pffNote`).

### 3.4 SEC series (`SEC_GAMES`, `SEC_TEAMS`)

`SEC_GAMES[slug]` = array of games in ascending order: `[season, A&M points, opponent points, site, note]`. Bowl games are listed under their **season** year (e.g., Jan. 1968 Cotton Bowl = 1967). Notes include bowl names, OT, and "... win vacated".

`SEC_TEAMS[slug]` = `n` (display name), `short`, `c` (team color, `cd` dark-mode variant), `home` (cities counted as the opponent's home; Little Rock for Arkansas and Jackson for Ole Miss count as their home), `next` (next 2026 meeting text), `vac` (team has vacated wins). College Station is A&M home; anything else is neutral.

**Rules (match Winsipedia):** official records remove NCAA-vacated wins from the opponent's total (the game still shows on the chart, dashed). Ties and vacated games end a streak. Win % for A&M = wins / all meetings; opponent win % = official wins / (meetings minus vacated). The at-a-glance table uses (W + 0.5T) / (W + L + T) with official losses.

---

## 4. Where the data comes from

### Ratings

- SP+ (Bill Connelly, ESPN): https://www.espn.com/college-football/story/_/id/49868647/2026-college-football-sp+-rankings-all-138-fbs-teams . 2026 Week 6 ranks were read from CFB Update's full mirror table https://cfbupdate.com/sp-ratings ; Week 7 from ESPN's updated table. Watch for mirror staleness (on Oct. 19 the mirror showed Week 7 records with Week 6 ratings).
- FEI (Brian Fremeau, BCF Toys): https://bcftoys.com/2026-fei , plus per-unit pages used for history: https://bcftoys.com/2025-ofei , https://bcftoys.com/2025-dfei , https://bcftoys.com/2024-ofei , https://bcftoys.com/2024-dfei , https://bcftoys.com/2024-sfei , and team page https://bcftoys.com/texas-am . BCF Toys rate-limits; Michael personally supplied three values when the site blocked access: Elko 2024 overall FEI 21, 2025 overall FEI 11, 2025 special-teams FEI 33.

### Quarterbacks

- ESPN player pages, game logs and **game box scores** (primary). Per-game QBR comes from each game's ESPN box score (the box score shows a QBR column).
- ESPN SEC QBR table: https://www.espn.com/college-football/qbr/_/group/8 (JavaScript-rendered; usually read through Vols Wire / Yahoo weekly "SEC football QBR ratings" articles, e.g. https://sports.yahoo.com/articles/2026-sec-football-qbr-ratings-183026353.html for post-Week 6).
- Cross-checks: CBS Sports gametrackers (show in-game and season lines), FOX Sports box scores, team athletic-site box scores (texaslonghorns.com, 12thman.com, rolltide.com, georgiadogs.com, lsusports.net, olemisssports.com, utsports.com, soonersports.com, arkansasrazorbacks.com PDFs), AP recaps.
- 2025 baseline: ESPN season stats; PFF grades from PFF; 2025 QBR from Saturday Down South's efficiency table and Hook 'em Headlines.
- Context for the Texas offense note: 247Sports (yards per play 104th, yards per rush 119th, 20+ yard plays 126th after the Florida loss), Texas official cumulative stats (361 ypg, 5.4 yards per play), TruMedia via theScore (Manning 69th of 136 in EPA per dropback).

### SEC series

- Winsipedia head-to-head pages and full game lists, e.g. https://www.winsipedia.com/texas-am/vs/arkansas and https://www.winsipedia.com/games/arkansas/vs/texas-am (game-list URL pattern is `games/{alphabetically-first}/vs/{second}`). Team index: https://www.winsipedia.com/texas-am
- 2026 results not yet on Winsipedia's lists came from ESPN and 12thman.com (Arkansas 34–7, LSU 6–35, Kentucky 21–31, Missouri 6–27).
- 2026 A&M schedule: Wikipedia 2026 Texas A&M Aggies football team page.

### ESPN game IDs used for 2026 Weeks 6 and 7 (box score: `https://www.espn.com/college-football/boxscore/_/gameId/{id}`)

| Game | ESPN gameId |
|---|---|
| Missouri 27, Texas A&M 6 (10/10) | 401856716 |
| Oklahoma 27, Texas 24 (10/10) | 401856717 |
| Georgia 20, Alabama 17 (10/10) | 401856712 |
| Tennessee 24, Arkansas 17 (10/10) | 401856713 |
| South Carolina 38, Florida 19 (10/10) | 401856714 |
| Kentucky 35, LSU 34 OT (10/10) | 401856715 |
| Ole Miss 52, Vanderbilt 17 (10/10) | 401856718 |
| Auburn 27, Georgia 24 (10/17) | 401856719 |
| LSU 31, Mississippi State 0 (10/17) | 401856720 |
| Ole Miss 31, Missouri 3 (10/17) | 401856721 |
| Kentucky 21, Oklahoma 13 (10/17) | 401856722 |
| Tennessee 26, Alabama 10 (10/17) | 401856723 |
| Florida 27, Texas 24 (10/17) | 401856724 |
| Texas A&M 41, The Citadel 0 (10/17) | 401856725 |
| Arkansas 31, Vanderbilt 21 (10/17) | 401856726 |

---

## 5. Format the data lives in

- **Today:** JavaScript constants embedded in one self-contained HTML file (`tracker-index.html`): `UPDATED`, `COACH`, `H2H`, `ELKO_PRIOR`, `QBS` (JSON array), `REED25`, `SEC_UPDATED`, `SEC_NEXT`, `SEC_GAMES` (JSON object), `SEC_TEAMS`. No external data files or database. Charts are hand-built inline SVG; no libraries.
- The companion Claude Doc holds two rank tables and a summary table (prose document, not data).
- Weekly updates have been done by editing those constants and republishing.
- **Recommended for the repo:** move data into `data/coaches.json`, `data/qbs-2026.json`, `data/qbs-2025.json`, `data/sec-series.json`, `data/sec-teams.json`, `data/meta.json`, and have the page load them at build time. Appendix A gives those exact JSON payloads.

---

## 6. All records currently held

### 6.1 Coach ratings by season (national ranks)

| Season | Coach | Record | SP+ Ovr | SP+ Off | SP+ Def | SP+ ST | FEI Ovr | FEI Off | FEI Def | FEI ST |
|---|---|---|---|---|---|---|---|---|---|---|
| 2021 | Sarkisian (Texas) | 5–7 | 48 | 29 | 86 | 6 | 34 | 35 | 47 | 31 |
| 2022 | Sarkisian (Texas) | 8–5 | 7 | 28 | 15 | 44 | 6 | 17 | 8 | 8 |
| 2023 | Sarkisian (Texas) | 12–2 | 6 | 6 | 13 | 17 | 7 | 19 | 6 | 3 |
| 2024 | Sarkisian (Texas) | 13–3 | 7 | 13 | 2 | 117 | 5 | 18 | 3 | 35 |
| 2024 | Elko (Texas A&M) | 8–5 | 12 | 20 | 19 | 19 | 21 | 29 | 23 | 12 |
| 2025 | Sarkisian (Texas) | 10–3 | 17 | 30 | 18 | 31 | 19 | 32 | 10 | 15 |
| 2025 | Elko (Texas A&M) | 11–2 | 10 | 11 | 14 | 97 | 11 | 15 | 9 | 33 |
| 2026 | Sarkisian (Texas) | 4–2 | 8 | 19 | 5 | 49 | 8 | 27 | 3 | 66 |
| 2026 | Elko (Texas A&M) | 4–3 | 33 | 46 | 31 | 17 | 28 | 54 | 26 | 1 |

Notes: 2021 to 2025 are final-season ranks, except 2024 SP+, which is Connelly's post-semifinal release. Elko's 2025 overall SP+ rank was read from ESPN's table order (not a labeled rank). 2026 SP+ is through Week 7; 2026 FEI is through Week 6 (Fremeau's Week 7 update was not posted as of Oct. 19). In-season 2026 ranks still blend preseason projections.

Career records shown in the hero: Sarkisian 52–22 at Texas; Elko 23–10 at Texas A&M (both through Week 7, 2026; the hero sums the season records).

Head-to-head: Sarkisian 2, Elko 0 (2024: Texas 17–7; 2025: Texas 27–17 in Austin). 2026 meeting: Nov. 27 at Kyle Field.

### 6.2 2025 baseline: Manning vs. Reed (full season)

| QB | Record | C | A | Yds | TD | INT | Sacks | Rush att | Rush yds | Rush TD | QBR | PFF |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Arch Manning | 10–3 | 248 | 404 | 3,163 | 26 | 7 | 23 | 92 | 399 | 10 | 70.6 | 87.3 overall (top 15 nationally) |
| Marcel Reed | 11–2 | 234 | 377 | 3,169 | 25 | 12 | 17 | 104 | 493 | 6 | 77.9 | 64.0 passing (105th nationally) |

### 6.3 2026 SEC quarterback season totals (through Week 7)

| QB | Team | Rec | C | A | Cmp% | Yds | Y/A | TD | INT | Sk* | Rating | QBR (post-Wk 6) | SEC QBR rk | Rush yds | Rush TD | Note |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Gunner Stockton | Georgia | 6-1 | 117 | 158 | 74.1 | 1,570 | 9.9 | 16 | 4 | 6 | 185.9 | 90.4 | 1 | 160 | 1 |  |
| Keelon Russell | Alabama | 5-2 | 129 | 191 | 67.5 | 1,746 | 9.1 | 12 | 4 | 15 | 160.9 | 90.0 | 2 | 231 | 5 |  |
| Kamario Taylor | Mississippi State | 4-2 | 102 | 154 | 66.2 | 1,450 | 9.4 | 17 | 4 | 13 | 176.6 | 83.3 | 3 | 159 | 1 |  |
| Trinidad Chambliss | Ole Miss | 5-1 | 142 | 210 | 67.6 | 1,676 | 8.0 | 14 | 4 | 5 | 152.8 | 83.0 | 4 | 127 | 6 |  |
| Sam Leavitt | LSU | 5-2 | 148 | 211 | 70.1 | 1,784 | 8.5 | 14 | 8 | 7 | 155.5 | 81.1 | 5 | 270 | 7 |  |
| Aaron Philo | Florida | 5-2 | 105 | 149 | 70.5 | 1,341 | 9.0 | 9 | 3 | 5 | 162.0 | 74.1 | 6 | 104 | 3 |  |
| Austin Simmons | Missouri | 5-2 | 132 | 209 | 63.2 | 1,773 | 8.5 | 14 | 2 | 8 | 154.6 | 71.5 | 7 | 18 | 0 |  |
| Arch Manning | Texas | 4-2 | 106 | 166 | 63.9 | 1,295 | 7.8 | 11 | 5 | 17 | 145.2 | 65.5 | 8 | 102 | 0 |  |
| Byrum Brown | Auburn | 4-2 | 119 | 185 | 64.3 | 1,205 | 6.5 | 8 | 7 | 18 | 125.7 | 64.8 | 9 | 459 | 5 |  |
| LaNorris Sellers | South Carolina | 3-3 | 115 | 187 | 61.5 | 1,338 | 7.2 | 12 | 4 | 7 | 138.5 | 62.5 | 10 | 411 | 2 |  |
| Jared Curtis | Vanderbilt | 3-4 | 101 | 161 | 62.7 | 1,242 | 7.7 | 6 | 7 | 11 | 131.1 | 57.9 | 11 | 131 | 2 | Week 7 rushing yards not yet available |
| Kenny Minchey | Kentucky | 6-1 | 141 | 206 | 68.4 | 1,552 | 7.5 | 13 | 1 | 11 | 151.6 | 57.1 | 12 | 9 | 1 |  |
| John Mateer | Oklahoma | 3-3 | 114 | 175 | 65.1 | 1,308 | 7.5 | 9 | 7 | 11 | 136.9 | 55.8 | 13 | 78 | 0 |  |
| Faizon Brandon | Tennessee | 6-1 | 61 | 101 | 60.4 | 697 | 6.9 | 7 | 1 | 15 | 139.3 | 47.0 | 14 | 130 | 3 |  |
| KJ Jackson | Arkansas | 3-4 | 131 | 215 | 60.9 | 1,444 | 6.7 | 7 | 4 | 12 | 124.4 | 46.9 | 15 | 140 | 3 |  |
| Marcel Reed | Texas A&M | 4-3 | 112 | 201 | 55.7 | 1,227 | 6.1 | 7 | 6 | 12 | 112.5 | 45.5 | 16 | 175 | 2 |  |

*Sacks are the sum of known game counts; some games have no published count (shown as `null` in the logs).

### 6.4 2026 game logs (every QB, every game)

#### Arch Manning (Texas)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/5 | vs Texas State | W 59-7 | 20/27 | 305 | 4 | 1 | 1 | 210.4 | 92.6 |
| 9/12 | vs No. 1 Ohio State | W 24-23 | 23/37 | 195 | 1 | 1 | 2 | 109.9 | 80.6 |
| 9/19 | vs UTSA | W 30-6 | 16/32 | 161 | 2 | 0 | 1 | 112.9 | 27.4 |
| 9/26 | at No. 14 Tennessee | W 20-17 | 14/19 | 201 | 0 | 1 | 6 | 152.0 | 53.0 |
| 10/3 | Bye week | | | | | | | | |
| 10/10 | vs Oklahoma (Dallas) | L 24-27 | 19/31 | 260 | 3 | 2 | 2 | 150.8 | 71.0 |
| 10/17 | vs Florida | L 24-27 | 14/20 | 173 | 1 | 0 | 5 | 159.2 | 20.6 |

#### Marcel Reed (Texas A&M)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/5 | vs Missouri State | W 50-0 | 21/30 | 233 | 2 | 0 | 2 | 157.2 | 58.2 |
| 9/12 | vs Arizona State | W 48-20 | 15/25 | 163 | 3 | 1 | 2 | 146.4 | 46.0 |
| 9/19 | vs Kentucky | L 21-31 | 26/49 | 236 | 1 | 2 | 0 | 92.1 | 62.3 |
| 9/26 | at No. 10 LSU | L 6-35 | 10/26 | 106 | 0 | 1 | 5 | 65.0 | 31.3 |
| 10/3 | vs Arkansas | W 34-7 | 13/19 | 170 | 0 | 0 | 0 | 143.6 | 82.2 |
| 10/10 | at No. 14 Missouri | L 6-27 | 19/39 | 189 | 0 | 2 | 3 | 79.2 | 18.2 |
| 10/17 | vs The Citadel (FCS) | W 41-0 | 8/13 | 130 | 1 | 0 | n/a | 170.9 | 75.5 |

#### John Mateer (Oklahoma)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/4 | vs UTEP | W 51-0 | 11/17 | 225 | 3 | 0 | 0 | 234.1 | 84.4 |
| 9/12 | at Michigan | L 10-17 | 17/33 | 189 | 1 | 1 | 3 | 103.6 | 52.0 |
| 9/19 | vs New Mexico | W 14-6 | 19/25 | 238 | 2 | 2 | 1 | 166.4 | 60.2 |
| 9/26 | at No. 2 Georgia | L 13-41 | 25/34 | 231 | 2 | 1 | 3 | 144.1 | 44.4 |
| 10/3 | Bye week | | | | | | | | |
| 10/10 | vs No. 1 Texas (Dallas) | W 27-24 | 18/26 | 155 | 0 | 0 | 3 | 119.3 | 48.6 |
| 10/17 | vs No. 15 Kentucky | L 13-21 | 24/40 | 270 | 1 | 3 | n/a | 110.0 | 50.2 |

#### Keelon Russell (Alabama)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/5 | vs East Carolina | W 48-10 | 18/30 | 256 | 0 | 0 | 1 | 131.7 | 82.2 |
| 9/12 | at Kentucky | W 45-17 | 16/23 | 188 | 1 | 2 | 4 | 135.2 | 44.4 |
| 9/19 | vs Florida State | W 50-36 | 23/30 | 320 | 3 | 0 | 1 | 199.3 | 97.7 |
| 9/26 | vs South Carolina | W 49-18 | 21/27 | 365 | 4 | 0 | 2 | 240.2 | 97.6 |
| 10/3 | at No. 16 Mississippi State | W 56-23 | 16/22 | 285 | 3 | 0 | 3 | 226.5 | n/a |
| 10/10 | vs No. 2 Georgia | L 17-20 | 16/30 | 185 | 1 | 0 | 4 | 116.1 | 77.8 |
| 10/17 | at No. 14 Tennessee | L 10-26 | 19/29 | 147 | 0 | 2 | n/a | 94.3 | 66.3 |

#### Aaron Philo (Florida)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/5 | vs FAU | W 66-21 | 16/21 | 275 | 3 | 1 | 0 | 223.8 | 94.0 |
| 9/12 | vs Campbell (FCS) | W 52-3 | 16/21 | 242 | 2 | 0 | 0 | 204.4 | 76.9 |
| 9/19 | at Auburn | W 44-39 | 15/22 | 204 | 1 | 1 | 2 | 152.0 | 88.7 |
| 9/26 | vs No. 4 Ole Miss | W 52-28 | 16/23 | 196 | 1 | 0 | 0 | 155.5 | 71.5 |
| 10/3 | at No. 25 Missouri | L 17-45 | 21/34 | 197 | 1 | 0 | 3 | 120.1 | n/a |
| 10/10 | vs South Carolina | L 19-38 | 21/28 | 227 | 1 | 1 | n/a | 147.7 | 31.9 |
| 10/17 | Did not play at No. 7 Texas (shoulder); Tramell Jones Jr. started: 22 of 35, 155 yds, 2 TD, W 27-24 | | | | | | | | |

#### Kamario Taylor (Mississippi State)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/5 | vs ULM | W 62-13 | 22/34 | 354 | 4 | 0 | 0 | 191.0 | 89.8 |
| 9/12 | at Minnesota | W 38-13 | 16/22 | 227 | 3 | 0 | 1 | 204.4 | 90.0 |
| 9/19 | at South Carolina | W 41-34 | 18/26 | 251 | 4 | 1 | 3 | 193.4 | 83.3 |
| 9/26 | vs No. 19 Missouri | W 31-24 | 27/38 | 360 | 3 | 1 | 4 | 171.4 | 85.1 |
| 10/3 | vs No. 7 Alabama | L 23-56 | 19/34 | 258 | 3 | 2 | 5 | 137.0 | n/a |
| 10/10 | Bye week | | | | | | | | |
| 10/17 | Did not play at No. 22 LSU (injury); Parker Puckett started, L 0-31 | | | | | | | | |

#### Sam Leavitt (LSU)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/5 | vs Clemson | W 51-10 | 16/28 | 230 | 1 | 1 | 0 | 130.8 | 91.1 |
| 9/12 | vs Louisiana Tech | W 45-14 | 25/38 | 344 | 1 | 3 | 5 | 134.7 | 42.1 |
| 9/19 | at No. 8 Ole Miss | L 24-32 | 16/27 | 158 | 1 | 1 | 2 | 113.2 | 86.1 |
| 9/26 | vs No. 23 Texas A&M | W 35-6 | 17/23 | 312 | 2 | 1 | 0 | 207.9 | 91.6 |
| 10/3 | vs McNeese State (FCS) | W 63-14 | 18/23 | 176 | 3 | 1 | 0 | 176.9 | n/a |
| 10/10 | at Kentucky | L 34-35 OT | 25/34 | 275 | 2 | 1 | n/a | 155.0 | 82.5 |
| 10/17 | vs No. 16 Mississippi State | W 31-0 | 31/38 | 289 | 4 | 0 | n/a | 180.2 | n/a |

#### Trinidad Chambliss (Ole Miss)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/6 | vs No. 24 Louisville | W 41-38 | 21/36 | 336 | 3 | 1 | 0 | 158.7 | 72.4 |
| 9/12 | vs Charlotte | W 41-9 | 23/26 | 225 | 2 | 0 | 1 | 186.5 | 73.2 |
| 9/19 | vs No. 7 LSU | W 32-24 | 33/48 | 363 | 2 | 1 | 2 | 141.9 | 92.6 |
| 9/26 | at No. 21 Florida | L 28-52 | 25/34 | 298 | 1 | 1 | 1 | 151.0 | 91.1 |
| 10/3 | Bye week | | | | | | | | |
| 10/10 | at Vanderbilt | W 52-17 | 20/30 | 213 | 3 | 1 | 1 | 152.6 | 91.1 |
| 10/17 | vs No. 13 Missouri | W 31-3 | 20/36 | 241 | 3 | 0 | n/a | 139.3 | 80.4 |

#### Gunner Stockton (Georgia)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/5 | vs Tennessee State (FCS) | W 63-3 | 15/16 | 202 | 3 | 0 | 0 | 261.7 | 87.0 |
| 9/12 | vs WKU | W 70-20 | 13/16 | 234 | 5 | 0 | 0 | 307.2 | 95.9 |
| 9/19 | at Arkansas | W 45-17 | 10/13 | 147 | 1 | 0 | 3 | 197.3 | 97.5 |
| 9/26 | vs Oklahoma | W 41-13 | 14/25 | 171 | 1 | 1 | 1 | 118.7 | 67.7 |
| 10/3 | vs Vanderbilt | W 38-14 | 25/32 | 295 | 3 | 0 | 1 | 186.5 | n/a |
| 10/10 | at No. 6 Alabama | W 20-17 | 18/22 | 249 | 1 | 1 | 1 | 182.8 | 94.4 |
| 10/17 | vs Auburn | L 24-27 | 22/34 | 272 | 2 | 2 | n/a | 139.6 | 64.6 |

#### KJ Jackson (Arkansas)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/5 | vs North Alabama (FCS) | W 31-14 | 24/35 | 267 | 3 | 1 | 3 | 155.2 | 32.0 |
| 9/12 | at No. 20 Utah | L 10-43 | 14/34 | 139 | 0 | 1 | 1 | 69.6 | 41.5 |
| 9/19 | vs No. 2 Georgia | L 17-45 | 20/27 | 212 | 1 | 0 | 2 | 152.3 | 69.1 |
| 9/26 | vs Tulsa | W 34-6 | 24/36 | 309 | 1 | 1 | 3 | 142.4 | 71.2 |
| 10/3 | at Texas A&M | L 7-34 | 13/25 | 111 | 1 | 1 | 1 | 94.5 | 24.2 |
| 10/10 | vs No. 15 Tennessee | L 17-24 | 15/27 | 155 | 1 | 0 | 2 | 116.0 | n/a |
| 10/17 | at Vanderbilt | W 31-21 | 21/31 | 251 | 0 | 0 | 0 | 135.8 | n/a |

#### Austin Simmons (Missouri)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/3 | vs Arkansas-Pine Bluff (FCS) | W 54-14 | 17/19 | 252 | 4 | 0 | 1 | 270.4 | 93.6 |
| 9/11 | at Kansas | W 38-21 | 19/29 | 292 | 3 | 0 | 0 | 184.2 | 86.0 |
| 9/19 | vs Troy | W 27-17 | 11/20 | 108 | 1 | 0 | 1 | 116.9 | 19.1 |
| 9/26 | at No. 24 Mississippi State | L 24-31 | 23/39 | 295 | 3 | 0 | 2 | 147.9 | 73.0 |
| 10/3 | vs No. 8 Florida | W 45-17 | 23/30 | 340 | 2 | 0 | 2 | 193.9 | 91.5 |
| 10/10 | vs Texas A&M | W 27-6 | 14/28 | 147 | 1 | 1 | n/a | 98.7 | 45.7 |
| 10/17 | at No. 9 Ole Miss | L 3-31 | 25/44 | 339 | 0 | 1 | 2 | 117.0 | 63.2 |

#### Byrum Brown (Auburn)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/5 | vs Baylor (Atlanta) | W 17-16 | 26/35 | 259 | 0 | 3 | 5 | 119.3 | 55.1 |
| 9/12 | vs Southern Miss | W 43-8 | 18/29 | 226 | 2 | 0 | 1 | 150.3 | 80.5 |
| 9/19 | vs Florida | L 39-44 | 20/35 | 271 | 3 | 1 | 7 | 144.8 | 73.9 |
| 9/26 | vs Vanderbilt | W 21-15 | 18/27 | 140 | 1 | 1 | 1 | 115.0 | 71.4 |
| 10/3 | at No. 17 Tennessee | L 14-24 | 22/33 | 159 | 1 | 2 | 4 | 105.0 | n/a |
| 10/10 | Bye week | | | | | | | | |
| 10/17 | at No. 1 Georgia | W 27-24 | 15/26 | 150 | 1 | 0 | n/a | 118.8 | 86.6 |

#### Jared Curtis (Vanderbilt)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/5 | vs Austin Peay (FCS), relief | W | 7/9 | 60 | 0 | 0 | n/a | 133.8 | n/a |
| 9/12 | vs Delaware, relief | W | 14/22 | 210 | 1 | 0 | n/a | 158.8 | n/a |
| 9/19 | vs NC State | W 35-31 | 20/34 | 277 | 3 | 2 | 0 | 144.6 | 50.6 |
| 9/26 | at Auburn | L 15-21 | 22/33 | 199 | 0 | 1 | 4 | 111.3 | 77.1 |
| 10/3 | Did not play at No. 2 Georgia (bruised knee); Blaze Berlowitz started, L 14-38 | | | | | | | | |
| 10/10 | vs No. 9 Ole Miss | L 17-52 | 19/32 | 222 | 1 | 2 | 3 | 115.5 | 47.5 |
| 10/17 | vs Arkansas | L 21-31 | 19/31 | 274 | 1 | 2 | 1 | 133.3 | n/a |

#### LaNorris Sellers (South Carolina)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/5 | vs Kent State | W 57-0 | 19/23 | 270 | 3 | 0 | 1 | 224.3 | 91.9 |
| 9/12 | vs Towson (FCS) | W 45-9 | 10/23 | 96 | 0 | 1 | 1 | 69.8 | 28.0 |
| 9/19 | vs Mississippi State | L 34-41 | 25/40 | 282 | 2 | 2 | 3 | 128.2 | 37.7 |
| 9/26 | at No. 8 Alabama | L 18-49 | 18/30 | 185 | 1 | 1 | 1 | 116.1 | 79.4 |
| 10/3 | vs No. 24 Kentucky | L 34-35 OT | 20/36 | 226 | 2 | 0 | 1 | 126.6 | 54.4 |
| 10/10 | at No. 16 Florida | W 38-19 | 23/35 | 279 | 4 | 0 | n/a | 170.4 | 83.6 |
| 10/17 | Bye week | | | | | | | | |

#### Kenny Minchey (Kentucky)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/5 | vs Youngstown State (FCS) | W 45-13 | 18/27 | 301 | 4 | 0 | 0 | 209.2 | 66.1 |
| 9/12 | vs No. 12 Alabama | L 17-45 | 18/31 | 146 | 1 | 1 | 4 | 101.8 | 8.1 |
| 9/19 | at No. 9 Texas A&M | W 31-21 | 14/18 | 252 | 2 | 0 | 2 | 232.0 | 90.0 |
| 9/26 | vs South Alabama | W 45-21 | 19/25 | 177 | 1 | 0 | 2 | 148.7 | 8.5 |
| 10/3 | at South Carolina | W 35-34 OT | 28/41 | 272 | 3 | 0 | 3 | 148.2 | 77.9 |
| 10/10 | vs No. 10 LSU | W 35-34 OT | 23/36 | 246 | 0 | 0 | n/a | 121.3 | 80.9 |
| 10/17 | at Oklahoma | W 21-13 | 21/28 | 158 | 2 | 0 | n/a | 146.0 | 71.5 |

#### Faizon Brandon (Tennessee)

| Date | Opponent | Result | C/A | Yds | TD | INT | Sk | Game rating | Game QBR |
|---|---|---|---|---|---|---|---|---|---|
| 9/5 | vs Furman (FCS) | W 56-9 | 13/17 | 226 | 3 | 0 | 1 | 246.4 | 87.2 |
| 9/12 | at Georgia Tech | W 45-24 | 10/21 | 148 | 2 | 0 | 1 | 138.2 | 78.7 |
| 9/19 | vs Kennesaw State | W 42-9 | 18/26 | 178 | 1 | 0 | 1 | 139.4 | 46.4 |
| 9/26 | vs No. 1 Texas | L 17-20 | 17/29 | 145 | 1 | 0 | 10 | 112.0 | n/a |
| 10/3 | vs Auburn (benched in 2nd quarter) | W 24-14 | 3/8 | 0 | 0 | 1 | 2 | 12.5 | n/a |
| 10/10 | Did not play at Arkansas (shoulder); George MacIntyre started: 18 of 29, 247 yds, 1 TD, 2 INT, W 24-17 | | | | | | | | |
| 10/17 | Did not play vs No. 6 Alabama (shoulder); George MacIntyre started: 21 of 31, 197 yds, 0 TD, 1 INT, W 26-10 | | | | | | | | |

### 6.5 SEC series: Texas A&M vs. all 15 opponents

Through Oct. 19, 2026 (Week 7). Next SEC opponent: Alabama (Oct. 24 at Tuscaloosa).

| Opponent | Meetings | First | A&M W | Opp W (official) | Ties | Vacated opp wins | Current streak | Last meeting | Next 2026 meeting |
|---|---|---|---|---|---|---|---|---|---|
| Alabama | 16 | 1941 | 3 | 13 | 0 | 0 | Alabama 2 (2022–2023) | 2023: Alabama 26–20 | Oct. 24 at Tuscaloosa |
| Arkansas | 83 | 1903 | 38 | 42 | 3 | 0 | A&M 5 (2022–2026) | 2026: A&M 34–7 | none scheduled |
| Auburn | 16 | 1911 | 9 | 7 | 0 | 0 | A&M 1 (2025) | 2025: A&M 16–10 | none scheduled |
| Florida | 8 | 1962 | 5 | 3 | 0 | 0 | A&M 2 (2024–2025) | 2025: A&M 34–17 | none scheduled |
| Georgia | 6 | 1950 | 3 | 3 | 0 | 0 | Georgia 3 (1980–2019) | 2019: Georgia 19–13 | none scheduled |
| Kentucky | 4 | 1952 | 2 | 2 | 0 | 0 | Kentucky 1 (2026) | 2026: Kentucky 31–21 | none scheduled |
| LSU | 65 | 1899 | 25 | 33 | 3 | 4 | LSU 1 (2026) | 2026: LSU 35–6 | none scheduled |
| Mississippi State | 19 | 1912 | 10 | 8 | 0 | 1 | A&M 3 (2023–2025) | 2025: A&M 31–9 | none scheduled |
| Missouri | 19 | 1957 | 11 | 8 | 0 | 0 | Missouri 1 (2026) | 2026: Missouri 27–6 | none scheduled |
| Oklahoma | 31 | 1903 | 12 | 19 | 0 | 0 | A&M 1 (2012) | 2012: A&M 41–13 | Nov. 21 at Norman |
| Ole Miss | 15 | 1911 | 9 | 4 | 0 | 2 | Ole Miss 3 (2021–2023) | 2023: Ole Miss 38–35 | none scheduled |
| South Carolina | 12 | 2014 | 10 | 2 | 0 | 0 | A&M 1 (2025) | 2025: A&M 31–30 | Nov. 7 at Columbia, S.C. |
| Tennessee | 5 | 1957 | 2 | 3 | 0 | 0 | Tennessee 1 (2023) | 2023: Tennessee 20–13 | Nov. 14 at Kyle Field |
| Texas | 120 | 1894 | 37 | 78 | 5 | 0 | Texas 3 (2011–2025) | 2025: Texas 27–17 | Nov. 27 at Kyle Field |
| Vanderbilt | 3 | 2013 | 3 | 0 | 0 | 0 | A&M 3 (2013–2020) | 2020: A&M 17–12 | none scheduled |

Vacated wins: LSU 2012, 2013, 2014, 2015; Ole Miss 2014, 2016; Mississippi State 2018. On-field records: LSU 25–37–3, Ole Miss 9–6, Mississippi State 10–9.

#### Every game, by opponent

Columns: season (bowls listed under season year), A&M score, opponent score, result for A&M, site, note.

##### Alabama (16 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 1941 | 21 | 29 | L | Dallas, TX | Cotton Bowl |
| 1967 | 20 | 16 | W | Dallas, TX | Cotton Bowl |
| 1985 | 10 | 23 | L | Birmingham, AL |  |
| 1988 | 10 | 30 | L | College Station, TX |  |
| 2012 | 29 | 24 | W | Tuscaloosa, AL |  |
| 2013 | 42 | 49 | L | College Station, TX |  |
| 2014 | 0 | 59 | L | Tuscaloosa, AL |  |
| 2015 | 23 | 41 | L | College Station, TX |  |
| 2016 | 14 | 33 | L | Tuscaloosa, AL |  |
| 2017 | 19 | 27 | L | College Station, TX |  |
| 2018 | 23 | 45 | L | Tuscaloosa, AL |  |
| 2019 | 28 | 47 | L | College Station, TX |  |
| 2020 | 24 | 52 | L | Tuscaloosa, AL |  |
| 2021 | 41 | 38 | W | College Station, TX |  |
| 2022 | 20 | 24 | L | Tuscaloosa, AL |  |
| 2023 | 20 | 26 | L | College Station, TX |  |

##### Arkansas (83 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 1903 | 6 | 0 | W | Houston, TX |  |
| 1910 | 0 | 5 | L | Fayetteville, AR |  |
| 1912 | 27 | 0 | W | Dallas, TX |  |
| 1927 | 40 | 6 | W | College Station, TX |  |
| 1928 | 12 | 27 | L | Fayetteville, AR |  |
| 1929 | 13 | 14 | L | College Station, TX |  |
| 1930 | 0 | 13 | L | Little Rock, AR |  |
| 1934 | 7 | 7 | T | College Station, TX |  |
| 1935 | 7 | 14 | L | Little Rock, AR |  |
| 1936 | 0 | 18 | L | College Station, TX |  |
| 1937 | 13 | 26 | L | Fayetteville, AR |  |
| 1938 | 13 | 7 | W | College Station, TX |  |
| 1939 | 27 | 0 | W | Fayetteville, AR |  |
| 1940 | 17 | 0 | W | College Station, TX |  |
| 1941 | 7 | 0 | W | Little Rock, AR |  |
| 1942 | 41 | 0 | W | College Station, TX |  |
| 1943 | 13 | 0 | W | Fayetteville, AR |  |
| 1944 | 6 | 7 | L | College Station, TX |  |
| 1945 | 34 | 0 | W | Fayetteville, AR |  |
| 1946 | 0 | 7 | L | College Station, TX |  |
| 1947 | 21 | 21 | T | Fayetteville, AR |  |
| 1948 | 6 | 28 | L | College Station, TX |  |
| 1949 | 6 | 27 | L | Fayetteville, AR |  |
| 1950 | 42 | 13 | W | College Station, TX |  |
| 1951 | 21 | 33 | L | Fayetteville, AR |  |
| 1952 | 31 | 12 | W | College Station, TX |  |
| 1953 | 14 | 41 | L | Little Rock, AR |  |
| 1954 | 7 | 14 | L | College Station, TX |  |
| 1955 | 7 | 7 | T | Fayetteville, AR |  |
| 1956 | 27 | 0 | W | College Station, TX |  |
| 1957 | 7 | 6 | W | Fayetteville, AR |  |
| 1958 | 8 | 21 | L | College Station, TX |  |
| 1959 | 7 | 12 | L | Fayetteville, AR |  |
| 1960 | 3 | 7 | L | College Station, TX |  |
| 1961 | 8 | 15 | L | Fayetteville, AR |  |
| 1962 | 7 | 17 | L | College Station, TX |  |
| 1963 | 7 | 21 | L | Little Rock, AR |  |
| 1964 | 0 | 17 | L | College Station, TX |  |
| 1965 | 0 | 31 | L | Little Rock, AR |  |
| 1966 | 0 | 34 | L | College Station, TX |  |
| 1967 | 33 | 21 | W | Fayetteville, AR |  |
| 1968 | 22 | 25 | L | College Station, TX |  |
| 1969 | 13 | 35 | L | Fayetteville, AR |  |
| 1970 | 6 | 45 | L | College Station, TX |  |
| 1971 | 17 | 9 | W | Little Rock, AR |  |
| 1972 | 10 | 7 | W | College Station, TX |  |
| 1973 | 10 | 14 | L | Fayetteville, AR |  |
| 1974 | 20 | 10 | W | College Station, TX |  |
| 1975 | 6 | 31 | L | Little Rock, AR |  |
| 1976 | 31 | 10 | W | Little Rock, AR |  |
| 1977 | 20 | 26 | L | College Station, TX |  |
| 1978 | 7 | 26 | L | Little Rock, AR |  |
| 1979 | 10 | 22 | L | College Station, TX |  |
| 1980 | 24 | 27 | L | Fayetteville, AR |  |
| 1981 | 7 | 10 | L | College Station, TX |  |
| 1982 | 0 | 35 | L | Little Rock, AR |  |
| 1983 | 36 | 23 | W | College Station, TX |  |
| 1984 | 0 | 28 | L | Fayetteville, AR |  |
| 1985 | 10 | 6 | W | College Station, TX |  |
| 1986 | 10 | 14 | L | Little Rock, AR |  |
| 1987 | 14 | 0 | W | College Station, TX |  |
| 1988 | 20 | 25 | L | Fayetteville, AR |  |
| 1989 | 22 | 23 | L | College Station, TX |  |
| 1990 | 20 | 16 | W | Fayetteville, AR |  |
| 1991 | 13 | 3 | W | College Station, TX |  |
| 2009 | 19 | 47 | L | Arlington, TX |  |
| 2010 | 17 | 24 | L | Arlington, TX |  |
| 2011 | 38 | 42 | L | Arlington, TX |  |
| 2012 | 58 | 10 | W | College Station, TX |  |
| 2013 | 45 | 33 | W | Fayetteville, AR |  |
| 2014 | 35 | 28 | W | Arlington, TX | OT |
| 2015 | 28 | 21 | W | Arlington, TX | OT |
| 2016 | 45 | 24 | W | Arlington, TX |  |
| 2017 | 50 | 43 | W | Arlington, TX | OT |
| 2018 | 24 | 17 | W | Arlington, TX |  |
| 2019 | 31 | 27 | W | Arlington, TX |  |
| 2020 | 42 | 31 | W | College Station, TX |  |
| 2021 | 10 | 20 | L | Arlington, TX |  |
| 2022 | 23 | 21 | W | Arlington, TX |  |
| 2023 | 34 | 22 | W | Arlington, TX |  |
| 2024 | 21 | 17 | W | Arlington, TX |  |
| 2025 | 45 | 42 | W | Fayetteville, AR |  |
| 2026 | 34 | 7 | W | College Station, TX |  |

##### Auburn (16 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 1911 | 16 | 0 | W | Dallas, TX |  |
| 1985 | 36 | 16 | W | Dallas, TX | Cotton Bowl |
| 2012 | 63 | 21 | W | Auburn, AL |  |
| 2013 | 41 | 45 | L | College Station, TX |  |
| 2014 | 41 | 38 | W | Auburn, AL |  |
| 2015 | 10 | 26 | L | College Station, TX |  |
| 2016 | 29 | 16 | W | Auburn, AL |  |
| 2017 | 27 | 42 | L | College Station, TX |  |
| 2018 | 24 | 28 | L | Auburn, AL |  |
| 2019 | 20 | 28 | L | College Station, TX |  |
| 2020 | 31 | 20 | W | Auburn, AL |  |
| 2021 | 20 | 3 | W | College Station, TX |  |
| 2022 | 10 | 13 | L | Auburn, AL |  |
| 2023 | 27 | 10 | W | College Station, TX |  |
| 2024 | 41 | 43 | L | Auburn, AL | 4OT |
| 2025 | 16 | 10 | W | College Station, TX |  |

##### Florida (8 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 1962 | 6 | 42 | L | Gainesville, FL |  |
| 1976 | 37 | 14 | W | El Paso, TX | Sun Bowl |
| 2012 | 17 | 20 | L | College Station, TX |  |
| 2017 | 19 | 17 | W | Gainesville, FL |  |
| 2020 | 41 | 38 | W | College Station, TX |  |
| 2022 | 24 | 41 | L | College Station, TX |  |
| 2024 | 33 | 20 | W | Gainesville, FL |  |
| 2025 | 34 | 17 | W | College Station, TX |  |

##### Georgia (6 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 1950 | 40 | 20 | W | College Park, MD | Presidential Cup Bowl |
| 1953 | 14 | 12 | W | Dallas, TX |  |
| 1954 | 6 | 0 | W | Athens, GA |  |
| 1980 | 0 | 42 | L | Athens, GA |  |
| 2009 | 20 | 44 | L | Shreveport, LA | Independence Bowl |
| 2019 | 13 | 19 | L | Athens, GA |  |

##### Kentucky (4 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 1952 | 7 | 10 | L | College Station, TX |  |
| 1953 | 7 | 6 | W | Lexington, KY |  |
| 2018 | 20 | 14 | W | College Station, TX | OT |
| 2026 | 21 | 31 | L | College Station, TX |  |

##### LSU (65 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 1899 | 52 | 0 | W | College Station, TX |  |
| 1906 | 22 | 12 | W | Baton Rouge, LA |  |
| 1907 | 11 | 5 | W | College Station, TX |  |
| 1908 | 0 | 26 | L | New Orleans, LA |  |
| 1913 | 7 | 7 | T | Houston, TX |  |
| 1914 | 63 | 9 | W | Dallas, TX |  |
| 1916 | 0 | 13 | L | Galveston, TX |  |
| 1917 | 27 | 0 | W | San Antonio, TX |  |
| 1920 | 0 | 0 | T | College Station, TX |  |
| 1921 | 0 | 6 | L | Baton Rouge, LA |  |
| 1922 | 46 | 0 | W | College Station, TX |  |
| 1923 | 28 | 0 | W | Baton Rouge, LA |  |
| 1942 | 7 | 16 | L | Baton Rouge, LA |  |
| 1943 | 28 | 13 | W | Baton Rouge, LA |  |
| 1943 | 14 | 19 | L | Miami, FL | Orange Bowl |
| 1944 | 7 | 0 | W | Baton Rouge, LA |  |
| 1945 | 12 | 31 | L | Baton Rouge, LA |  |
| 1946 | 9 | 33 | L | Baton Rouge, LA |  |
| 1947 | 13 | 19 | L | Baton Rouge, LA |  |
| 1948 | 13 | 14 | L | Baton Rouge, LA |  |
| 1949 | 0 | 34 | L | Baton Rouge, LA |  |
| 1955 | 28 | 0 | W | Dallas, TX |  |
| 1956 | 9 | 6 | W | Baton Rouge, LA |  |
| 1960 | 0 | 9 | L | Baton Rouge, LA |  |
| 1961 | 7 | 16 | L | Baton Rouge, LA |  |
| 1962 | 0 | 21 | L | Baton Rouge, LA |  |
| 1963 | 6 | 14 | L | Baton Rouge, LA |  |
| 1964 | 6 | 9 | L | Baton Rouge, LA |  |
| 1965 | 0 | 10 | L | Baton Rouge, LA |  |
| 1966 | 7 | 7 | T | Baton Rouge, LA |  |
| 1967 | 6 | 17 | L | Baton Rouge, LA |  |
| 1968 | 12 | 13 | L | Baton Rouge, LA |  |
| 1969 | 6 | 35 | L | Baton Rouge, LA |  |
| 1970 | 20 | 18 | W | Baton Rouge, LA |  |
| 1971 | 0 | 37 | L | Baton Rouge, LA |  |
| 1972 | 17 | 42 | L | Baton Rouge, LA |  |
| 1973 | 23 | 28 | L | Baton Rouge, LA |  |
| 1974 | 21 | 14 | W | Baton Rouge, LA |  |
| 1975 | 39 | 8 | W | Baton Rouge, LA |  |
| 1986 | 17 | 35 | L | Baton Rouge, LA |  |
| 1987 | 3 | 17 | L | College Station, TX |  |
| 1988 | 0 | 27 | L | Baton Rouge, LA |  |
| 1989 | 28 | 16 | W | College Station, TX |  |
| 1990 | 8 | 17 | L | Baton Rouge, LA |  |
| 1991 | 45 | 7 | W | College Station, TX |  |
| 1992 | 31 | 22 | W | Baton Rouge, LA |  |
| 1993 | 24 | 0 | W | College Station, TX |  |
| 1994 | 18 | 13 | W | Baton Rouge, LA |  |
| 1995 | 33 | 17 | W | College Station, TX |  |
| 2010 | 24 | 41 | L | Arlington, TX | Cotton Bowl |
| 2012 | 19 | 24 | L | College Station, TX | LSU win vacated |
| 2013 | 10 | 34 | L | Baton Rouge, LA | LSU win vacated |
| 2014 | 17 | 23 | L | College Station, TX | LSU win vacated |
| 2015 | 7 | 19 | L | Baton Rouge, LA | LSU win vacated |
| 2016 | 39 | 54 | L | College Station, TX |  |
| 2017 | 21 | 45 | L | Baton Rouge, LA |  |
| 2018 | 74 | 72 | W | College Station, TX | 7OT |
| 2019 | 7 | 50 | L | Baton Rouge, LA |  |
| 2020 | 20 | 7 | W | College Station, TX |  |
| 2021 | 24 | 27 | L | Baton Rouge, LA |  |
| 2022 | 38 | 23 | W | College Station, TX |  |
| 2023 | 30 | 42 | L | Baton Rouge, LA |  |
| 2024 | 38 | 23 | W | College Station, TX |  |
| 2025 | 49 | 25 | W | Baton Rouge, LA |  |
| 2026 | 6 | 35 | L | Baton Rouge, LA |  |

##### Mississippi State (19 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 1912 | 41 | 7 | W | Houston, TX |  |
| 1913 | 0 | 6 | L | Dallas, TX |  |
| 1915 | 0 | 7 | L | Dallas, TX |  |
| 1937 | 14 | 0 | W | Tyler, TX |  |
| 2000 | 41 | 43 | L | Shreveport, LA | Independence Bowl (OT) |
| 2012 | 38 | 13 | W | Starkville, MS |  |
| 2013 | 51 | 41 | W | College Station, TX |  |
| 2014 | 31 | 48 | L | Starkville, MS |  |
| 2015 | 30 | 17 | W | College Station, TX |  |
| 2016 | 28 | 35 | L | Starkville, MS |  |
| 2017 | 14 | 35 | L | College Station, TX |  |
| 2018 | 13 | 28 | L | Starkville, MS | MSU win vacated |
| 2019 | 49 | 30 | W | College Station, TX |  |
| 2020 | 28 | 14 | W | Starkville, MS |  |
| 2021 | 22 | 26 | L | College Station, TX |  |
| 2022 | 24 | 42 | L | Starkville, MS |  |
| 2023 | 51 | 10 | W | College Station, TX |  |
| 2024 | 34 | 24 | W | Starkville, MS |  |
| 2025 | 31 | 9 | W | College Station, TX |  |

##### Missouri (19 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 1957 | 28 | 0 | W | Columbia, MO |  |
| 1958 | 12 | 0 | W | College Station, TX |  |
| 1992 | 26 | 13 | W | Columbia, MO |  |
| 1993 | 73 | 0 | W | College Station, TX |  |
| 1998 | 17 | 14 | W | College Station, TX |  |
| 1999 | 51 | 14 | W | Columbia, MO |  |
| 2002 | 27 | 33 | L | College Station, TX |  |
| 2003 | 22 | 45 | L | Columbia, MO |  |
| 2006 | 25 | 19 | W | College Station, TX |  |
| 2007 | 26 | 40 | L | Columbia, MO |  |
| 2010 | 9 | 30 | L | College Station, TX |  |
| 2011 | 31 | 38 | L | College Station, TX | OT |
| 2012 | 59 | 29 | W | College Station, TX |  |
| 2013 | 21 | 28 | L | Columbia, MO |  |
| 2014 | 27 | 34 | L | College Station, TX |  |
| 2021 | 35 | 14 | W | Columbia, MO |  |
| 2024 | 41 | 10 | W | College Station, TX |  |
| 2025 | 38 | 17 | W | Columbia, MO |  |
| 2026 | 6 | 27 | L | Columbia, MO |  |

##### Oklahoma (31 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 1903 | 0 | 6 | L | Bryan, TX |  |
| 1907 | 19 | 0 | W | College Station, TX |  |
| 1909 | 14 | 8 | W | Dallas, TX |  |
| 1912 | 28 | 6 | W | Houston, TX |  |
| 1944 | 14 | 21 | L | Oklahoma City, OK |  |
| 1945 | 19 | 14 | W | Norman, OK |  |
| 1946 | 7 | 10 | L | Norman, OK |  |
| 1947 | 14 | 26 | L | Norman, OK |  |
| 1948 | 14 | 42 | L | Norman, OK |  |
| 1949 | 13 | 33 | L | Norman, OK |  |
| 1950 | 28 | 34 | L | Norman, OK |  |
| 1951 | 14 | 7 | W | College Station, TX |  |
| 1993 | 14 | 44 | L | Norman, OK |  |
| 1994 | 36 | 14 | W | College Station, TX |  |
| 1996 | 33 | 16 | W | College Station, TX |  |
| 1997 | 51 | 7 | W | Norman, OK |  |
| 1998 | 29 | 0 | W | College Station, TX |  |
| 1999 | 6 | 51 | L | Norman, OK |  |
| 2000 | 31 | 35 | L | College Station, TX |  |
| 2001 | 10 | 31 | L | Norman, OK |  |
| 2002 | 30 | 26 | W | College Station, TX |  |
| 2003 | 0 | 77 | L | Norman, OK |  |
| 2004 | 35 | 42 | L | College Station, TX |  |
| 2005 | 30 | 36 | L | Norman, OK |  |
| 2006 | 16 | 17 | L | College Station, TX |  |
| 2007 | 14 | 42 | L | Norman, OK |  |
| 2008 | 28 | 66 | L | College Station, TX |  |
| 2009 | 10 | 65 | L | Norman, OK |  |
| 2010 | 33 | 19 | W | College Station, TX |  |
| 2011 | 25 | 41 | L | Norman, OK |  |
| 2012 | 41 | 13 | W | Arlington, TX | Cotton Bowl |

##### Ole Miss (15 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 1911 | 17 | 0 | W | College Station, TX |  |
| 1914 | 14 | 7 | W | Beaumont, TX |  |
| 1975 | 7 | 0 | W | College Station, TX |  |
| 1980 | 23 | 20 | W | Jackson, MS |  |
| 2012 | 30 | 27 | W | Oxford, MS |  |
| 2013 | 41 | 38 | W | Oxford, MS |  |
| 2014 | 20 | 35 | L | College Station, TX | Ole Miss win vacated |
| 2015 | 3 | 23 | L | Oxford, MS |  |
| 2016 | 28 | 29 | L | College Station, TX | Ole Miss win vacated |
| 2017 | 31 | 24 | W | Oxford, MS |  |
| 2018 | 38 | 24 | W | College Station, TX |  |
| 2019 | 24 | 17 | W | Oxford, MS |  |
| 2021 | 19 | 29 | L | Oxford, MS |  |
| 2022 | 28 | 31 | L | College Station, TX |  |
| 2023 | 35 | 38 | L | Oxford, MS |  |

##### South Carolina (12 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 2014 | 52 | 28 | W | Columbia, SC |  |
| 2015 | 35 | 28 | W | College Station, TX |  |
| 2016 | 24 | 13 | W | Columbia, SC |  |
| 2017 | 24 | 17 | W | College Station, TX |  |
| 2018 | 26 | 23 | W | Columbia, SC |  |
| 2019 | 30 | 6 | W | College Station, TX |  |
| 2020 | 48 | 3 | W | Columbia, SC |  |
| 2021 | 44 | 14 | W | College Station, TX |  |
| 2022 | 24 | 30 | L | Columbia, SC |  |
| 2023 | 30 | 17 | W | College Station, TX |  |
| 2024 | 20 | 44 | L | Columbia, SC |  |
| 2025 | 31 | 30 | W | College Station, TX |  |

##### Tennessee (5 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 1957 | 0 | 3 | L | Jacksonville, FL | Gator Bowl |
| 2004 | 7 | 38 | L | Dallas, TX | Cotton Bowl |
| 2016 | 45 | 38 | W | College Station, TX | 2OT |
| 2020 | 34 | 13 | W | Knoxville, TN |  |
| 2023 | 13 | 20 | L | Knoxville, TN |  |

##### Texas (120 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 1894 | 0 | 38 | L | Austin, TX |  |
| 1898 | 0 | 48 | L | Austin, TX |  |
| 1899 | 0 | 6 | L | San Antonio, TX |  |
| 1900 | 0 | 5 | L | San Antonio, TX |  |
| 1900 | 0 | 11 | L | Austin, TX |  |
| 1901 | 0 | 17 | L | San Antonio, TX |  |
| 1901 | 0 | 32 | L | Austin, TX |  |
| 1902 | 0 | 0 | T | San Antonio, TX |  |
| 1902 | 12 | 0 | W | Austin, TX |  |
| 1903 | 6 | 29 | L | Austin, TX |  |
| 1904 | 6 | 34 | L | Austin, TX |  |
| 1905 | 0 | 27 | L | Austin, TX |  |
| 1906 | 0 | 24 | L | Austin, TX |  |
| 1907 | 0 | 0 | T | Dallas, TX |  |
| 1907 | 6 | 11 | L | Austin, TX |  |
| 1908 | 8 | 24 | L | Houston, TX |  |
| 1908 | 12 | 28 | L | Austin, TX |  |
| 1909 | 23 | 0 | W | Houston, TX |  |
| 1909 | 5 | 0 | W | Austin, TX |  |
| 1910 | 14 | 8 | W | Houston, TX |  |
| 1911 | 0 | 6 | L | Houston, TX |  |
| 1915 | 13 | 0 | W | College Station, TX |  |
| 1916 | 7 | 21 | L | Austin, TX |  |
| 1917 | 7 | 0 | W | College Station, TX |  |
| 1918 | 0 | 7 | L | Austin, TX |  |
| 1919 | 7 | 0 | W | College Station, TX |  |
| 1920 | 3 | 7 | L | Austin, TX |  |
| 1921 | 0 | 0 | T | College Station, TX |  |
| 1922 | 14 | 7 | W | Austin, TX |  |
| 1923 | 0 | 6 | L | College Station, TX |  |
| 1924 | 0 | 7 | L | Austin, TX |  |
| 1925 | 28 | 0 | W | College Station, TX |  |
| 1926 | 5 | 14 | L | Austin, TX |  |
| 1927 | 28 | 7 | W | College Station, TX |  |
| 1928 | 0 | 19 | L | Austin, TX |  |
| 1929 | 13 | 0 | W | College Station, TX |  |
| 1930 | 0 | 26 | L | Austin, TX |  |
| 1931 | 7 | 6 | W | College Station, TX |  |
| 1932 | 0 | 21 | L | Austin, TX |  |
| 1933 | 10 | 10 | T | College Station, TX |  |
| 1934 | 0 | 13 | L | Austin, TX |  |
| 1935 | 20 | 6 | W | College Station, TX |  |
| 1936 | 0 | 7 | L | Austin, TX |  |
| 1937 | 7 | 0 | W | College Station, TX |  |
| 1938 | 6 | 7 | L | Austin, TX |  |
| 1939 | 20 | 0 | W | College Station, TX |  |
| 1940 | 0 | 7 | L | Austin, TX |  |
| 1941 | 0 | 23 | L | College Station, TX |  |
| 1942 | 6 | 12 | L | Austin, TX |  |
| 1943 | 13 | 27 | L | College Station, TX |  |
| 1944 | 0 | 6 | L | Austin, TX |  |
| 1945 | 10 | 20 | L | College Station, TX |  |
| 1946 | 7 | 24 | L | Austin, TX |  |
| 1947 | 13 | 32 | L | College Station, TX |  |
| 1948 | 14 | 14 | T | Austin, TX |  |
| 1949 | 14 | 42 | L | College Station, TX |  |
| 1950 | 0 | 17 | L | Austin, TX |  |
| 1951 | 22 | 21 | W | College Station, TX |  |
| 1952 | 12 | 32 | L | Austin, TX |  |
| 1953 | 12 | 21 | L | College Station, TX |  |
| 1954 | 13 | 22 | L | Austin, TX |  |
| 1955 | 6 | 21 | L | College Station, TX |  |
| 1956 | 34 | 21 | W | Austin, TX |  |
| 1957 | 7 | 9 | L | College Station, TX |  |
| 1958 | 0 | 27 | L | Austin, TX |  |
| 1959 | 17 | 20 | L | College Station, TX |  |
| 1960 | 14 | 21 | L | Austin, TX |  |
| 1961 | 0 | 25 | L | College Station, TX |  |
| 1962 | 3 | 13 | L | Austin, TX |  |
| 1963 | 13 | 15 | L | College Station, TX |  |
| 1964 | 7 | 26 | L | Austin, TX |  |
| 1965 | 17 | 21 | L | College Station, TX |  |
| 1966 | 14 | 22 | L | Austin, TX |  |
| 1967 | 10 | 7 | W | College Station, TX |  |
| 1968 | 14 | 35 | L | Austin, TX |  |
| 1969 | 12 | 49 | L | College Station, TX |  |
| 1970 | 14 | 52 | L | Austin, TX |  |
| 1971 | 14 | 34 | L | College Station, TX |  |
| 1972 | 3 | 38 | L | Austin, TX |  |
| 1973 | 13 | 42 | L | College Station, TX |  |
| 1974 | 3 | 32 | L | Austin, TX |  |
| 1975 | 20 | 10 | W | College Station, TX |  |
| 1976 | 27 | 3 | W | Austin, TX |  |
| 1977 | 28 | 57 | L | College Station, TX |  |
| 1978 | 7 | 22 | L | Austin, TX |  |
| 1979 | 13 | 7 | W | College Station, TX |  |
| 1980 | 24 | 14 | W | Austin, TX |  |
| 1981 | 13 | 21 | L | College Station, TX |  |
| 1982 | 16 | 53 | L | Austin, TX |  |
| 1983 | 13 | 45 | L | College Station, TX |  |
| 1984 | 37 | 12 | W | Austin, TX |  |
| 1985 | 42 | 10 | W | College Station, TX |  |
| 1986 | 16 | 3 | W | Austin, TX |  |
| 1987 | 20 | 13 | W | College Station, TX |  |
| 1988 | 28 | 24 | W | Austin, TX |  |
| 1989 | 21 | 10 | W | College Station, TX |  |
| 1990 | 27 | 28 | L | Austin, TX |  |
| 1991 | 31 | 14 | W | College Station, TX |  |
| 1992 | 34 | 13 | W | Austin, TX |  |
| 1993 | 18 | 9 | W | College Station, TX |  |
| 1994 | 34 | 10 | W | Austin, TX |  |
| 1995 | 6 | 16 | L | College Station, TX |  |
| 1996 | 15 | 51 | L | Austin, TX |  |
| 1997 | 27 | 16 | W | College Station, TX |  |
| 1998 | 24 | 26 | L | Austin, TX |  |
| 1999 | 20 | 16 | W | College Station, TX |  |
| 2000 | 17 | 43 | L | Austin, TX |  |
| 2001 | 7 | 21 | L | College Station, TX |  |
| 2002 | 20 | 50 | L | Austin, TX |  |
| 2003 | 15 | 46 | L | College Station, TX |  |
| 2004 | 13 | 26 | L | Austin, TX |  |
| 2005 | 29 | 40 | L | College Station, TX |  |
| 2006 | 12 | 7 | W | Austin, TX |  |
| 2007 | 38 | 30 | W | College Station, TX |  |
| 2008 | 9 | 49 | L | Austin, TX |  |
| 2009 | 39 | 49 | L | College Station, TX |  |
| 2010 | 24 | 17 | W | Austin, TX |  |
| 2011 | 25 | 27 | L | College Station, TX |  |
| 2024 | 7 | 17 | L | College Station, TX |  |
| 2025 | 17 | 27 | L | Austin, TX |  |

##### Vanderbilt (3 games)

| Season | A&M | Opp | Result | Site | Note |
|---|---|---|---|---|---|
| 2013 | 56 | 24 | W | College Station, TX |  |
| 2015 | 25 | 0 | W | Nashville, TN |  |
| 2020 | 17 | 12 | W | College Station, TX |  |

### 6.6 Companion Claude Doc contents (stale, through 2026 Week 4)

The Doc "Sarkisian at Texas vs. Elko at Texas A&M: SP+ and FEI" (dated Oct. 1, 2026) holds the same 2021 to 2025 rank tables as Section 6.1 plus a 2026 row frozen at Week 4: Texas 4–0, SP+ 7 / 16 / 5 / 22, FEI 8 / 23 / 3 / 33; A&M 2–2, SP+ 39 / 38 / 44 / 74, FEI 21 / 33 / 27 / 3. Its summary table (as of Week 4): record in shared seasons Sarkisian 27–6 vs. Elko 21–9; average overall SP+ rank in shared seasons 10.3 vs. 20.3; FEI 10.7 vs. 17.7; first three seasons record 25–14 vs. 21–9; first-three-seasons SP+ 20.3 vs. 20.3; FEI 15.7 vs. 17.7; head-to-head 2–0; career at school 52–20 vs. 21–9.

**Conflict to resolve:** Michael said the Doc should stay in sync with the tracker; it has not been updated since Week 4. It also flagged that ESPN's Week 4 table listed Texas special teams 22nd while an earlier Texas chart showed 15th. Decide with Michael whether to retire the Doc or regenerate it from the repo data each week.

### 6.7 Related analysis held outside the tracker: 2026 offensive-line transfer portal comparison (Aug. 5, 2026)

Not part of the tracker data and not stored in the page. Summary of what was produced in a separate chat:

- Compared 9 incoming OL transfers. Texas A&M (4): Wilkin Formby, Tyree Adams, Coen Echols, Trovon Baugh. Texas (5): Melvin Siani, Laurence Seymore III, Dylan Sikorski, Jonte Newman, Paris Patterson Jr. Sources: 247Sports transfer portal pages https://247sports.com/college/texas-am/season/2026-football/transferportal/?institutionkey=24029 and https://247sports.com/college/texas/season/2026-football/transferportal/?institutionkey=24028 , plus PFF and team data.
- Factors Michael required: individual statistics, depth, competition level; explicitly not team records or message boards.
- Conclusion: A&M held the stronger group (all four logged meaningful 2025 SEC snaps; two Texas additions had under 25 career snaps and a third produced against non-power competition).
- Frame analysis vs. recent high NFL draft picks (Mims, Latham, Alt, Campbell, Membou): Formby (6-7/324) and Adams (6-7/310) closest to first-round tackle frames; Siani (6-6/302) and Seymore lowest. Arm length and hand size are not available for these players (one exception: a documented wingspan for Adams).
- Offered but not built: a weighted OL scoring model (production + competition adjustment + frame percentiles). The per-player tables are in that chat, not in this export.

---

## 7. Analyses, comparisons and views Michael asked for

- Sarkisian vs. Elko SP+ and FEI ranks for every season, all four units, aligned by calendar year and by tenure year.
- Same-seasons vs. first-three-seasons summary comparisons (records, average ranks, head-to-head).
- Manning vs. Reed head-to-head for 2025 and for 2026 to date.
- All 16 SEC starting QBs: season totals, ESPN QBR with SEC rank, efficiency scatter, sortable leaderboard, game logs (started as 10 QBs, expanded to 16).
- Texas A&M vs. every SEC opponent, Winsipedia-style (record bar, streak, margin chart, decade records, highlights, full game list, at-a-glance table), added as a tab so everything lives at one link.
- A raw-production reality check on Texas's offense next to its SP+/FEI ranks.
- Weekly refreshes of all three tabs (Michael has to prompt each week; the claude.ai version could not run itself).
- Earlier: an Excel QB workbook with formulas and a Sources tab, and the OL transfer portal analysis (Section 6.7).

---

## 8. Accuracy standards Michael set (non-negotiable)

These came from direct corrections in October 2026 and apply to every dashboard Claude builds for him:

1. **Never present data in a way that makes results look better than they were.** Accurate numbers presented misleadingly count as a failure. Example that triggered this: Manning's NCAA passer rating (145.2) looked solid while his QBR and raw production were poor; Texas's SP+ offense rank (19th) looked fine while raw production ranked about 104th in yards per play.
2. **Lead with the most direct measure;** never show a rating or index without the raw number next to it.
3. **Flag every conflict explicitly** (between sources, or between a metric and reality) on the page, not in a buried footnote.
4. **Label source and freshness;** if something is stale or partial, say so where it appears.
5. **Missing data stays visibly missing** (n/a, dash, asterisk) with a reason. Never fill a gap from memory or a weaker source silently. When Michael says data is missing, go to the primary source (ESPN box scores for QBR) for every game, not just the ones mentioned.
6. **Do not mix weeks in one column** (e.g., do not drop one post-Week 7 QBR into a post-Week 6 column).
7. **If a chosen metric turns out to be misleading, change it,** do not just caveat it.
8. **Writing style:** never use em dashes in responses; use commas, periods, parentheses, or colons.

---

## 9. Known gaps and open data issues (as of Oct. 21, 2026)

- Season QBR and SEC QBR rank are ESPN's post-Week 6 values. The post-Week 7 table had not been published in any accessible source on Oct. 19 (On3 reported Chambliss at 83.5 after Week 7). Update all 16 together when available.
- Game QBR not published for: Sam Leavitt vs. Mississippi State (10/17), KJ Jackson vs. Tennessee (10/10) and at Vanderbilt (10/17), Jared Curtis vs. Arkansas (10/17).
- Earlier-week game QBRs that are null in the logs were never backfilled from box scores (most Week 1 to 5 rows have values; check every null).
- Sacks unknown (null) for several games; season `sk` sums known values only. Manning's season total was confirmed through official box scores for Weeks 6 and 7 (2 vs. Oklahoma, 5 vs. Florida).
- Jared Curtis Week 7 rushing yards missing (rushNote set).
- `ra` (rush attempts) is not reliably maintained; it is not displayed. Either maintain it from box scores or drop it.
- Sam Leavitt's season passing yards are 1 yard below CBS's figure (1,784 vs. 1,785 implied); a Week 1 to 5 game line is off by 1 yard. Reconcile from ESPN game logs.
- 2026 FEI is through Week 6; refresh when bcftoys posts Week 7.
- Companion Claude Doc is frozen at Week 4 (Section 6.6).
- Backup QBs who started (Tennessee's George MacIntyre in Weeks 6 and 7, Florida's Tramell Jones Jr. and Mississippi State's Parker Puckett in Week 7) are logged only as notes on the injured starter's row. Decide with Michael whether to track the actual starter.
- Faizon Brandon is still listed as Tennessee's QB (47.0 QBR, qualifying through Week 6) despite missing two games.

---

## 10. Weekly update procedure (how it has been done)

1. Get every SEC result for the week (ESPN schedule, AP recaps). Note byes and injured starters.
2. For each of the 16 QBs, pull the line from the **ESPN box score** (search the exact title pattern "{Winner} {score}-{score} {Loser} (Mon D, YYYY) Box Score - ESPN" when ESPN pages will not render): C/A, yards, TD, INT, game QBR, sacks, rushing. Cross-check against the team's official box score and CBS/FOX season totals.
3. Append a game row (or a bye / did-not-play note row), add to season totals, update team `rec`.
4. Update season QBR and SEC rank from ESPN's SEC QBR table (or the weekly Vols Wire / Yahoo article) for all 16 at once.
5. Update 2026 SP+ (ESPN; CFB Update mirror if needed, but verify it is not stale) and FEI (bcftoys.com/2026-fei) for Texas and A&M: overall, offense, defense, special teams; update records and `UPDATED`.
6. SEC Series: if A&M played an SEC opponent, append `[2026, A&M pts, opp pts, site, note]` to that opponent; update `SEC_UPDATED`, `SEC_NEXT`, and `next` strings.
7. Re-read the raw-production context (Texas offense note) and any spotlight caveats; update them so the page never flatters.
8. Update footer notes: what is through which week, every correction made, and every gap.
9. Validate: totals equal the sum of game rows; records reconcile; render all three tabs without errors; check light and dark mode and mobile.
10. Tell Michael what changed and list anything still missing.

2026 A&M schedule remainder: Oct. 24 at Alabama, Nov. 7 at South Carolina, Nov. 14 vs. Tennessee, Nov. 21 at Oklahoma, Nov. 27 vs. Texas (Kyle Field).

---

## 11. Notes for the Claude Code rebuild

- Keep a single self-contained static page as the output (it was built to publish anywhere; no remote images, inline SVG charts, theme-aware light/dark colors via CSS variables, responsive down to phone width, en dashes for scores and records, curly apostrophes in display text).
- Visual identity: Texas burnt orange (CSS `--orange`) and A&M maroon (`--maroon`); opponent colors in `SEC_TEAMS`; display font for big numbers.
- Suggested repo layout: `data/*.json` (Appendix A), `src/` (page + render code split from `tracker-index.html`), `scripts/validate.(js|py)` (recompute totals from game logs, reconcile SEC records against official totals in Section 6.5, flag nulls), `scripts/build` (inject data into the page), `docs/CHANGELOG.md` (weekly corrections log), and this file as `docs/tracker-export.md`.
- Add automated checks that would have caught past errors: season totals equal sum of game rows; every non-bye game has QBR or an explicit not-published reason; no column mixes data from different weeks; SEC records match Winsipedia totals.
- Pending idea from the OL analysis: weighted OL scoring model. Not started.
- Sharing: the claude.ai artifact required viewers to have a Claude account; Michael was told GitHub Pages, Netlify or WordPress would allow open access.

---

## Appendix A. Machine-readable data

### A.1 `data/meta.json`

```json
{
  "updated": {
    "week": 7,
    "date": "2026-10-17",
    "feiWeek": 6,
    "secUpdated": "2026-10-19"
  },
  "h2h": {
    "tx": 2,
    "am": 0,
    "games": [
      {
        "season": 2024,
        "winner": "Texas",
        "score": "17-7"
      },
      {
        "season": 2025,
        "winner": "Texas",
        "score": "27-17",
        "site": "Austin, TX"
      }
    ]
  },
  "elkoPrior": {
    "2021": "Texas A&M defensive coordinator",
    "2022": "Head coach at Duke",
    "2023": "Head coach at Duke"
  },
  "secNext": "alabama",
  "rankOrder": [
    "overall",
    "offense",
    "defense",
    "special_teams"
  ]
}
```

### A.2 `data/coaches.json`

```json
{
  "tx": {
    "name": "Steve Sarkisian",
    "school": "Texas",
    "start": 2021,
    "seasons": {
      "2021": {
        "rec": "5–7",
        "sp": [
          48,
          29,
          86,
          6
        ],
        "fei": [
          34,
          35,
          47,
          31
        ]
      },
      "2022": {
        "rec": "8–5",
        "sp": [
          7,
          28,
          15,
          44
        ],
        "fei": [
          6,
          17,
          8,
          8
        ]
      },
      "2023": {
        "rec": "12–2",
        "sp": [
          6,
          6,
          13,
          17
        ],
        "fei": [
          7,
          19,
          6,
          3
        ]
      },
      "2024": {
        "rec": "13–3",
        "sp": [
          7,
          13,
          2,
          117
        ],
        "fei": [
          5,
          18,
          3,
          35
        ]
      },
      "2025": {
        "rec": "10–3",
        "sp": [
          17,
          30,
          18,
          31
        ],
        "fei": [
          19,
          32,
          10,
          15
        ]
      },
      "2026": {
        "rec": "4–2",
        "sp": [
          8,
          19,
          5,
          49
        ],
        "fei": [
          8,
          27,
          3,
          66
        ]
      }
    }
  },
  "am": {
    "name": "Mike Elko",
    "school": "Texas A&M",
    "start": 2024,
    "seasons": {
      "2024": {
        "rec": "8–5",
        "sp": [
          12,
          20,
          19,
          19
        ],
        "fei": [
          21,
          29,
          23,
          12
        ]
      },
      "2025": {
        "rec": "11–2",
        "sp": [
          10,
          11,
          14,
          97
        ],
        "fei": [
          11,
          15,
          9,
          33
        ]
      },
      "2026": {
        "rec": "4–3",
        "sp": [
          33,
          46,
          31,
          17
        ],
        "fei": [
          28,
          54,
          26,
          1
        ]
      }
    }
  }
}
```

### A.3 `data/qbs-2025.json`

```json
{
  "tx": {
    "name": "Arch Manning",
    "rec": "10–3",
    "c": 248,
    "a": 404,
    "y": 3163,
    "td": 26,
    "int": 7,
    "sk": 23,
    "ra": 92,
    "ry": 399,
    "rtd": 10,
    "qbr": 70.6,
    "pff": "87.3 overall",
    "pffNote": "top 15 nationally"
  },
  "am": {
    "name": "Marcel Reed",
    "rec": "11–2",
    "c": 234,
    "a": 377,
    "y": 3169,
    "td": 25,
    "int": 12,
    "sk": 17,
    "ra": 104,
    "ry": 493,
    "rtd": 6,
    "qbr": 77.9,
    "pff": "64.0 passing",
    "pffNote": "105th nationally"
  }
}
```

### A.4 `data/qbs-2026.json`

```json
[
 {
  "name": "Arch Manning",
  "team": "Texas",
  "ab": "TEX",
  "side": "tx",
  "rec": "4-2",
  "c": 106,
  "a": 166,
  "y": 1295,
  "td": 11,
  "int": 5,
  "sk": 17,
  "qbr": 65.5,
  "qbrRk": 8,
  "ra": 32,
  "ry": 102,
  "rtd": 0,
  "games": [
   {
    "d": "9/5",
    "opp": "vs Texas State",
    "res": "W 59-7",
    "c": 20,
    "a": 27,
    "y": 305,
    "td": 4,
    "int": 1,
    "sk": 1,
    "qbr": 92.6
   },
   {
    "d": "9/12",
    "opp": "vs No. 1 Ohio State",
    "res": "W 24-23",
    "c": 23,
    "a": 37,
    "y": 195,
    "td": 1,
    "int": 1,
    "sk": 2,
    "qbr": 80.6
   },
   {
    "d": "9/19",
    "opp": "vs UTSA",
    "res": "W 30-6",
    "c": 16,
    "a": 32,
    "y": 161,
    "td": 2,
    "int": 0,
    "sk": 1,
    "qbr": 27.4
   },
   {
    "d": "9/26",
    "opp": "at No. 14 Tennessee",
    "res": "W 20-17",
    "c": 14,
    "a": 19,
    "y": 201,
    "td": 0,
    "int": 1,
    "sk": 6,
    "qbr": 53.0
   },
   {
    "d": "10/3",
    "bye": true
   },
   {
    "d": "10/10",
    "opp": "vs Oklahoma (Dallas)",
    "res": "L 24-27",
    "c": 19,
    "a": 31,
    "y": 260,
    "td": 3,
    "int": 2,
    "sk": 2,
    "qbr": 71.0
   },
   {
    "d": "10/17",
    "opp": "vs Florida",
    "res": "L 24-27",
    "c": 14,
    "a": 20,
    "y": 173,
    "td": 1,
    "int": 0,
    "sk": 5,
    "qbr": 20.6
   }
  ]
 },
 {
  "name": "Marcel Reed",
  "team": "Texas A&M",
  "ab": "TAMU",
  "side": "am",
  "rec": "4-3",
  "c": 112,
  "a": 201,
  "y": 1227,
  "td": 7,
  "int": 6,
  "sk": 12,
  "qbr": 45.5,
  "qbrRk": 16,
  "ra": 43,
  "ry": 175,
  "rtd": 2,
  "games": [
   {
    "d": "9/5",
    "opp": "vs Missouri State",
    "res": "W 50-0",
    "c": 21,
    "a": 30,
    "y": 233,
    "td": 2,
    "int": 0,
    "sk": 2,
    "qbr": 58.2
   },
   {
    "d": "9/12",
    "opp": "vs Arizona State",
    "res": "W 48-20",
    "c": 15,
    "a": 25,
    "y": 163,
    "td": 3,
    "int": 1,
    "sk": 2,
    "qbr": 46.0
   },
   {
    "d": "9/19",
    "opp": "vs Kentucky",
    "res": "L 21-31",
    "c": 26,
    "a": 49,
    "y": 236,
    "td": 1,
    "int": 2,
    "sk": 0,
    "qbr": 62.3
   },
   {
    "d": "9/26",
    "opp": "at No. 10 LSU",
    "res": "L 6-35",
    "c": 10,
    "a": 26,
    "y": 106,
    "td": 0,
    "int": 1,
    "sk": 5,
    "qbr": 31.3
   },
   {
    "d": "10/3",
    "opp": "vs Arkansas",
    "res": "W 34-7",
    "c": 13,
    "a": 19,
    "y": 170,
    "td": 0,
    "int": 0,
    "sk": 0,
    "qbr": 82.2
   },
   {
    "d": "10/10",
    "opp": "at No. 14 Missouri",
    "res": "L 6-27",
    "c": 19,
    "a": 39,
    "y": 189,
    "td": 0,
    "int": 2,
    "sk": 3,
    "qbr": 18.2
   },
   {
    "d": "10/17",
    "opp": "vs The Citadel (FCS)",
    "res": "W 41-0",
    "c": 8,
    "a": 13,
    "y": 130,
    "td": 1,
    "int": 0,
    "sk": null,
    "qbr": 75.5
   }
  ]
 },
 {
  "name": "John Mateer",
  "team": "Oklahoma",
  "ab": "OU",
  "side": "",
  "rec": "3-3",
  "c": 114,
  "a": 175,
  "y": 1308,
  "td": 9,
  "int": 7,
  "sk": 11,
  "qbr": 55.8,
  "qbrRk": 13,
  "ra": 41,
  "ry": 78,
  "rtd": 0,
  "games": [
   {
    "d": "9/4",
    "opp": "vs UTEP",
    "res": "W 51-0",
    "c": 11,
    "a": 17,
    "y": 225,
    "td": 3,
    "int": 0,
    "sk": 0,
    "qbr": 84.4
   },
   {
    "d": "9/12",
    "opp": "at Michigan",
    "res": "L 10-17",
    "c": 17,
    "a": 33,
    "y": 189,
    "td": 1,
    "int": 1,
    "sk": 3,
    "qbr": 52.0
   },
   {
    "d": "9/19",
    "opp": "vs New Mexico",
    "res": "W 14-6",
    "c": 19,
    "a": 25,
    "y": 238,
    "td": 2,
    "int": 2,
    "sk": 1,
    "qbr": 60.2
   },
   {
    "d": "9/26",
    "opp": "at No. 2 Georgia",
    "res": "L 13-41",
    "c": 25,
    "a": 34,
    "y": 231,
    "td": 2,
    "int": 1,
    "sk": 3,
    "qbr": 44.4
   },
   {
    "d": "10/3",
    "bye": true
   },
   {
    "d": "10/10",
    "opp": "vs No. 1 Texas (Dallas)",
    "res": "W 27-24",
    "c": 18,
    "a": 26,
    "y": 155,
    "td": 0,
    "int": 0,
    "sk": 3,
    "qbr": 48.6
   },
   {
    "d": "10/17",
    "opp": "vs No. 15 Kentucky",
    "res": "L 13-21",
    "c": 24,
    "a": 40,
    "y": 270,
    "td": 1,
    "int": 3,
    "sk": null,
    "qbr": 50.2
   }
  ]
 },
 {
  "name": "Keelon Russell",
  "team": "Alabama",
  "ab": "BAMA",
  "side": "",
  "rec": "5-2",
  "c": 129,
  "a": 191,
  "y": 1746,
  "td": 12,
  "int": 4,
  "sk": 15,
  "qbr": 90.0,
  "qbrRk": 2,
  "ra": 37,
  "ry": 231,
  "rtd": 5,
  "games": [
   {
    "d": "9/5",
    "opp": "vs East Carolina",
    "res": "W 48-10",
    "c": 18,
    "a": 30,
    "y": 256,
    "td": 0,
    "int": 0,
    "sk": 1,
    "qbr": 82.2
   },
   {
    "d": "9/12",
    "opp": "at Kentucky",
    "res": "W 45-17",
    "c": 16,
    "a": 23,
    "y": 188,
    "td": 1,
    "int": 2,
    "sk": 4,
    "qbr": 44.4
   },
   {
    "d": "9/19",
    "opp": "vs Florida State",
    "res": "W 50-36",
    "c": 23,
    "a": 30,
    "y": 320,
    "td": 3,
    "int": 0,
    "sk": 1,
    "qbr": 97.7
   },
   {
    "d": "9/26",
    "opp": "vs South Carolina",
    "res": "W 49-18",
    "c": 21,
    "a": 27,
    "y": 365,
    "td": 4,
    "int": 0,
    "sk": 2,
    "qbr": 97.6
   },
   {
    "d": "10/3",
    "opp": "at No. 16 Mississippi State",
    "res": "W 56-23",
    "c": 16,
    "a": 22,
    "y": 285,
    "td": 3,
    "int": 0,
    "sk": 3,
    "qbr": null
   },
   {
    "d": "10/10",
    "opp": "vs No. 2 Georgia",
    "res": "L 17-20",
    "c": 16,
    "a": 30,
    "y": 185,
    "td": 1,
    "int": 0,
    "sk": 4,
    "qbr": 77.8
   },
   {
    "d": "10/17",
    "opp": "at No. 14 Tennessee",
    "res": "L 10-26",
    "c": 19,
    "a": 29,
    "y": 147,
    "td": 0,
    "int": 2,
    "sk": null,
    "qbr": 66.3
   }
  ]
 },
 {
  "name": "Aaron Philo",
  "team": "Florida",
  "ab": "UF",
  "side": "",
  "rec": "5-2",
  "c": 105,
  "a": 149,
  "y": 1341,
  "td": 9,
  "int": 3,
  "sk": 5,
  "qbr": 74.1,
  "qbrRk": 6,
  "ra": 24,
  "ry": 104,
  "rtd": 3,
  "games": [
   {
    "d": "9/5",
    "opp": "vs FAU",
    "res": "W 66-21",
    "c": 16,
    "a": 21,
    "y": 275,
    "td": 3,
    "int": 1,
    "sk": 0,
    "qbr": 94.0
   },
   {
    "d": "9/12",
    "opp": "vs Campbell (FCS)",
    "res": "W 52-3",
    "c": 16,
    "a": 21,
    "y": 242,
    "td": 2,
    "int": 0,
    "sk": 0,
    "qbr": 76.9
   },
   {
    "d": "9/19",
    "opp": "at Auburn",
    "res": "W 44-39",
    "c": 15,
    "a": 22,
    "y": 204,
    "td": 1,
    "int": 1,
    "sk": 2,
    "qbr": 88.7
   },
   {
    "d": "9/26",
    "opp": "vs No. 4 Ole Miss",
    "res": "W 52-28",
    "c": 16,
    "a": 23,
    "y": 196,
    "td": 1,
    "int": 0,
    "sk": 0,
    "qbr": 71.5
   },
   {
    "d": "10/3",
    "opp": "at No. 25 Missouri",
    "res": "L 17-45",
    "c": 21,
    "a": 34,
    "y": 197,
    "td": 1,
    "int": 0,
    "sk": 3,
    "qbr": null
   },
   {
    "d": "10/10",
    "opp": "vs South Carolina",
    "res": "L 19-38",
    "c": 21,
    "a": 28,
    "y": 227,
    "td": 1,
    "int": 1,
    "sk": null,
    "qbr": 31.9
   },
   {
    "d": "10/17",
    "bye": true,
    "note": "Did not play at No. 7 Texas (shoulder); Tramell Jones Jr. started: 22 of 35, 155 yds, 2 TD, W 27-24"
   }
  ]
 },
 {
  "name": "Kamario Taylor",
  "team": "Mississippi State",
  "ab": "MSST",
  "side": "",
  "rec": "4-2",
  "c": 102,
  "a": 154,
  "y": 1450,
  "td": 17,
  "int": 4,
  "sk": 13,
  "qbr": 83.3,
  "qbrRk": 3,
  "ra": 43,
  "ry": 159,
  "rtd": 1,
  "games": [
   {
    "d": "9/5",
    "opp": "vs ULM",
    "res": "W 62-13",
    "c": 22,
    "a": 34,
    "y": 354,
    "td": 4,
    "int": 0,
    "sk": 0,
    "qbr": 89.8
   },
   {
    "d": "9/12",
    "opp": "at Minnesota",
    "res": "W 38-13",
    "c": 16,
    "a": 22,
    "y": 227,
    "td": 3,
    "int": 0,
    "sk": 1,
    "qbr": 90.0
   },
   {
    "d": "9/19",
    "opp": "at South Carolina",
    "res": "W 41-34",
    "c": 18,
    "a": 26,
    "y": 251,
    "td": 4,
    "int": 1,
    "sk": 3,
    "qbr": 83.3
   },
   {
    "d": "9/26",
    "opp": "vs No. 19 Missouri",
    "res": "W 31-24",
    "c": 27,
    "a": 38,
    "y": 360,
    "td": 3,
    "int": 1,
    "sk": 4,
    "qbr": 85.1
   },
   {
    "d": "10/3",
    "opp": "vs No. 7 Alabama",
    "res": "L 23-56",
    "c": 19,
    "a": 34,
    "y": 258,
    "td": 3,
    "int": 2,
    "sk": 5,
    "qbr": null
   },
   {
    "d": "10/10",
    "bye": true,
    "note": "Bye week"
   },
   {
    "d": "10/17",
    "bye": true,
    "note": "Did not play at No. 22 LSU (injury); Parker Puckett started, L 0-31"
   }
  ]
 },
 {
  "name": "Sam Leavitt",
  "team": "LSU",
  "ab": "LSU",
  "side": "",
  "rec": "5-2",
  "c": 148,
  "a": 211,
  "y": 1784,
  "td": 14,
  "int": 8,
  "sk": 7,
  "qbr": 81.1,
  "qbrRk": 5,
  "ra": 45,
  "ry": 270,
  "rtd": 7,
  "games": [
   {
    "d": "9/5",
    "opp": "vs Clemson",
    "res": "W 51-10",
    "c": 16,
    "a": 28,
    "y": 230,
    "td": 1,
    "int": 1,
    "sk": 0,
    "qbr": 91.1
   },
   {
    "d": "9/12",
    "opp": "vs Louisiana Tech",
    "res": "W 45-14",
    "c": 25,
    "a": 38,
    "y": 344,
    "td": 1,
    "int": 3,
    "sk": 5,
    "qbr": 42.1
   },
   {
    "d": "9/19",
    "opp": "at No. 8 Ole Miss",
    "res": "L 24-32",
    "c": 16,
    "a": 27,
    "y": 158,
    "td": 1,
    "int": 1,
    "sk": 2,
    "qbr": 86.1
   },
   {
    "d": "9/26",
    "opp": "vs No. 23 Texas A&M",
    "res": "W 35-6",
    "c": 17,
    "a": 23,
    "y": 312,
    "td": 2,
    "int": 1,
    "sk": 0,
    "qbr": 91.6
   },
   {
    "d": "10/3",
    "opp": "vs McNeese State (FCS)",
    "res": "W 63-14",
    "c": 18,
    "a": 23,
    "y": 176,
    "td": 3,
    "int": 1,
    "sk": 0,
    "qbr": null
   },
   {
    "d": "10/10",
    "opp": "at Kentucky",
    "res": "L 34-35 OT",
    "c": 25,
    "a": 34,
    "y": 275,
    "td": 2,
    "int": 1,
    "sk": null,
    "qbr": 82.5
   },
   {
    "d": "10/17",
    "opp": "vs No. 16 Mississippi State",
    "res": "W 31-0",
    "c": 31,
    "a": 38,
    "y": 289,
    "td": 4,
    "int": 0,
    "sk": null,
    "qbr": null
   }
  ]
 },
 {
  "name": "Trinidad Chambliss",
  "team": "Ole Miss",
  "ab": "MISS",
  "side": "",
  "rec": "5-1",
  "c": 142,
  "a": 210,
  "y": 1676,
  "td": 14,
  "int": 4,
  "sk": 5,
  "qbr": 83.0,
  "qbrRk": 4,
  "ra": 28,
  "ry": 127,
  "rtd": 6,
  "games": [
   {
    "d": "9/6",
    "opp": "vs No. 24 Louisville",
    "res": "W 41-38",
    "c": 21,
    "a": 36,
    "y": 336,
    "td": 3,
    "int": 1,
    "sk": 0,
    "qbr": 72.4
   },
   {
    "d": "9/12",
    "opp": "vs Charlotte",
    "res": "W 41-9",
    "c": 23,
    "a": 26,
    "y": 225,
    "td": 2,
    "int": 0,
    "sk": 1,
    "qbr": 73.2
   },
   {
    "d": "9/19",
    "opp": "vs No. 7 LSU",
    "res": "W 32-24",
    "c": 33,
    "a": 48,
    "y": 363,
    "td": 2,
    "int": 1,
    "sk": 2,
    "qbr": 92.6
   },
   {
    "d": "9/26",
    "opp": "at No. 21 Florida",
    "res": "L 28-52",
    "c": 25,
    "a": 34,
    "y": 298,
    "td": 1,
    "int": 1,
    "sk": 1,
    "qbr": 91.1
   },
   {
    "d": "10/3",
    "bye": true
   },
   {
    "d": "10/10",
    "opp": "at Vanderbilt",
    "res": "W 52-17",
    "c": 20,
    "a": 30,
    "y": 213,
    "td": 3,
    "int": 1,
    "sk": 1,
    "qbr": 91.1
   },
   {
    "d": "10/17",
    "opp": "vs No. 13 Missouri",
    "res": "W 31-3",
    "c": 20,
    "a": 36,
    "y": 241,
    "td": 3,
    "int": 0,
    "sk": null,
    "qbr": 80.4
   }
  ]
 },
 {
  "name": "Gunner Stockton",
  "team": "Georgia",
  "ab": "UGA",
  "side": "",
  "rec": "6-1",
  "c": 117,
  "a": 158,
  "y": 1570,
  "td": 16,
  "int": 4,
  "sk": 6,
  "qbr": 90.4,
  "qbrRk": 1,
  "ra": 20,
  "ry": 160,
  "rtd": 1,
  "games": [
   {
    "d": "9/5",
    "opp": "vs Tennessee State (FCS)",
    "res": "W 63-3",
    "c": 15,
    "a": 16,
    "y": 202,
    "td": 3,
    "int": 0,
    "sk": 0,
    "qbr": 87.0
   },
   {
    "d": "9/12",
    "opp": "vs WKU",
    "res": "W 70-20",
    "c": 13,
    "a": 16,
    "y": 234,
    "td": 5,
    "int": 0,
    "sk": 0,
    "qbr": 95.9
   },
   {
    "d": "9/19",
    "opp": "at Arkansas",
    "res": "W 45-17",
    "c": 10,
    "a": 13,
    "y": 147,
    "td": 1,
    "int": 0,
    "sk": 3,
    "qbr": 97.5
   },
   {
    "d": "9/26",
    "opp": "vs Oklahoma",
    "res": "W 41-13",
    "c": 14,
    "a": 25,
    "y": 171,
    "td": 1,
    "int": 1,
    "sk": 1,
    "qbr": 67.7
   },
   {
    "d": "10/3",
    "opp": "vs Vanderbilt",
    "res": "W 38-14",
    "c": 25,
    "a": 32,
    "y": 295,
    "td": 3,
    "int": 0,
    "sk": 1,
    "qbr": null
   },
   {
    "d": "10/10",
    "opp": "at No. 6 Alabama",
    "res": "W 20-17",
    "c": 18,
    "a": 22,
    "y": 249,
    "td": 1,
    "int": 1,
    "sk": 1,
    "qbr": 94.4
   },
   {
    "d": "10/17",
    "opp": "vs Auburn",
    "res": "L 24-27",
    "c": 22,
    "a": 34,
    "y": 272,
    "td": 2,
    "int": 2,
    "sk": null,
    "qbr": 64.6
   }
  ]
 },
 {
  "name": "KJ Jackson",
  "team": "Arkansas",
  "ab": "ARK",
  "side": "",
  "rec": "3-4",
  "c": 131,
  "a": 215,
  "y": 1444,
  "td": 7,
  "int": 4,
  "sk": 12,
  "qbr": 46.9,
  "qbrRk": 15,
  "ra": 39,
  "ry": 140,
  "rtd": 3,
  "games": [
   {
    "d": "9/5",
    "opp": "vs North Alabama (FCS)",
    "res": "W 31-14",
    "c": 24,
    "a": 35,
    "y": 267,
    "td": 3,
    "int": 1,
    "sk": 3,
    "qbr": 32.0
   },
   {
    "d": "9/12",
    "opp": "at No. 20 Utah",
    "res": "L 10-43",
    "c": 14,
    "a": 34,
    "y": 139,
    "td": 0,
    "int": 1,
    "sk": 1,
    "qbr": 41.5
   },
   {
    "d": "9/19",
    "opp": "vs No. 2 Georgia",
    "res": "L 17-45",
    "c": 20,
    "a": 27,
    "y": 212,
    "td": 1,
    "int": 0,
    "sk": 2,
    "qbr": 69.1
   },
   {
    "d": "9/26",
    "opp": "vs Tulsa",
    "res": "W 34-6",
    "c": 24,
    "a": 36,
    "y": 309,
    "td": 1,
    "int": 1,
    "sk": 3,
    "qbr": 71.2
   },
   {
    "d": "10/3",
    "opp": "at Texas A&M",
    "res": "L 7-34",
    "c": 13,
    "a": 25,
    "y": 111,
    "td": 1,
    "int": 1,
    "sk": 1,
    "qbr": 24.2
   },
   {
    "d": "10/10",
    "opp": "vs No. 15 Tennessee",
    "res": "L 17-24",
    "c": 15,
    "a": 27,
    "y": 155,
    "td": 1,
    "int": 0,
    "sk": 2,
    "qbr": null
   },
   {
    "d": "10/17",
    "opp": "at Vanderbilt",
    "res": "W 31-21",
    "c": 21,
    "a": 31,
    "y": 251,
    "td": 0,
    "int": 0,
    "sk": 0,
    "qbr": null
   }
  ]
 },
 {
  "name": "Austin Simmons",
  "team": "Missouri",
  "ab": "MIZ",
  "side": "",
  "rec": "5-2",
  "c": 132,
  "a": 209,
  "y": 1773,
  "td": 14,
  "int": 2,
  "sk": 8,
  "qbr": 71.5,
  "qbrRk": 7,
  "ra": 13,
  "ry": 18,
  "rtd": 0,
  "games": [
   {
    "d": "9/3",
    "opp": "vs Arkansas-Pine Bluff (FCS)",
    "res": "W 54-14",
    "c": 17,
    "a": 19,
    "y": 252,
    "td": 4,
    "int": 0,
    "sk": 1,
    "qbr": 93.6
   },
   {
    "d": "9/11",
    "opp": "at Kansas",
    "res": "W 38-21",
    "c": 19,
    "a": 29,
    "y": 292,
    "td": 3,
    "int": 0,
    "sk": 0,
    "qbr": 86.0
   },
   {
    "d": "9/19",
    "opp": "vs Troy",
    "res": "W 27-17",
    "c": 11,
    "a": 20,
    "y": 108,
    "td": 1,
    "int": 0,
    "sk": 1,
    "qbr": 19.1
   },
   {
    "d": "9/26",
    "opp": "at No. 24 Mississippi State",
    "res": "L 24-31",
    "c": 23,
    "a": 39,
    "y": 295,
    "td": 3,
    "int": 0,
    "sk": 2,
    "qbr": 73.0
   },
   {
    "d": "10/3",
    "opp": "vs No. 8 Florida",
    "res": "W 45-17",
    "c": 23,
    "a": 30,
    "y": 340,
    "td": 2,
    "int": 0,
    "sk": 2,
    "qbr": 91.5
   },
   {
    "d": "10/10",
    "opp": "vs Texas A&M",
    "res": "W 27-6",
    "c": 14,
    "a": 28,
    "y": 147,
    "td": 1,
    "int": 1,
    "sk": null,
    "qbr": 45.7
   },
   {
    "d": "10/17",
    "opp": "at No. 9 Ole Miss",
    "res": "L 3-31",
    "c": 25,
    "a": 44,
    "y": 339,
    "td": 0,
    "int": 1,
    "sk": 2,
    "qbr": 63.2
   }
  ]
 },
 {
  "name": "Byrum Brown",
  "team": "Auburn",
  "ab": "AUB",
  "side": "",
  "rec": "4-2",
  "c": 119,
  "a": 185,
  "y": 1205,
  "td": 8,
  "int": 7,
  "sk": 18,
  "qbr": 64.8,
  "qbrRk": 9,
  "ra": 81,
  "ry": 459,
  "rtd": 5,
  "games": [
   {
    "d": "9/5",
    "opp": "vs Baylor (Atlanta)",
    "res": "W 17-16",
    "c": 26,
    "a": 35,
    "y": 259,
    "td": 0,
    "int": 3,
    "sk": 5,
    "qbr": 55.1
   },
   {
    "d": "9/12",
    "opp": "vs Southern Miss",
    "res": "W 43-8",
    "c": 18,
    "a": 29,
    "y": 226,
    "td": 2,
    "int": 0,
    "sk": 1,
    "qbr": 80.5
   },
   {
    "d": "9/19",
    "opp": "vs Florida",
    "res": "L 39-44",
    "c": 20,
    "a": 35,
    "y": 271,
    "td": 3,
    "int": 1,
    "sk": 7,
    "qbr": 73.9
   },
   {
    "d": "9/26",
    "opp": "vs Vanderbilt",
    "res": "W 21-15",
    "c": 18,
    "a": 27,
    "y": 140,
    "td": 1,
    "int": 1,
    "sk": 1,
    "qbr": 71.4
   },
   {
    "d": "10/3",
    "opp": "at No. 17 Tennessee",
    "res": "L 14-24",
    "c": 22,
    "a": 33,
    "y": 159,
    "td": 1,
    "int": 2,
    "sk": 4,
    "qbr": null
   },
   {
    "d": "10/10",
    "bye": true,
    "note": "Bye week"
   },
   {
    "d": "10/17",
    "opp": "at No. 1 Georgia",
    "res": "W 27-24",
    "c": 15,
    "a": 26,
    "y": 150,
    "td": 1,
    "int": 0,
    "sk": null,
    "qbr": 86.6
   }
  ]
 },
 {
  "name": "Jared Curtis",
  "team": "Vanderbilt",
  "ab": "VAN",
  "side": "",
  "rec": "3-4",
  "c": 101,
  "a": 161,
  "y": 1242,
  "td": 6,
  "int": 7,
  "sk": 11,
  "qbr": 57.9,
  "qbrRk": 11,
  "ra": 41,
  "ry": 131,
  "rtd": 2,
  "games": [
   {
    "d": "9/5",
    "opp": "vs Austin Peay (FCS), relief",
    "res": "W",
    "c": 7,
    "a": 9,
    "y": 60,
    "td": 0,
    "int": 0,
    "sk": null,
    "qbr": null
   },
   {
    "d": "9/12",
    "opp": "vs Delaware, relief",
    "res": "W",
    "c": 14,
    "a": 22,
    "y": 210,
    "td": 1,
    "int": 0,
    "sk": null,
    "qbr": null
   },
   {
    "d": "9/19",
    "opp": "vs NC State",
    "res": "W 35-31",
    "c": 20,
    "a": 34,
    "y": 277,
    "td": 3,
    "int": 2,
    "sk": 0,
    "qbr": 50.6
   },
   {
    "d": "9/26",
    "opp": "at Auburn",
    "res": "L 15-21",
    "c": 22,
    "a": 33,
    "y": 199,
    "td": 0,
    "int": 1,
    "sk": 4,
    "qbr": 77.1
   },
   {
    "d": "10/3",
    "bye": true,
    "note": "Did not play at No. 2 Georgia (bruised knee); Blaze Berlowitz started, L 14-38"
   },
   {
    "d": "10/10",
    "opp": "vs No. 9 Ole Miss",
    "res": "L 17-52",
    "c": 19,
    "a": 32,
    "y": 222,
    "td": 1,
    "int": 2,
    "sk": 3,
    "qbr": 47.5
   },
   {
    "d": "10/17",
    "opp": "vs Arkansas",
    "res": "L 21-31",
    "c": 19,
    "a": 31,
    "y": 274,
    "td": 1,
    "int": 2,
    "sk": 1,
    "qbr": null
   }
  ],
  "rushNote": "Week 7 rushing yards not yet available"
 },
 {
  "name": "LaNorris Sellers",
  "team": "South Carolina",
  "ab": "SC",
  "side": "",
  "rec": "3-3",
  "c": 115,
  "a": 187,
  "y": 1338,
  "td": 12,
  "int": 4,
  "sk": 7,
  "qbr": 62.5,
  "qbrRk": 10,
  "ra": 53,
  "ry": 411,
  "rtd": 2,
  "games": [
   {
    "d": "9/5",
    "opp": "vs Kent State",
    "res": "W 57-0",
    "c": 19,
    "a": 23,
    "y": 270,
    "td": 3,
    "int": 0,
    "sk": 1,
    "qbr": 91.9
   },
   {
    "d": "9/12",
    "opp": "vs Towson (FCS)",
    "res": "W 45-9",
    "c": 10,
    "a": 23,
    "y": 96,
    "td": 0,
    "int": 1,
    "sk": 1,
    "qbr": 28.0
   },
   {
    "d": "9/19",
    "opp": "vs Mississippi State",
    "res": "L 34-41",
    "c": 25,
    "a": 40,
    "y": 282,
    "td": 2,
    "int": 2,
    "sk": 3,
    "qbr": 37.7
   },
   {
    "d": "9/26",
    "opp": "at No. 8 Alabama",
    "res": "L 18-49",
    "c": 18,
    "a": 30,
    "y": 185,
    "td": 1,
    "int": 1,
    "sk": 1,
    "qbr": 79.4
   },
   {
    "d": "10/3",
    "opp": "vs No. 24 Kentucky",
    "res": "L 34-35 OT",
    "c": 20,
    "a": 36,
    "y": 226,
    "td": 2,
    "int": 0,
    "sk": 1,
    "qbr": 54.4
   },
   {
    "d": "10/10",
    "opp": "at No. 16 Florida",
    "res": "W 38-19",
    "c": 23,
    "a": 35,
    "y": 279,
    "td": 4,
    "int": 0,
    "sk": null,
    "qbr": 83.6
   },
   {
    "d": "10/17",
    "bye": true,
    "note": "Bye week"
   }
  ]
 },
 {
  "name": "Kenny Minchey",
  "team": "Kentucky",
  "ab": "UK",
  "side": "",
  "rec": "6-1",
  "c": 141,
  "a": 206,
  "y": 1552,
  "td": 13,
  "int": 1,
  "sk": 11,
  "qbr": 57.1,
  "qbrRk": 12,
  "ra": 25,
  "ry": 9,
  "rtd": 1,
  "games": [
   {
    "d": "9/5",
    "opp": "vs Youngstown State (FCS)",
    "res": "W 45-13",
    "c": 18,
    "a": 27,
    "y": 301,
    "td": 4,
    "int": 0,
    "sk": 0,
    "qbr": 66.1
   },
   {
    "d": "9/12",
    "opp": "vs No. 12 Alabama",
    "res": "L 17-45",
    "c": 18,
    "a": 31,
    "y": 146,
    "td": 1,
    "int": 1,
    "sk": 4,
    "qbr": 8.1
   },
   {
    "d": "9/19",
    "opp": "at No. 9 Texas A&M",
    "res": "W 31-21",
    "c": 14,
    "a": 18,
    "y": 252,
    "td": 2,
    "int": 0,
    "sk": 2,
    "qbr": 90.0
   },
   {
    "d": "9/26",
    "opp": "vs South Alabama",
    "res": "W 45-21",
    "c": 19,
    "a": 25,
    "y": 177,
    "td": 1,
    "int": 0,
    "sk": 2,
    "qbr": 8.5
   },
   {
    "d": "10/3",
    "opp": "at South Carolina",
    "res": "W 35-34 OT",
    "c": 28,
    "a": 41,
    "y": 272,
    "td": 3,
    "int": 0,
    "sk": 3,
    "qbr": 77.9
   },
   {
    "d": "10/10",
    "opp": "vs No. 10 LSU",
    "res": "W 35-34 OT",
    "c": 23,
    "a": 36,
    "y": 246,
    "td": 0,
    "int": 0,
    "sk": null,
    "qbr": 80.9
   },
   {
    "d": "10/17",
    "opp": "at Oklahoma",
    "res": "W 21-13",
    "c": 21,
    "a": 28,
    "y": 158,
    "td": 2,
    "int": 0,
    "sk": null,
    "qbr": 71.5
   }
  ]
 },
 {
  "name": "Faizon Brandon",
  "team": "Tennessee",
  "ab": "TENN",
  "side": "",
  "rec": "6-1",
  "c": 61,
  "a": 101,
  "y": 697,
  "td": 7,
  "int": 1,
  "sk": 15,
  "qbr": 47.0,
  "qbrRk": 14,
  "ra": 53,
  "ry": 130,
  "rtd": 3,
  "games": [
   {
    "d": "9/5",
    "opp": "vs Furman (FCS)",
    "res": "W 56-9",
    "c": 13,
    "a": 17,
    "y": 226,
    "td": 3,
    "int": 0,
    "sk": 1,
    "qbr": 87.2
   },
   {
    "d": "9/12",
    "opp": "at Georgia Tech",
    "res": "W 45-24",
    "c": 10,
    "a": 21,
    "y": 148,
    "td": 2,
    "int": 0,
    "sk": 1,
    "qbr": 78.7
   },
   {
    "d": "9/19",
    "opp": "vs Kennesaw State",
    "res": "W 42-9",
    "c": 18,
    "a": 26,
    "y": 178,
    "td": 1,
    "int": 0,
    "sk": 1,
    "qbr": 46.4
   },
   {
    "d": "9/26",
    "opp": "vs No. 1 Texas",
    "res": "L 17-20",
    "c": 17,
    "a": 29,
    "y": 145,
    "td": 1,
    "int": 0,
    "sk": 10,
    "qbr": null
   },
   {
    "d": "10/3",
    "opp": "vs Auburn (benched in 2nd quarter)",
    "res": "W 24-14",
    "c": 3,
    "a": 8,
    "y": 0,
    "td": 0,
    "int": 1,
    "sk": 2,
    "qbr": null
   },
   {
    "d": "10/10",
    "bye": true,
    "note": "Did not play at Arkansas (shoulder); George MacIntyre started: 18 of 29, 247 yds, 1 TD, 2 INT, W 24-17"
   },
   {
    "d": "10/17",
    "bye": true,
    "note": "Did not play vs No. 6 Alabama (shoulder); George MacIntyre started: 21 of 31, 197 yds, 0 TD, 1 INT, W 26-10"
   }
  ]
 }
]
```

### A.5 `data/sec-teams.json`

```json
{
 "alabama": {
  "n": "Alabama",
  "home": [
   "Tuscaloosa, AL"
  ],
  "next": "Oct. 24 at Tuscaloosa",
  "vac": false
 },
 "arkansas": {
  "n": "Arkansas",
  "home": [
   "Fayetteville, AR",
   "Little Rock, AR"
  ],
  "next": "",
  "vac": false
 },
 "auburn": {
  "n": "Auburn",
  "home": [
   "Auburn, AL"
  ],
  "next": "",
  "vac": false
 },
 "florida": {
  "n": "Florida",
  "home": [
   "Gainesville, FL"
  ],
  "next": "",
  "vac": false
 },
 "georgia": {
  "n": "Georgia",
  "home": [
   "Athens, GA"
  ],
  "next": "",
  "vac": false
 },
 "kentucky": {
  "n": "Kentucky",
  "home": [
   "Lexington, KY"
  ],
  "next": "",
  "vac": false
 },
 "lsu": {
  "n": "LSU",
  "home": [
   "Baton Rouge, LA"
  ],
  "next": "",
  "vac": true
 },
 "mississippi-state": {
  "n": "Mississippi State",
  "home": [
   "Starkville, MS"
  ],
  "next": "",
  "vac": true
 },
 "missouri": {
  "n": "Missouri",
  "home": [
   "Columbia, MO"
  ],
  "next": "",
  "vac": false
 },
 "oklahoma": {
  "n": "Oklahoma",
  "home": [
   "Norman, OK"
  ],
  "next": "Nov. 21 at Norman",
  "vac": false
 },
 "ole-miss": {
  "n": "Ole Miss",
  "home": [
   "Oxford, MS",
   "Jackson, MS"
  ],
  "next": "",
  "vac": true
 },
 "south-carolina": {
  "n": "South Carolina",
  "home": [
   "Columbia, SC"
  ],
  "next": "Nov. 7 at Columbia, S.C.",
  "vac": false
 },
 "tennessee": {
  "n": "Tennessee",
  "home": [
   "Knoxville, TN"
  ],
  "next": "Nov. 14 at Kyle Field",
  "vac": false
 },
 "texas": {
  "n": "Texas",
  "home": [
   "Austin, TX"
  ],
  "next": "Nov. 27 at Kyle Field",
  "vac": false
 },
 "vanderbilt": {
  "n": "Vanderbilt",
  "home": [
   "Nashville, TN"
  ],
  "next": "",
  "vac": false
 }
}
```

Team colors (light / dark) are in `tracker-index.html` under `SEC_TEAMS`.

### A.6 `data/sec-series.json` (rows: [season, A&M pts, opp pts, site, note], ascending)

```json
{"alabama": [[1941, 21, 29, "Dallas, TX", "Cotton Bowl"], [1967, 20, 16, "Dallas, TX", "Cotton Bowl"], [1985, 10, 23, "Birmingham, AL", ""], [1988, 10, 30, "College Station, TX", ""], [2012, 29, 24, "Tuscaloosa, AL", ""], [2013, 42, 49, "College Station, TX", ""], [2014, 0, 59, "Tuscaloosa, AL", ""], [2015, 23, 41, "College Station, TX", ""], [2016, 14, 33, "Tuscaloosa, AL", ""], [2017, 19, 27, "College Station, TX", ""], [2018, 23, 45, "Tuscaloosa, AL", ""], [2019, 28, 47, "College Station, TX", ""], [2020, 24, 52, "Tuscaloosa, AL", ""], [2021, 41, 38, "College Station, TX", ""], [2022, 20, 24, "Tuscaloosa, AL", ""], [2023, 20, 26, "College Station, TX", ""]],
 "arkansas": [[1903, 6, 0, "Houston, TX", ""], [1910, 0, 5, "Fayetteville, AR", ""], [1912, 27, 0, "Dallas, TX", ""], [1927, 40, 6, "College Station, TX", ""], [1928, 12, 27, "Fayetteville, AR", ""], [1929, 13, 14, "College Station, TX", ""], [1930, 0, 13, "Little Rock, AR", ""], [1934, 7, 7, "College Station, TX", ""], [1935, 7, 14, "Little Rock, AR", ""], [1936, 0, 18, "College Station, TX", ""], [1937, 13, 26, "Fayetteville, AR", ""], [1938, 13, 7, "College Station, TX", ""], [1939, 27, 0, "Fayetteville, AR", ""], [1940, 17, 0, "College Station, TX", ""], [1941, 7, 0, "Little Rock, AR", ""], [1942, 41, 0, "College Station, TX", ""], [1943, 13, 0, "Fayetteville, AR", ""], [1944, 6, 7, "College Station, TX", ""], [1945, 34, 0, "Fayetteville, AR", ""], [1946, 0, 7, "College Station, TX", ""], [1947, 21, 21, "Fayetteville, AR", ""], [1948, 6, 28, "College Station, TX", ""], [1949, 6, 27, "Fayetteville, AR", ""], [1950, 42, 13, "College Station, TX", ""], [1951, 21, 33, "Fayetteville, AR", ""], [1952, 31, 12, "College Station, TX", ""], [1953, 14, 41, "Little Rock, AR", ""], [1954, 7, 14, "College Station, TX", ""], [1955, 7, 7, "Fayetteville, AR", ""], [1956, 27, 0, "College Station, TX", ""], [1957, 7, 6, "Fayetteville, AR", ""], [1958, 8, 21, "College Station, TX", ""], [1959, 7, 12, "Fayetteville, AR", ""], [1960, 3, 7, "College Station, TX", ""], [1961, 8, 15, "Fayetteville, AR", ""], [1962, 7, 17, "College Station, TX", ""], [1963, 7, 21, "Little Rock, AR", ""], [1964, 0, 17, "College Station, TX", ""], [1965, 0, 31, "Little Rock, AR", ""], [1966, 0, 34, "College Station, TX", ""], [1967, 33, 21, "Fayetteville, AR", ""], [1968, 22, 25, "College Station, TX", ""], [1969, 13, 35, "Fayetteville, AR", ""], [1970, 6, 45, "College Station, TX", ""], [1971, 17, 9, "Little Rock, AR", ""], [1972, 10, 7, "College Station, TX", ""], [1973, 10, 14, "Fayetteville, AR", ""], [1974, 20, 10, "College Station, TX", ""], [1975, 6, 31, "Little Rock, AR", ""], [1976, 31, 10, "Little Rock, AR", ""], [1977, 20, 26, "College Station, TX", ""], [1978, 7, 26, "Little Rock, AR", ""], [1979, 10, 22, "College Station, TX", ""], [1980, 24, 27, "Fayetteville, AR", ""], [1981, 7, 10, "College Station, TX", ""], [1982, 0, 35, "Little Rock, AR", ""], [1983, 36, 23, "College Station, TX", ""], [1984, 0, 28, "Fayetteville, AR", ""], [1985, 10, 6, "College Station, TX", ""], [1986, 10, 14, "Little Rock, AR", ""], [1987, 14, 0, "College Station, TX", ""], [1988, 20, 25, "Fayetteville, AR", ""], [1989, 22, 23, "College Station, TX", ""], [1990, 20, 16, "Fayetteville, AR", ""], [1991, 13, 3, "College Station, TX", ""], [2009, 19, 47, "Arlington, TX", ""], [2010, 17, 24, "Arlington, TX", ""], [2011, 38, 42, "Arlington, TX", ""], [2012, 58, 10, "College Station, TX", ""], [2013, 45, 33, "Fayetteville, AR", ""], [2014, 35, 28, "Arlington, TX", "OT"], [2015, 28, 21, "Arlington, TX", "OT"], [2016, 45, 24, "Arlington, TX", ""], [2017, 50, 43, "Arlington, TX", "OT"], [2018, 24, 17, "Arlington, TX", ""], [2019, 31, 27, "Arlington, TX", ""], [2020, 42, 31, "College Station, TX", ""], [2021, 10, 20, "Arlington, TX", ""], [2022, 23, 21, "Arlington, TX", ""], [2023, 34, 22, "Arlington, TX", ""], [2024, 21, 17, "Arlington, TX", ""], [2025, 45, 42, "Fayetteville, AR", ""], [2026, 34, 7, "College Station, TX", ""]],
 "auburn": [[1911, 16, 0, "Dallas, TX", ""], [1985, 36, 16, "Dallas, TX", "Cotton Bowl"], [2012, 63, 21, "Auburn, AL", ""], [2013, 41, 45, "College Station, TX", ""], [2014, 41, 38, "Auburn, AL", ""], [2015, 10, 26, "College Station, TX", ""], [2016, 29, 16, "Auburn, AL", ""], [2017, 27, 42, "College Station, TX", ""], [2018, 24, 28, "Auburn, AL", ""], [2019, 20, 28, "College Station, TX", ""], [2020, 31, 20, "Auburn, AL", ""], [2021, 20, 3, "College Station, TX", ""], [2022, 10, 13, "Auburn, AL", ""], [2023, 27, 10, "College Station, TX", ""], [2024, 41, 43, "Auburn, AL", "4OT"], [2025, 16, 10, "College Station, TX", ""]],
 "florida": [[1962, 6, 42, "Gainesville, FL", ""], [1976, 37, 14, "El Paso, TX", "Sun Bowl"], [2012, 17, 20, "College Station, TX", ""], [2017, 19, 17, "Gainesville, FL", ""], [2020, 41, 38, "College Station, TX", ""], [2022, 24, 41, "College Station, TX", ""], [2024, 33, 20, "Gainesville, FL", ""], [2025, 34, 17, "College Station, TX", ""]],
 "georgia": [[1950, 40, 20, "College Park, MD", "Presidential Cup Bowl"], [1953, 14, 12, "Dallas, TX", ""], [1954, 6, 0, "Athens, GA", ""], [1980, 0, 42, "Athens, GA", ""], [2009, 20, 44, "Shreveport, LA", "Independence Bowl"], [2019, 13, 19, "Athens, GA", ""]],
 "kentucky": [[1952, 7, 10, "College Station, TX", ""], [1953, 7, 6, "Lexington, KY", ""], [2018, 20, 14, "College Station, TX", "OT"], [2026, 21, 31, "College Station, TX", ""]],
 "lsu": [[1899, 52, 0, "College Station, TX", ""], [1906, 22, 12, "Baton Rouge, LA", ""], [1907, 11, 5, "College Station, TX", ""], [1908, 0, 26, "New Orleans, LA", ""], [1913, 7, 7, "Houston, TX", ""], [1914, 63, 9, "Dallas, TX", ""], [1916, 0, 13, "Galveston, TX", ""], [1917, 27, 0, "San Antonio, TX", ""], [1920, 0, 0, "College Station, TX", ""], [1921, 0, 6, "Baton Rouge, LA", ""], [1922, 46, 0, "College Station, TX", ""], [1923, 28, 0, "Baton Rouge, LA", ""], [1942, 7, 16, "Baton Rouge, LA", ""], [1943, 28, 13, "Baton Rouge, LA", ""], [1943, 14, 19, "Miami, FL", "Orange Bowl"], [1944, 7, 0, "Baton Rouge, LA", ""], [1945, 12, 31, "Baton Rouge, LA", ""], [1946, 9, 33, "Baton Rouge, LA", ""], [1947, 13, 19, "Baton Rouge, LA", ""], [1948, 13, 14, "Baton Rouge, LA", ""], [1949, 0, 34, "Baton Rouge, LA", ""], [1955, 28, 0, "Dallas, TX", ""], [1956, 9, 6, "Baton Rouge, LA", ""], [1960, 0, 9, "Baton Rouge, LA", ""], [1961, 7, 16, "Baton Rouge, LA", ""], [1962, 0, 21, "Baton Rouge, LA", ""], [1963, 6, 14, "Baton Rouge, LA", ""], [1964, 6, 9, "Baton Rouge, LA", ""], [1965, 0, 10, "Baton Rouge, LA", ""], [1966, 7, 7, "Baton Rouge, LA", ""], [1967, 6, 17, "Baton Rouge, LA", ""], [1968, 12, 13, "Baton Rouge, LA", ""], [1969, 6, 35, "Baton Rouge, LA", ""], [1970, 20, 18, "Baton Rouge, LA", ""], [1971, 0, 37, "Baton Rouge, LA", ""], [1972, 17, 42, "Baton Rouge, LA", ""], [1973, 23, 28, "Baton Rouge, LA", ""], [1974, 21, 14, "Baton Rouge, LA", ""], [1975, 39, 8, "Baton Rouge, LA", ""], [1986, 17, 35, "Baton Rouge, LA", ""], [1987, 3, 17, "College Station, TX", ""], [1988, 0, 27, "Baton Rouge, LA", ""], [1989, 28, 16, "College Station, TX", ""], [1990, 8, 17, "Baton Rouge, LA", ""], [1991, 45, 7, "College Station, TX", ""], [1992, 31, 22, "Baton Rouge, LA", ""], [1993, 24, 0, "College Station, TX", ""], [1994, 18, 13, "Baton Rouge, LA", ""], [1995, 33, 17, "College Station, TX", ""], [2010, 24, 41, "Arlington, TX", "Cotton Bowl"], [2012, 19, 24, "College Station, TX", "LSU win vacated"], [2013, 10, 34, "Baton Rouge, LA", "LSU win vacated"], [2014, 17, 23, "College Station, TX", "LSU win vacated"], [2015, 7, 19, "Baton Rouge, LA", "LSU win vacated"], [2016, 39, 54, "College Station, TX", ""], [2017, 21, 45, "Baton Rouge, LA", ""], [2018, 74, 72, "College Station, TX", "7OT"], [2019, 7, 50, "Baton Rouge, LA", ""], [2020, 20, 7, "College Station, TX", ""], [2021, 24, 27, "Baton Rouge, LA", ""], [2022, 38, 23, "College Station, TX", ""], [2023, 30, 42, "Baton Rouge, LA", ""], [2024, 38, 23, "College Station, TX", ""], [2025, 49, 25, "Baton Rouge, LA", ""], [2026, 6, 35, "Baton Rouge, LA", ""]],
 "mississippi-state": [[1912, 41, 7, "Houston, TX", ""], [1913, 0, 6, "Dallas, TX", ""], [1915, 0, 7, "Dallas, TX", ""], [1937, 14, 0, "Tyler, TX", ""], [2000, 41, 43, "Shreveport, LA", "Independence Bowl (OT)"], [2012, 38, 13, "Starkville, MS", ""], [2013, 51, 41, "College Station, TX", ""], [2014, 31, 48, "Starkville, MS", ""], [2015, 30, 17, "College Station, TX", ""], [2016, 28, 35, "Starkville, MS", ""], [2017, 14, 35, "College Station, TX", ""], [2018, 13, 28, "Starkville, MS", "MSU win vacated"], [2019, 49, 30, "College Station, TX", ""], [2020, 28, 14, "Starkville, MS", ""], [2021, 22, 26, "College Station, TX", ""], [2022, 24, 42, "Starkville, MS", ""], [2023, 51, 10, "College Station, TX", ""], [2024, 34, 24, "Starkville, MS", ""], [2025, 31, 9, "College Station, TX", ""]],
 "missouri": [[1957, 28, 0, "Columbia, MO", ""], [1958, 12, 0, "College Station, TX", ""], [1992, 26, 13, "Columbia, MO", ""], [1993, 73, 0, "College Station, TX", ""], [1998, 17, 14, "College Station, TX", ""], [1999, 51, 14, "Columbia, MO", ""], [2002, 27, 33, "College Station, TX", ""], [2003, 22, 45, "Columbia, MO", ""], [2006, 25, 19, "College Station, TX", ""], [2007, 26, 40, "Columbia, MO", ""], [2010, 9, 30, "College Station, TX", ""], [2011, 31, 38, "College Station, TX", "OT"], [2012, 59, 29, "College Station, TX", ""], [2013, 21, 28, "Columbia, MO", ""], [2014, 27, 34, "College Station, TX", ""], [2021, 35, 14, "Columbia, MO", ""], [2024, 41, 10, "College Station, TX", ""], [2025, 38, 17, "Columbia, MO", ""], [2026, 6, 27, "Columbia, MO", ""]],
 "oklahoma": [[1903, 0, 6, "Bryan, TX", ""], [1907, 19, 0, "College Station, TX", ""], [1909, 14, 8, "Dallas, TX", ""], [1912, 28, 6, "Houston, TX", ""], [1944, 14, 21, "Oklahoma City, OK", ""], [1945, 19, 14, "Norman, OK", ""], [1946, 7, 10, "Norman, OK", ""], [1947, 14, 26, "Norman, OK", ""], [1948, 14, 42, "Norman, OK", ""], [1949, 13, 33, "Norman, OK", ""], [1950, 28, 34, "Norman, OK", ""], [1951, 14, 7, "College Station, TX", ""], [1993, 14, 44, "Norman, OK", ""], [1994, 36, 14, "College Station, TX", ""], [1996, 33, 16, "College Station, TX", ""], [1997, 51, 7, "Norman, OK", ""], [1998, 29, 0, "College Station, TX", ""], [1999, 6, 51, "Norman, OK", ""], [2000, 31, 35, "College Station, TX", ""], [2001, 10, 31, "Norman, OK", ""], [2002, 30, 26, "College Station, TX", ""], [2003, 0, 77, "Norman, OK", ""], [2004, 35, 42, "College Station, TX", ""], [2005, 30, 36, "Norman, OK", ""], [2006, 16, 17, "College Station, TX", ""], [2007, 14, 42, "Norman, OK", ""], [2008, 28, 66, "College Station, TX", ""], [2009, 10, 65, "Norman, OK", ""], [2010, 33, 19, "College Station, TX", ""], [2011, 25, 41, "Norman, OK", ""], [2012, 41, 13, "Arlington, TX", "Cotton Bowl"]],
 "ole-miss": [[1911, 17, 0, "College Station, TX", ""], [1914, 14, 7, "Beaumont, TX", ""], [1975, 7, 0, "College Station, TX", ""], [1980, 23, 20, "Jackson, MS", ""], [2012, 30, 27, "Oxford, MS", ""], [2013, 41, 38, "Oxford, MS", ""], [2014, 20, 35, "College Station, TX", "Ole Miss win vacated"], [2015, 3, 23, "Oxford, MS", ""], [2016, 28, 29, "College Station, TX", "Ole Miss win vacated"], [2017, 31, 24, "Oxford, MS", ""], [2018, 38, 24, "College Station, TX", ""], [2019, 24, 17, "Oxford, MS", ""], [2021, 19, 29, "Oxford, MS", ""], [2022, 28, 31, "College Station, TX", ""], [2023, 35, 38, "Oxford, MS", ""]],
 "south-carolina": [[2014, 52, 28, "Columbia, SC", ""], [2015, 35, 28, "College Station, TX", ""], [2016, 24, 13, "Columbia, SC", ""], [2017, 24, 17, "College Station, TX", ""], [2018, 26, 23, "Columbia, SC", ""], [2019, 30, 6, "College Station, TX", ""], [2020, 48, 3, "Columbia, SC", ""], [2021, 44, 14, "College Station, TX", ""], [2022, 24, 30, "Columbia, SC", ""], [2023, 30, 17, "College Station, TX", ""], [2024, 20, 44, "Columbia, SC", ""], [2025, 31, 30, "College Station, TX", ""]],
 "tennessee": [[1957, 0, 3, "Jacksonville, FL", "Gator Bowl"], [2004, 7, 38, "Dallas, TX", "Cotton Bowl"], [2016, 45, 38, "College Station, TX", "2OT"], [2020, 34, 13, "Knoxville, TN", ""], [2023, 13, 20, "Knoxville, TN", ""]],
 "texas": [[1894, 0, 38, "Austin, TX", ""], [1898, 0, 48, "Austin, TX", ""], [1899, 0, 6, "San Antonio, TX", ""], [1900, 0, 5, "San Antonio, TX", ""], [1900, 0, 11, "Austin, TX", ""], [1901, 0, 17, "San Antonio, TX", ""], [1901, 0, 32, "Austin, TX", ""], [1902, 0, 0, "San Antonio, TX", ""], [1902, 12, 0, "Austin, TX", ""], [1903, 6, 29, "Austin, TX", ""], [1904, 6, 34, "Austin, TX", ""], [1905, 0, 27, "Austin, TX", ""], [1906, 0, 24, "Austin, TX", ""], [1907, 0, 0, "Dallas, TX", ""], [1907, 6, 11, "Austin, TX", ""], [1908, 8, 24, "Houston, TX", ""], [1908, 12, 28, "Austin, TX", ""], [1909, 23, 0, "Houston, TX", ""], [1909, 5, 0, "Austin, TX", ""], [1910, 14, 8, "Houston, TX", ""], [1911, 0, 6, "Houston, TX", ""], [1915, 13, 0, "College Station, TX", ""], [1916, 7, 21, "Austin, TX", ""], [1917, 7, 0, "College Station, TX", ""], [1918, 0, 7, "Austin, TX", ""], [1919, 7, 0, "College Station, TX", ""], [1920, 3, 7, "Austin, TX", ""], [1921, 0, 0, "College Station, TX", ""], [1922, 14, 7, "Austin, TX", ""], [1923, 0, 6, "College Station, TX", ""], [1924, 0, 7, "Austin, TX", ""], [1925, 28, 0, "College Station, TX", ""], [1926, 5, 14, "Austin, TX", ""], [1927, 28, 7, "College Station, TX", ""], [1928, 0, 19, "Austin, TX", ""], [1929, 13, 0, "College Station, TX", ""], [1930, 0, 26, "Austin, TX", ""], [1931, 7, 6, "College Station, TX", ""], [1932, 0, 21, "Austin, TX", ""], [1933, 10, 10, "College Station, TX", ""], [1934, 0, 13, "Austin, TX", ""], [1935, 20, 6, "College Station, TX", ""], [1936, 0, 7, "Austin, TX", ""], [1937, 7, 0, "College Station, TX", ""], [1938, 6, 7, "Austin, TX", ""], [1939, 20, 0, "College Station, TX", ""], [1940, 0, 7, "Austin, TX", ""], [1941, 0, 23, "College Station, TX", ""], [1942, 6, 12, "Austin, TX", ""], [1943, 13, 27, "College Station, TX", ""], [1944, 0, 6, "Austin, TX", ""], [1945, 10, 20, "College Station, TX", ""], [1946, 7, 24, "Austin, TX", ""], [1947, 13, 32, "College Station, TX", ""], [1948, 14, 14, "Austin, TX", ""], [1949, 14, 42, "College Station, TX", ""], [1950, 0, 17, "Austin, TX", ""], [1951, 22, 21, "College Station, TX", ""], [1952, 12, 32, "Austin, TX", ""], [1953, 12, 21, "College Station, TX", ""], [1954, 13, 22, "Austin, TX", ""], [1955, 6, 21, "College Station, TX", ""], [1956, 34, 21, "Austin, TX", ""], [1957, 7, 9, "College Station, TX", ""], [1958, 0, 27, "Austin, TX", ""], [1959, 17, 20, "College Station, TX", ""], [1960, 14, 21, "Austin, TX", ""], [1961, 0, 25, "College Station, TX", ""], [1962, 3, 13, "Austin, TX", ""], [1963, 13, 15, "College Station, TX", ""], [1964, 7, 26, "Austin, TX", ""], [1965, 17, 21, "College Station, TX", ""], [1966, 14, 22, "Austin, TX", ""], [1967, 10, 7, "College Station, TX", ""], [1968, 14, 35, "Austin, TX", ""], [1969, 12, 49, "College Station, TX", ""], [1970, 14, 52, "Austin, TX", ""], [1971, 14, 34, "College Station, TX", ""], [1972, 3, 38, "Austin, TX", ""], [1973, 13, 42, "College Station, TX", ""], [1974, 3, 32, "Austin, TX", ""], [1975, 20, 10, "College Station, TX", ""], [1976, 27, 3, "Austin, TX", ""], [1977, 28, 57, "College Station, TX", ""], [1978, 7, 22, "Austin, TX", ""], [1979, 13, 7, "College Station, TX", ""], [1980, 24, 14, "Austin, TX", ""], [1981, 13, 21, "College Station, TX", ""], [1982, 16, 53, "Austin, TX", ""], [1983, 13, 45, "College Station, TX", ""], [1984, 37, 12, "Austin, TX", ""], [1985, 42, 10, "College Station, TX", ""], [1986, 16, 3, "Austin, TX", ""], [1987, 20, 13, "College Station, TX", ""], [1988, 28, 24, "Austin, TX", ""], [1989, 21, 10, "College Station, TX", ""], [1990, 27, 28, "Austin, TX", ""], [1991, 31, 14, "College Station, TX", ""], [1992, 34, 13, "Austin, TX", ""], [1993, 18, 9, "College Station, TX", ""], [1994, 34, 10, "Austin, TX", ""], [1995, 6, 16, "College Station, TX", ""], [1996, 15, 51, "Austin, TX", ""], [1997, 27, 16, "College Station, TX", ""], [1998, 24, 26, "Austin, TX", ""], [1999, 20, 16, "College Station, TX", ""], [2000, 17, 43, "Austin, TX", ""], [2001, 7, 21, "College Station, TX", ""], [2002, 20, 50, "Austin, TX", ""], [2003, 15, 46, "College Station, TX", ""], [2004, 13, 26, "Austin, TX", ""], [2005, 29, 40, "College Station, TX", ""], [2006, 12, 7, "Austin, TX", ""], [2007, 38, 30, "College Station, TX", ""], [2008, 9, 49, "Austin, TX", ""], [2009, 39, 49, "College Station, TX", ""], [2010, 24, 17, "Austin, TX", ""], [2011, 25, 27, "College Station, TX", ""], [2024, 7, 17, "College Station, TX", ""], [2025, 17, 27, "Austin, TX", ""]],
 "vanderbilt": [[2013, 56, 24, "College Station, TX", ""], [2015, 25, 0, "Nashville, TN", ""], [2020, 17, 12, "College Station, TX", ""]]}
```
