## v3.2.9 — 24 June 2026

### RBA cash rate: live fetch + runtime override

The RBA cash rate, decision date, and next-meeting note are no longer hardcoded in the UI.
They now flow from a single `RbaInfo` holder that is:

- **Overlaid at startup** from a new optional `rba` section in `rates_override.json`
  (`rate`, `asOf`, `nextMeeting`, `note`, `environmentDate`) — so you can update the cash rate
  and meeting schedule without rebuilding.
- **Fetched live**: "Fetch Live Rates" now also pulls the current cash rate from rba.gov.au
  (best-effort, never fails the run) and shows it in the Live Rate Review dialog. **Apply to
  Analysis** writes the cash rate into the override's `rba` section and refreshes the header
  ticker, the Market Snapshot RBA row, and the About panel immediately.
- The numeric rate also feeds the AI commentary prompt, so AI analysis uses the current rate.

The shipped `rates_override.json` now reflects the cash rate held at 4.35% on 16 Jun 2026 with the
next decision on 11 Aug 2026.

> Note: the meeting *schedule* is a fixed RBA calendar and isn't scraped — keep `nextMeeting` in
> the override file. Everything else (the rate itself) can come from the live fetch.

### "Show Database Rates" button removed

It duplicated the Savings DB / Term Deposits tabs (which already list every embedded rate), so it's
gone. "Fetch Live Rates" now sits on its own in the Settings panel. The Settings log still shows the
database summary and override status.

### Notes

- App version → **3.2.9**. Embedded DB stays at 3.2.6 (16 May 2026); current rates ride in the
  override and the live fetch.
- Still no new NuGet dependencies.

---

## v3.2.8 — 17 June 2026

### Tier 1.5 — Runtime rate overrides (update rates without rebuilding)

The app now reads a `rates_override.json` file beside the exe at startup and overlays its
values onto the embedded database. This is the authoritative way to keep rates current
between releases — no recompile needed.

- **`rates_override.json` loader.** Keyed by account Id (see `AccountData.vb`); every rate
  field is optional (`MaxRate`, `BaseRate`, `BonusRate`, `IntroRate`, `PostIntroRate` for
  savings; `R3`–`R60` for term deposits). Rates are `Double` fractions (`0.0575` = 5.75%).
  Malformed or missing files are ignored — the embedded database stays intact.
- **"Apply to Analysis" button** added to the Live Rate Review dialog. After a live fetch,
  this writes the matched rates into `rates_override.json` and reapplies them immediately, so
  recommendations use the live values (previously the live fetch only updated the snapshot panel).
- **Today's rates shipped.** A `rates_override.json` with rates verified 17 June 2026 is now
  included and copied into `build\` by `build.bat`. The About panel shows the override status.

### Free / alternative AI providers

The AI commentary feature is no longer Anthropic-only. Settings now has a **provider
selector** supporting Anthropic (Claude), xAI (Grok), Groq, Google Gemini, OpenAI, and any
custom OpenAI-compatible endpoint.

- Free options: **Groq** and **Google Gemini** both offer free tiers.
- Anthropic uses its native `/v1/messages` API; all other providers use the OpenAI-compatible
  `/chat/completions` shape (Bearer auth). Provider, model, and base URL persist in `.config.json`.
- Presets auto-fill the base URL and a sensible default model; **Test** validates the selected
  provider. The `sk-ant-` key check now applies only when Anthropic is selected.

### Notes

- App version → **3.2.8**. Embedded DB stays at **3.2.6** (16 May 2026); current rates ride in
  the override file, surfaced as "Rates Override" in About.
- **Rates-override date now shown consistently:** when an override is active, the header subtitle shows "Rates: {override date}", and both the header and the About "Rates Override" row refresh after **Apply to Analysis** (previously they kept the startup values while the log showed the newer applied date/count).
- Still no new NuGet dependencies — provider calls use built-in `System.Net.Http`.
- **Version display fix:** the window title bar previously showed `DB_VERSION` (3.2.6) instead of the app version. Added a dedicated `APP_VERSION` constant in `AccountData.vb`; the title bar, both header subtitles, and the About "Version" row now all read from it, so they can't drift apart again. (DB version stays separate and still shows 3.2.6.)
- **build.bat hardened:** writes its log to `%TEMP%\savings_analyzer_build.log` (not the project folder, so an editor/file-watcher can't re-trigger on it); deletes the old exe before publishing so success/failure is unambiguous; NSIS installer is opt-in (`build.bat installer`); no `pause` prompt (the window auto-closes), and a re-entry throttle refuses to start if the script was already launched seconds earlier. If a build *loop* persists, the cause is external (an IDE build/watch task or wrapper relaunching it) — run build.bat from a plain Command Prompt with no editor open.
- Verify provider model names against each vendor's current list (they change); the model field
  is editable.

---

## v3.2.7 — 17 June 2026

### Live Rate Fetch (Tier 1) — in-app rate checking without a rebuild

The app can now pull current rates from savings.com.au at runtime, review them against
the built-in database, and persist a market snapshot — no recompilation required.

**What changed:**

- **New "Fetch Live Rates" button** (Settings tab, beside "Show Database Rates"). Performs
  a live HTTP request to savings.com.au and parses the current rate table.
- **Fixed the 403 problem.** The previous failure was *not* hard bot-blocking — the default
  .NET `HttpClient` sends no browser `User-Agent`, which the site rejects. `FetchLiveRates()`
  now sends real browser headers (`User-Agent`, `Accept`, `Accept-Language`) plus gzip/deflate
  decompression. Result: HTTP 200 with the full page (~1.0 MB).
- **Review dialog before anything is saved.** Fetched rates appear in a modal grid
  (Bank · Live Rate · Type · DB Rate · Diff · page Context). Nothing is applied unless you
  click *Save as Market Snapshot*.
- **Snapshot persistence.** Saving writes the top intro + top ongoing rate into
  `.config.json` via the existing `ScrapedSnapshot`, so the header and Market Snapshot panel
  show live data on next launch. Manual `AccountData.vb` editing remains the fallback.

**Parser notes:**

- Bank names are matched at word boundaries (so "ING" no longer matches inside "savings").
- Takes the **highest** plausible rate within a bank's row, not the first — comparison
  tables list Base Rate before Max Rate, so the first match was the base rate (this is why
  early testing showed Rabobank at 3.45% instead of 5.35%).
- Sanity-bounded to 3.0–7.5%; results are auto-detected by proximity and meant for review,
  not blind trust.

### Build script

- **`build.bat` rewritten.** Removed the duplicate "BUILD SUCCEEDED" banner, consolidated to
  a single exit path, added `setlocal`, and stripped all non-ASCII characters (the em-dash /
  box-drawing glyphs were rendering as mojibake under non-UTF-8 codepages).

### Notes

- App version → **3.2.7**. Database version unchanged at **3.2.6** (16 May 2026) — this
  release adds a feature, not new rate data.
- No new NuGet dependencies (live fetch uses built-in `System.Net.Http`).

---

## v3.2.6 — 16 May 2026

### Full Database Rebuild — Savings & Term Deposits

**Sources:** Canstar (15/05/2026), Finder (15/05/2026), Savings.com.au (16/05/2026), Money.com.au (15/05/2026), official bank websites.

**Savings accounts updated (40 total):**

| Account | Old Rate | New Rate | Notes |
|---|---|---|---|
| Rabobank HISA | 5.65% intro | **5.90% intro** | Highest in market — 4 months, up to $250k |
| ING Savings Accelerator | 5.65% intro | **5.85% intro** | From 15 May 2026, reverts to 4.80% |
| uBank Save Account | 5.60% intro | **5.85% intro** | From 12 May 2026, requires Spend account |
| Judo Bank Personal Savings | 5.35% | **5.35% confirmed** | No conditions — market leader ongoing |
| ING Savings Maximiser | 5.50% | **5.50% confirmed** | With conditions |
| Suncorp Growth Saver | 5.50% | **5.50% confirmed** | From 15 May |
| Westpac Life (18-34) | 5.50% | **5.50% confirmed** | Up to $30k |
| CommBank NetBank Saver | 5.00% intro | **5.20% intro** | Special offer, limited time |
| NAB iSaver | 5.00% intro | **5.25% intro** | From 11 May |
| Macquarie Savings (intro) | 5.00% | **5.10% 4mo** | Then 4.25% ongoing |
| MyState Hello Saver | 5.15% | **5.40% intro** | From 14 May |
| AMP GO Save | 5.10% | **5.10% confirmed** | No conditions, up to $500k |
| ANZ Plus Save | ~4.75% | **4.95% confirmed** | No conditions ongoing |

**Term deposits updated — all 22 providers:**

| Provider | Old 12mo | New 12mo | Notes |
|---|---|---|---|
| Gateway Bank | 5.60% | **5.60% confirmed** | Market leader (equal) |
| Bank Australia | 5.60% | **5.60% confirmed** | Market leader (equal), from 20 May |
| Heartland Bank | 5.55% | **5.55% confirmed** | Top-tier, up to $1M |
| Rabobank | 5.55% | **5.50%** | Slight correction |
| Great Southern Bank | 5.55% | **5.50%** | Update |
| Judo Bank | 5.35% | **5.35% confirmed** | Finder Award winner 2026 |
| ANZ | 5.25% | **5.25% confirmed** | Best of big 4 |
| CommBank | 5.20% | **5.20% confirmed** | Special offer |
| NAB | 5.05% | **5.05%** | Effective 11 May |
| Westpac | 5.05% | **5.05%** | Special offer existing customers |
| Bendigo Bank | 5.10% | **5.10% confirmed** |  |
| All others | varied | +0.25% applied | Standard RBA pass-through |

**Market snapshot updated:**
- Best Honeymoon: 5.90% (was 5.65%)
- Best TD 12mo: 5.60% Gateway + Bank Australia (was 5.40% Heartland)

---

## v3.2.5 — 15 May 2026

### Database & Features
- 4 new savings accounts: Heartland MySavings (5.00% no conditions), AMP GO Save (5.10%), Easy Street Flex Saver (5.00%), St George Maxi Saver (5.10% intro 6mo)
- Rate check button renamed "Show Database Rates" — accurate description of what it does
- Fixed misleading "contact the developer" message — now shows where to verify rates yourself

---

## v3.2.5 — 15 May 2026

### Database Updates (verified 15 May 2026)
**Savings accounts — 40 total (3 new):**
- New: **Heartland Bank MySavings** — 5.00% p.a. no conditions, tiered up to $2M, min age 14 (launched Feb 2025, raised with RBA hikes)
- New: **AMP Bank GO Save** — 5.10% p.a. no conditions, up to $500k, from 11 May 2026
- New: **Easy Street Flex Saver** — 5.00% p.a. no conditions, up to $3M, from 13 May 2026
- New: **St George Maxi Saver** — 5.10% p.a. intro 6 months (new customers), reverts to 1.25%, linked account + $50/mo required
- Updated: ING Savings Accelerator 5.85% (was 5.65%), ING Savings Maximiser 5.50% (was 5.25%), MyState Hello Saver 5.40% (was 5.15%), Suncorp Growth Saver 5.50% (was 5.25%)

**Term deposits — all 22 providers updated post-RBA hike:**
- Heartland Bank TD 12mo: **5.55%** (was 5.40%) — confirmed market leader
- Gateway Bank 12mo: 5.60%, Bank Australia 12mo: 5.60%
- ANZ 12mo: **5.25%** (was 4.75%) — major correction
- CommBank 12mo: **5.20%** special offer (was 5.10%)
- Bendigo Bank 12mo: **5.10%** (was 4.75%) — major correction
- All other providers updated ~+0.25% in line with RBA hike
- Rabobank 5yr: 5.70% confirmed (largest balance tiers)
- DB_DATE updated to 15 May 2026

### Bug Fixes
- **Rate check button** — renamed from "Scrape Live Rates Now" to "Check Rate Database". Now honestly shows a connectivity test result + top 8 DB rates. Previous scraper was broken: all AU comparison sites (savings.com.au, finder.com.au, canstar.com.au) return HTTP 403 to non-browser requests, and the naive regex was picking up wrong bank rates from comparison tables (e.g. Heartland showing 5.65% — that was Rabobank's rate appearing nearby in stripped HTML).

### Why the old scraper failed
Australian financial comparison sites use bot-blocking (Cloudflare, custom 403) and JavaScript rendering — the actual rate data is injected by JS after page load, not present in the raw HTTP response. A desktop app cannot reliably scrape these sites without a full browser engine.

---

# Australian Savings Account Analyzer — Changelog

---

## v3.2.4 — 5 May 2026

### Bug Fixes
- **Profile save finally fixed** — root cause identified as `System.Text.Json` VB.NET `Decimal` serialization bug. Introduced `ProfileDto` class using `Double` for monetary fields (`Amount`, `MonthlyDeposit`) with explicit `CDbl`/`CDec` conversion. Profiles now persist correctly across sessions.
- **Profiles grid renders correctly** — fixed WinForms dock order in Profiles tab. Grid (`DockStyle.Fill`) must be added before heading/button bars (`DockStyle.Top`); reversed order caused header to overlap grid, hiding all rows.
- **Export to Excel button disappears on theme switch** — `ApplyThemeToContainer` identified gold buttons by `BackColor` comparison which is unreliable after rendering. Fixed by tagging gold buttons with `.Tag = "gold"` in `StyleGoldButton()`; theme switcher now checks tag and skips those buttons entirely.
- **Rate label text overlap ("Verified · Base" hidden under rate)** — Georgia 20pt font renders ~30px tall but `y += 32` left only 2px clearance. Increased to `y += 40` for savings cards and `y += 44` for TD cards.
- **Grid row selection invisible in light theme** — selection colour was `BgElevated` (slightly lighter navy), invisible against white background. Changed to **gold background / navy text** for all grids in both themes.

### New Features
- **Explicit Save Profile button** — added to Analyse tab below the Analyse button; saves current form values immediately without needing to run an analysis. Profile count shown in Profiles tab heading ("Saved Profiles — 2 saved").
- **Scraped rates persist across sessions** — `ScrapedSnapshot` class stores last-scraped date, top honeymoon rate, and top ongoing rate in `.config.json`. Restored on next launch via `RestoreSnapshot()`.
- **Market snapshot updates after scraping** — named labels (`lblSnapshotRate0–5`, `lblSnapshotSub0–5`, `lblSnapshotDate`) update live after a successful scrape. "Scraped" wording replaced with clean date/time format.
- **RBA Cash Rate updated to 4.35%** — RBA raised rates 25bp on 5 May 2026 (8-1 vote). Updated `AccountData.RBA_RATE`, header ticker, Market Snapshot RBA row, About panel, DB_DATE, and README. RBA row always restores to confirmed current rate on launch.
- **About panel layout fixed** — right-side value labels now use a single `rightCard.Resize` handler that repositions all labels correctly. Previous per-row `Resize` handlers never fired because child panels started at width=0.
- **Comprehensive README** — full rewrite covering: quick start, how the database works, account categories, scoring engine and formulas, tab-by-tab usage guide, AI Fund Manager (what it adds, cost, setup), data sources, government guarantee details, technical specs.

### Data Updates
- RBA Cash Rate: **4.10% → 4.35%** (raised 5 May 2026, 8-1 board vote)
- DB_DATE updated to 5 May 2026
- Next RBA meeting: June 2026 (further hikes forecast to 4.70% by end 2026)

---

## v3.2.3 — 3 May 2026

### Bug Fixes
- **`Application.StartupPath` not available in Engine.vb** — `System.Windows.Forms.Application` not in scope outside WinForms context. Replaced with `Directory.GetCurrentDirectory()` fallback.
- **`Private Shared` invalid in Module** — `Shared` keyword is meaningless inside VB.NET Modules (everything is already implicitly shared). Removed from config path function.
- **ConfigManager rewritten** — replaced fragile `JsonNode` mutation with strongly-typed `ConfigData` class. `Public` visibility required for `System.Text.Json` reflection.
- **API payload JSON** — replaced `Dictionary(Of String, Object)` serialization (caused boxing issues) with explicit JSON string construction using `JEsc()` helper.
- **API Test button** — rewrote to use direct `HttpClient` POST instead of full `CallApi` path (which tried to parse response as JSON, failing on plain text responses).

### New Features
- **Market snapshot rows update after scraping** — named `lblSnapshotRate0/1` and `lblSnapshotSub0/1` labels update with live scraped bank names and rates.
- **Config file path shown in Settings** — log box shows exact path of `.config.json` for debugging persistence issues.
- **`Environment.ProcessPath` for config path** — uses actual exe location rather than temp extraction directory, ensuring config persists between runs in single-file publish mode.

### Data Updates
- RBA Cash Rate messaging updated to reflect May 5 decision probability.

---

## v3.2.2 — 2 May 2026

### Bug Fixes
- **Icon not showing** — single-file publish mode extracts to a temp directory that changes every run. Icon now embedded as base64 string in source code, loaded from `MemoryStream` at runtime — no file path lookup needed.
- **Light theme results cards dark** — `AddMetricRow`, `AddRec`, `AddCardHeading`, `AddFieldLabel`, `AddField` all used hardcoded `Theme.White`, `Theme.TextMuted`, `Theme.BgElevated`. Replaced with `ThemeState.CurText`, `ThemeState.CurMuted`, `ThemeState.CurCard`.
- **Theme toggle re-renders results** — `OnThemeToggle` now calls `ShowResults()` to rebuild dynamic controls fresh rather than attempting to recolour them in-place.
- **Config path persists** — `ResolveConfigPath()` now uses `Environment.ProcessPath` (actual exe directory) as priority, avoiding temp-dir config loss.
- **Version mismatch in title bar** — hardcoded `"v3.2"` replaced with `$"v{AccountData.DB_VERSION}"`.

### New Features
- **Light/dark theme toggle fully working** — `ApplyThemeToContainer` recursively walks all form controls updating backgrounds, text colours, grid styles, and button colours per theme.
- **Live Rate Scraper (no API key)** — `ScrapeCurrentRates()` fetches from savings.com.au and canstar.com.au using realistic browser User-Agent. Parses 20 known bank names and nearby rate patterns. Falls back to second source if fewer than 3 results.
- **Header banner updates after scrape** — turns green with `Rates verified: [date]`.

---

## v3.2.1 — 2 May 2026

### Bug Fixes (all compiler errors)
- **BC30481/BC30460** — `MainForm` split across two files without `Partial` keyword. Added `Partial` to both `MainForm_Part1.vb` and `MainForm_Part2.vb`. Added `End Class` to Part1.
- **BC36637** — `??(i)` C# null-coalescing operator invalid in VB.NET. Replaced with explicit `If ... IsNot Nothing` blocks and `CType(..., JsonArray)(i)` indexing.
- **BC30201** — `For Each step In steps` — `step` is a reserved VB.NET keyword (used in `For i = 0 To 10 Step 2`). Renamed loop variable to `stepText` in both files.
- **BC30002** — `Imports System.Text.Json.Linq` doesn't exist. Changed to `System.Text.Json.Nodes`.
- **BC30451 'Path'** — missing `Imports System.IO` in MainForm_Part1.vb.
- **BC30456 StyleGoldButton/StyleComboBox** — Theme module methods renamed from short forms (`Gold`, `Cmb`) to full names matching MainForm calls.
- **BC30040** — `Get : Return x : End Get` single-line property bodies invalid. Expanded all to multi-line.
- **BC30205/BC30087** — Single-line `If condition Then value` chained with `ElseIf` on next line invalid. Converted to proper block `If ... Then / ElseIf ... Then / End If`.
- **vbNewLine obsolete** — replaced all `vbNewLine` with `Environment.NewLine`.
- **BC30593 Private Shared** — `Shared` invalid inside Module. Removed.

---

## v3.2.0 — 2 May 2026 (Initial Desktop Release)

### New Application
Complete rewrite from web PWA to standalone VB.NET WinForms desktop application.

### Features
- **6-tab UI**: Analyse, Results, Savings DB, Term Deposits, Profiles, Settings
- **37 savings accounts** + **22 term deposit providers** embedded as VB.NET arrays
- **Scoring engine** — filters by age, government guarantee, conditions preference; ranks by effective interest; deduplicates by banking group
- **Term deposit support** — 8 term lengths (3, 6, 9, 12, 18, 24, 36, 60 months); full rate matrix display
- **Results cards** — top 3 recommendations with rate, conditions, metrics, action steps, honeymoon reminders, ADI guarantee status
- **Excel export** — 4-sheet workbook (analysis, savings DB, TD matrix, all accounts ranked)
- **Profile save/load** — JSON persistence via ConfigManager
- **Navy + gold theme** — matching web PWA aesthetic
- **Light/dark theme toggle** — ☀/🌙 button in header
- **NSIS installer script** — Modern UI, Start Menu + Desktop shortcuts, uninstaller
- **build.bat** — logs to `build.log`, checks .NET SDK, runs `dotnet publish`, optional NSIS
- **No external packages** — native XLSX writer (Open XML), System.Text.Json for config/API, System.Net.Http for scraping and API calls
- **Self-contained** — single win-x64 exe (~160MB), no runtime required on target machine

### Financial Formulas
- Monthly: `(balance × rate) / 12`
- Annual compound: `balance × ((1+r/12)¹²−1)`
- Future value with deposits: `P×(1+r)ⁿ + PMT×((1+r)ⁿ−1)/r`
- Term deposit simple: `principal × rate × (months/12)`

---

## v3.0.x — April 2026 (Web PWA)

Web-based Progressive Web App built with HTML/CSS/JavaScript.

- 37 savings accounts + 18 term deposit providers
- Claude AI fund manager commentary (Anthropic API)
- PWA offline support, light/dark theme, Excel export (SheetJS)
- `launch.bat` with local server, Python GUI launcher
- Fixed: infinite loop from duplicate `DOMContentLoaded` handlers
- Fixed: UNC path issues in `launch.bat`
