# 🏛 Australian Savings Account Analyzer
### Desktop Edition · v3.2.6 · VB.NET + WinForms · No browser required

A professional-grade Australian savings account comparison tool that analyses 40 savings accounts and 22 term deposit providers against your personal financial profile. All calculations run completely offline. No subscription. No ads.

**RBA Cash Rate: 4.35% p.a.** (raised 5 May 2026) · Data verified: 16 May 2026

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

**Show Database Rates** — displays the rates built into this version with the verification date, and tests connectivity to savings.com.au. Rates are embedded in the app and updated with each version — this button shows what's in the current build so you can compare against what you see on comparison sites.

---

## Current Market Rates (16 May 2026)

| Metric | Rate | Provider |
|---|---|---|
| Best Honeymoon | **5.90% p.a.** | Rabobank HISA — 4 months (ING Accelerator 5.85%) |
| Best No-Conditions | **5.35% p.a.** | Judo Bank Personal Savings |
| Best Ongoing Bonus | **5.50% p.a.** | ING Maximiser, Westpac Life (18–34), MOVE, Suncorp |
| Best TD (12 months) | **5.60% p.a.** | Gateway Bank + Bank Australia |
| Best TD (5 years) | **5.70% p.a.** | Rabobank ($500k–$2m) |
| RBA Cash Rate | **4.35% p.a.** | Raised 5 May 2026 — next meeting June 2026 |

---

## Light / Dark Theme
Click the **☀/✱** button (top-right header) to toggle themes. Both themes fully supported with gold selection highlighting in grids.

---

## AI Fund Manager (Optional)

> ⚠️ **Requires a separate paid Anthropic API account — NOT included with Claude Pro.**
> Claude Pro gives you access to claude.ai (the chat interface). The API is a separate developer product at console.anthropic.com with its own billing.

### What AI adds
Without AI the app gives fully accurate calculations and ranked recommendations. With AI each card also includes:
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
~US$0.005–0.01 per full AI analysis. Uses `claude-sonnet-4-6`.

---

## Data Sources & Accuracy

Rates sourced from: Canstar, Finder, Savings.com.au, Money.com.au, official bank websites.
**Verification date: 16 May 2026**

Note: Australian bank and comparison sites block automated HTTP scraping. Rates in this app are manually verified from primary sources before each release. Always verify with your bank before committing.

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
