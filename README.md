# 🏛 Australian Savings Account Analyzer
### Desktop Edition · v3.2.4 · VB.NET + WinForms · No browser required

A professional-grade Australian savings account comparison tool that analyses 37+ savings accounts and 22 term deposit providers against your personal financial profile. All calculations run completely offline. No subscription. No ads.

---

## Quick Start

**Step 1 — Install .NET 8 SDK** (one-time, ~220MB, free)
Download the **Windows x64 Installer**: https://dotnet.microsoft.com/download/dotnet/8.0

**Step 2 — Run build.bat**
Double-click `build.bat` in the `SavingsAnalyzer_Build` folder.
Output: `build\AustralianSavingsAnalyzer.exe` (~160MB self-contained, no install needed)

**Step 3 (optional) — Build installer**
Install NSIS from https://nsis.sourceforge.io/Download then re-run `build.bat` to produce `AustralianSavingsAnalyzer_Setup.exe`.

---

## How It Works

### The Embedded Database
The app contains 37 savings accounts and 22 term deposit providers sourced from Canstar, Finder, Savings.com.au, and direct bank websites, verified April 2026.

Each record includes: base rate, bonus/intro rate, max rate, all conditions required, balance caps, minimum deposits, age restrictions, ADI guarantee status, intro period length, revert rate, and detailed notes.

**Account categories:**

| Type | Description | Example |
|---|---|---|
| Honeymoon/Intro | High rate for fixed period, then reverts | Rabobank HISA — 5.65% for 4 months |
| Bonus conditions | High rate when monthly conditions met | ING Maximiser — 5.50% with deposit + transaction |
| No conditions | Competitive ongoing rate, no strings | Judo Bank — 5.35%, nothing required |
| Age-specific | Products for specific age groups | BOQ Future Saver — 5.50% for under-35s |
| Term Deposits | Fixed rate for locked term | Heartland Bank — 5.40% for 12 months |

### Scoring Engine
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
| Age | Required — some accounts have age eligibility (e.g. 18–35) |
| Investment Amount | Your available lump sum |
| Monthly Deposit | Extra you'll add each month (0 = none) |
| Account Type | Savings Accounts or Term Deposits |
| Term Length | TD only — 3 months to 5 years (grayed out for savings) |
| Conditions Preference | Controls which accounts are shown |
| Government Guaranteed Only | Filters to ADI-protected accounts (recommended) |
| Include Age-Specific Accounts | Shows age-restricted products if you qualify |

**Conditions Preference:**
- **Any** — all accounts including honeymoon rates; best raw return but requires a diary reminder to switch
- **Easy** — excludes accounts with very demanding conditions; ongoing accounts with simple monthly requirements
- **None** — only accounts where you earn the top rate with zero conditions (e.g. Judo Bank 5.35%)

**Save Profile** — saves current form values for later reload. Profiles also auto-save on every Analyse run.

### Results Tab

Top 3 recommendation cards showing:
- **Rate** — large gold display with base rate underneath
- **Honeymoon badge** — orange warning if the rate is introductory only
- **Metric rows** — Est. Monthly interest, 12-Month Projection, Interest Earned
- **Why it fits** — explanation of why this account suits your profile
- **Action Steps** — exactly what to do to open the account and qualify for the top rate
- **Revert reminder** — shows the rate it drops to and when you need to switch
- **Government guarantee** — confirms ADI Financial Claims Scheme status

**Export to Excel** — button (top-right) exports a 4-sheet workbook:
1. Your top 3 analysis with all calculations for your specific scenario
2. Full savings accounts database (all 37 accounts)
3. Term deposit rate matrix (all 22 providers × all 8 term lengths)
4. All 37 accounts ranked by max rate with full conditions

### Savings DB Tab

Filterable table of all 37 savings accounts. Filter by text search, honeymoon only, no-hoops only, or government guaranteed only.

### Term Deposits Tab

Rate matrix showing all 22 TD providers across all 8 term lengths (3, 6, 9, 12, 18, 24, 36, 60 months). Best 12-month rates highlighted in gold.

### Profiles Tab

Saved profiles from previous analyses. Select a row and click **Load & Re-Analyse** to reload all your details and run the analysis instantly. Useful for comparing scenarios: different amounts, savings vs TD, conditions preferences.

### Settings Tab

**API Key** — optional, for AI commentary (see below).

**Live Rate Scraper** — fetches current rates from savings.com.au and canstar.com.au with no API key. After a successful scrape:
- Header banner turns green with today's date
- Market Snapshot panel updates with scraped rates and timestamps
- Always verify the scraped rates directly with the bank before acting

**Config file location** — shown in the log box. This is where your profiles and API key are stored: beside the .exe file.

---

## Market Snapshot

The right panel of the Analyse tab shows the current rate environment (updates after scraping):

| Metric | Rate (April 2026) | Provider |
|---|---|---|
| Best Honeymoon | 5.65% p.a. | Rabobank HISA + ING Accelerator (4 months) |
| Best No-Conditions | 5.35% p.a. | Judo Bank Personal Savings |
| Best Bonus | 5.50% p.a. | ING Maximiser + BOQ Future Saver |
| Best TD (12 months) | 5.40% p.a. | Heartland Bank |
| Best TD (5 years) | 5.70% p.a. | Rabobank ($500k–$2m) |
| RBA Cash Rate | 4.10% p.a. | Effective 18 Mar 2026 |

---

## Light / Dark Theme

Click the **☀/✱** button (top-right header) to toggle themes. Dark (navy + gold, default) and Light (blue-grey + navy + gold) both fully supported. Theme applies to all tabs and re-renders results cards.

---

## AI Fund Manager (Optional)

> ⚠️ **Requires a separate paid Anthropic API account — NOT included with Claude Pro.**
> Claude Pro = access to claude.ai (this chat). The API is a separate developer product with separate billing at console.anthropic.com.

### What AI adds to each analysis
Without AI the app gives fully accurate calculations and ranked recommendations.
With AI each recommendation card also includes:

- **Personalised overview** — addresses you by name, contextualises your situation
- **Detailed "Why it fits"** — reasoning specific to your age, goal, deposit habits
- **Personalised action steps** — specific monthly steps, not generic instructions
- **Cautions** — warnings relevant to your specific profile
- **Strategy tip** — broader wealth observation (TD laddering, split strategy, etc.)
- **Tax note** — reminder about marginal tax on interest income

The AI does NOT change the numbers — calculations and rankings are always done locally.

### Setup
1. Go to **console.anthropic.com/settings/billing** — add credits (US$5 covers 500+ analyses)
2. Go to **console.anthropic.com/settings/keys** — create a key (starts `sk-ant-`)
3. In app: **Settings** → paste key → **Save Key** → **Test** to verify

### Cost
~US$0.005–0.01 per full AI analysis (uses `claude-sonnet-4-6`). US$5 credit = hundreds of analyses.

---

## Data & Accuracy

Rates sourced from Canstar, Finder, Savings.com.au, and direct bank websites. Verified: **April 2026**.

Rates are variable — always verify directly with the bank before making decisions.

**Government guarantee:** Australia's Financial Claims Scheme (APRA/FCS) protects deposits up to **$250,000 per person per ADI**. For balances over $250k, split across multiple ADIs. Note: BOQ, ME Bank, Virgin Money Australia, and BOQ Specialist are all the same ADI.

---

## Disclaimer

For educational and informational purposes only. Not financial advice. The developer is not a licensed financial adviser. Consult a qualified professional before making investment decisions. Rates variable — verify with your bank.

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
| **3.2.4** | 3 May 2026 | Fixed profiles rendering (dock order: grid first). Fixed profile save (`ProfileDto` with `Double` fields bypasses VB.NET `Decimal`/`System.Text.Json` serialization bug). Profile count in heading. Market snapshot updates to scrape date/time. Comprehensive README. |
| 3.2.3 | 3 May 2026 | `ConfigManager` rewritten as typed class. API payload uses explicit JSON string. `Environment.ProcessPath` for config path. Market snapshot rows update live after scrape. |
| 3.2.2 | 2 May 2026 | Icon embedded as base64. Light/dark theme fully applied to results cards. `ResolveConfigPath` avoids temp-dir config loss in single-file publish. |
| 3.2.1 | 2 May 2026 | 8 VB.NET compiler errors fixed: `Partial Class`, `??` operator, `step` keyword, wrong Json namespace, missing imports, Theme method names, single-line `Get`, chained `ElseIf`. |
| 3.2.0 | 2 May 2026 | Initial VB.NET WinForms release. 6-tab UI, 37 accounts, 22 TDs, web scraper, Excel export, profiles, navy/gold theme, NSIS installer. |

---

*Australian Savings Account Analyzer — Desktop Edition*
*Educational use only · Not financial advice · Rates variable · Verify with your bank*
