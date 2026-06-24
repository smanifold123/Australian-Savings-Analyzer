# 🏛 Australian Savings Account Analyzer
### Desktop Edition · v3.2.9 · VB.NET + WinForms · No browser required

A professional-grade Australian savings account comparison tool that analyses 40 savings accounts and 22 term deposit providers against your personal financial profile. All calculations run completely offline. No subscription. No ads.

**RBA Cash Rate: 4.35% p.a.** (held 16 Jun 2026 · next meeting 11 Aug 2026) · Embedded data verified: 16 May 2026

---

## Quick Start

**Step 1 — Install .NET 8 SDK** (one-time, ~220MB, free)
Download the **Windows x64 Installer**: https://dotnet.microsoft.com/download/dotnet/8.0

**Step 2 — Run build.bat**
Double-click `build.bat` in the `SavingsAnalyzer_Build` folder, or run it from a Command Prompt.
It runs steps [1]–[6], then auto-closes (no "press any key" prompt). The build log is written to
`%TEMP%\savings_analyzer_build.log`.
Output: `build\AustralianSavingsAnalyzer.exe` (~160MB self-contained, no install needed), with
`rates_override.json` copied in beside it.

> **If build.bat appears to "loop"** (re-runs every few seconds and never closes): the script
> itself exits cleanly, so an external tool is relaunching it — almost always a code editor with a
> build/watch task, or the project folder open in an IDE that rebuilds on change. Close the editor
> and run `build.bat` from a plain Command Prompt (Win+R → `cmd`). A built-in throttle also refuses
> to start if it was launched seconds earlier.

**Step 3 (optional) — Build installer**
Install NSIS from https://nsis.sourceforge.io/Download, then run `build.bat installer` to produce the setup. The installer is opt-in and skipped by default.

---

## How It Works

### The Embedded Database
The app contains an embedded database of **40 savings accounts** and **22 term deposit providers** sourced from Canstar, Finder, Savings.com.au, and official bank websites, verified 16 May 2026.

**Account categories:**

| Type | Description | Example |
|---|---|---|
| Honeymoon/Intro | High rate for fixed period, then reverts | Rabobank HISA — 5.90% for 4 months |
| Bonus conditions | High rate when monthly conditions met | ING Maximiser — 5.50% with deposit + transaction |
| No-conditions | Competitive ongoing rate, no strings | Judo Bank — 5.35%, nothing required |
| Age-specific | Products for specific age groups | Westpac Life — 5.50% for 18–34 year olds |
| Term deposits | Fixed rate for locked term | Gateway Bank — 5.60% for 12 months |

### The Scoring Engine
When you click **Analyse**:
1. Filters database by your age, government guarantee preference, and conditions tolerance
2. Calculates effective interest on your balance using verified formulas
3. Scores each account on interest earned, conditions met, balance caps, and usability
4. Returns top 3, ensuring no two from the same banking group

**Financial formulas (all verified):**

| Formula | Calculation |
|---|---|
| Monthly interest | `(balance × rate) ÷ 12` |
| Annual compound | `balance × ((1 + r/12)¹² − 1)` |
| 12-month projection with deposits | `P × (1+r)ⁿ + PMT × ((1+r)ⁿ − 1) / r` |
| Term deposit interest | `principal × rate × (months ÷ 12)` |

---

## Using the App

### Analyse Tab

Fill in your profile and click **Analyse & Get Recommendations**.

| Field | Notes |
|---|---|
| Full Name | Used for personalisation and profile saving |
| Age | Required — some accounts have age eligibility (e.g. 18–34) |
| Investment Amount | Your available lump sum |
| Monthly Deposit | Extra you'll add each month (0 = none) |
| Account Type | Savings Accounts or Term Deposits |
| Term Length | TD only — 3 months to 5 years (grayed out for savings) |
| Conditions Preference | Controls which accounts are shown |
| Government Guaranteed Only | Filters to ADI-protected accounts (recommended) |
| Include Age-Specific Accounts | Shows age-restricted products if you qualify |

**Conditions Preference:**
- **Any** — all accounts including honeymoon rates; best raw return but requires a diary reminder to switch
- **Easy** — excludes accounts with very demanding conditions
- **None** — only accounts where you earn the top rate with zero conditions (e.g. Judo Bank 5.35%)

**Save Profile** — saves current form values for later reload. Profiles also auto-save on every Analyse run.

### Results Tab

Top 3 recommendation cards showing:
- **Rate** — large gold display with base rate underneath
- **Honeymoon badge** — orange warning if the rate is introductory only
- **Metric rows** — Est. Monthly interest, 12-Month Projection, Interest Earned
- **Action Steps** — exactly what to do to open the account and qualify for the top rate
- **Revert reminder** — shows the rate it drops to and when you need to switch
- **Government guarantee** — confirms ADI Financial Claims Scheme status

**Export to Excel** — button (top-right) exports a 4-sheet workbook:
1. Your top 3 analysis with all calculations for your specific scenario
2. Full savings accounts database (all 40 accounts)
3. Term deposit rate matrix (all 22 providers × all 8 term lengths)
4. All 40 accounts ranked by max rate with full conditions

### Savings DB Tab
Filterable table of all 40 savings accounts. Filter by text search, honeymoon only, no-hoops only, or government guaranteed only.

### Term Deposits Tab
Rate matrix showing all 22 TD providers across all 8 term lengths (3, 6, 9, 12, 18, 24, 36, 60 months). Best 12-month rates highlighted in gold.

### Profiles Tab
Saved profiles from previous analyses. Select a row and click **Load & Re-Analyse** to instantly reload all your details. Gold row selection makes it clear which profile is highlighted.

### Settings Tab

**API Key** — optional, for AI commentary (see below).

**Fetch Live Rates** — pulls current savings rates from savings.com.au *and* the current RBA cash rate from rba.gov.au at runtime, then opens a review window comparing savings rates against the built-in database (Bank · Live Rate · Type · DB Rate · Diff · page context), with the detected RBA rate shown in the header. Two actions:
- **Save as Market Snapshot** stores the top intro/ongoing rate so the header and Market Snapshot panel reflect live data on next launch.
- **Apply to Analysis** writes the matched savings rates *and* the RBA cash rate into `rates_override.json` and applies them immediately, so recommendations and the RBA display update without a rebuild.

Detected rates are auto-matched by proximity — eyeball them against the context column before applying; manual `AccountData.vb` / `rates_override.json` editing remains the authoritative fallback.

> The current database rates are always visible on the **Savings DB** and **Term Deposits** tabs, so there's no separate "show rates" button.

---

## Current Market Rates (16 May 2026)

| Metric | Rate | Provider |
|---|---|---|
| Best Honeymoon | **5.90% p.a.** | Rabobank HISA — 4 months (ING Accelerator 5.85%) |
| Best No-Conditions | **5.35% p.a.** | Judo Bank Personal Savings |
| Best Ongoing Bonus | **5.50% p.a.** | ING Maximiser, Westpac Life (18–34), MOVE, Suncorp |
| Best TD (12 months) | **5.60% p.a.** | Gateway Bank + Bank Australia |
| Best TD (5 years) | **5.70% p.a.** | Rabobank ($500k–$2m) |
| RBA Cash Rate | **4.35% p.a.** | Held 16 Jun 2026 — next meeting 11 Aug 2026 |

---

## Light / Dark Theme
Click the **☀/✱** button (top-right header) to toggle themes. Both themes fully supported with gold selection highlighting in grids.

---

## AI Fund Manager (Optional)

> The AI commentary is optional. **All calculations and rankings run locally** — AI only adds written commentary. You can pick from several providers, including **free** ones.

### Supported providers
Choose in **Settings → Provider**:

| Provider | Cost | Notes |
|---|---|---|
| Anthropic (Claude) | Paid | Native API (`sk-ant-` key). Not included with Claude Pro — separate billing at console.anthropic.com. |
| xAI (Grok) | Paid (free credits sometimes) | OpenAI-compatible, `api.x.ai`. |
| **Groq** | **Free tier** | OpenAI-compatible, fast inference, `api.groq.com`. |
| **Google Gemini** | **Free tier** | OpenAI-compatible mode, `generativelanguage.googleapis.com`. |
| OpenAI | Paid | `api.openai.com`. |
| Custom | — | Any OpenAI-compatible endpoint (e.g. local Ollama). |

Anthropic uses its native `/v1/messages` API; every other provider uses the OpenAI-compatible
`/chat/completions` shape with a Bearer key. Provider, model, and base URL are saved in `.config.json`.

### What AI adds
Personalised overview (by name), detailed "why it fits", monthly action steps, profile-specific
cautions, a strategy tip, and a tax note. The AI does **not** change the numbers.

### Setup
1. Pick a provider in **Settings → Provider** (the base URL and a default model auto-fill).
2. Create a key with that provider (Groq and Gemini have free tiers).
3. Paste the key → **Save** → **Test** to verify. Adjust the **Model** field if needed — vendor
   model names change over time.

### Cost
Anthropic `claude-sonnet-4-6`: ~US$0.005–0.01 per analysis. Groq/Gemini free tiers: $0 within limits.

---

## Updating Rates Without Rebuilding (rates_override.json)

The app reads **`rates_override.json`** (beside the exe) at startup and overlays it on the embedded
database — so you can refresh rates without recompiling.

- Keyed by account Id (see `AccountData.vb`); all fields optional. Rates are decimals: `0.0575` = 5.75%.
- An optional **`rba`** section sets the cash rate and meeting info: `rate` (decimal), `asOf`, `nextMeeting`, `note`, `environmentDate`. The cash rate can also be filled by the live fetch; the meeting schedule is maintained here by hand.
- Edit the file and relaunch; delete it to fall back to embedded rates. Status shows in **About**.
- **Settings → Fetch Live Rates → Apply to Analysis** writes matched savings rates *and* the RBA cash rate into this file for you.
- `build.bat` copies the project-root `rates_override.json` into `build\` on each build, so the
  shipped build carries the latest rates.

---

## Data Sources & Accuracy

Rates sourced from: Canstar, Finder, Savings.com.au, Money.com.au, rba.gov.au, official bank websites.
**Embedded verification date: 16 May 2026** · current rates ride in `rates_override.json` (24 June 2026).

Note: Australian bank and comparison sites block default HTTP clients (they reject requests with no
browser User-Agent). The in-app live fetch sends browser headers to work around this, but parsed
rates are auto-detected and should be reviewed before saving. Always verify with your bank before committing.

**Government guarantee:** Australia's FCS (APRA) protects deposits up to **$250,000 per person per ADI**. BOQ, ME Bank, Virgin Money Australia, and BOQ Specialist are all the same ADI for FCS purposes.

---

## Disclaimer
Educational and informational purposes only. Not financial advice. Consult a qualified professional before making investment decisions. Rates variable — verify with your bank.

---

## Technical Details

| Item | Detail |
|---|---|
| Framework | .NET 8 WinForms, VB.NET, self-contained win-x64 |
| Config | `.config.json` beside the exe (profiles + API key) |
| Dependencies | None — no NuGet packages |
| AI model | `claude-sonnet-4-6` (Claude Sonnet 4.6) |
| XLSX export | Native Open XML writer — no Excel required |
| Icon | Embedded base64 — works in all publish modes |

---

## Version History

| Version | Date | Changes |
|---|---|---|
| **3.2.9** | 24 June 2026 | RBA cash rate now live-fetched (rba.gov.au) and runtime-overridable via the `rba` section of `rates_override.json` — Apply to Analysis persists it and refreshes the header, Market Snapshot, and About instantly; the rate also feeds the AI prompt. Removed the redundant "Show Database Rates" button (the Savings DB / Term Deposits tabs already list every rate). |
| **3.2.8** | 17 June 2026 | Tier 1.5: `rates_override.json` overlay — update rates at runtime without rebuilding (loaded at startup, copied into build by build.bat, today's rates shipped). "Apply to Analysis" button writes live-fetched rates into the override so recommendations use them. Multi-provider AI: Anthropic, xAI Grok, Groq (free), Google Gemini (free), OpenAI, or custom OpenAI-compatible endpoint. Fixed title-bar version (now from a single `APP_VERSION` constant). build.bat: log moved to `%TEMP%`, no pause / auto-close, re-entry throttle, opt-in installer. |
| **3.2.7** | 17 June 2026 | Live Rate Fetch (Tier 1): new "Fetch Live Rates" button pulls current rates from savings.com.au at runtime with a review-before-save dialog and persistent market snapshot — no rebuild needed. Fixed the savings.com.au 403 (default HttpClient had no browser User-Agent). Parser takes the highest rate per bank row (was grabbing base rate) and matches bank names at word boundaries. build.bat rewritten (single exit, ASCII-clean). Database unchanged at v3.2.6. |
| **3.2.6** | 16 May 2026 | Full database rebuild: Rabobank HISA 5.90% (was 5.65%), ING Accelerator 5.85% (was 5.65%), uBank Save 5.85% (was 5.60%), all 22 TDs updated post-RBA. Gateway Bank + Bank Australia now lead at 5.60% 12mo. Judo Bank confirmed 5.35% no conditions. Market snapshot updated. |
| 3.2.5 | 15 May 2026 | 4 new savings accounts added (Heartland MySavings, AMP GO Save, Easy Street Flex Saver, St George Maxi Saver). Rate check button renamed to "Show Database Rates". Honest description of what the button does. |
| 3.2.4 | 5 May 2026 | Profile save fixed (ProfileDto). Profiles grid dock order fixed. Export button gold tag. Rate label spacing. Gold grid row selection. RBA updated to 4.35%. Scraped rates persist. |
| 3.2.3 | 3 May 2026 | ConfigManager rewritten. Market snapshot updates after rate check. Config path fix. |
| 3.2.2 | 2 May 2026 | Icon embedded. Light/dark theme for results cards. Config path persistence fix. |
| 3.2.1 | 2 May 2026 | 8 VB.NET compiler errors fixed. |
| 3.2.0 | 2 May 2026 | Initial VB.NET WinForms release. 6-tab UI, 37 accounts, 22 TDs, Excel export, profiles, themes. |

---
*Australian Savings Account Analyzer — Desktop Edition*
*Educational use only · Not financial advice · Rates variable · Verify with your bank*
