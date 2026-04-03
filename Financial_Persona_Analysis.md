# Financial Persona Analysis for Overdraft Risk Projection

**Date**: March 2026
**Data Source**: Databricks (`main_prod`)
**Interactive Dashboard**: [zeweizong.github.io/persona-trajectories](https://zeweizong.github.io/persona-trajectories/)

---

## TL;DR

We analyzed ~1.6 million active EarnIn users to understand how their bank balances behave across pay periods. We identified **5 distinct financial personas** — from users who consistently maintain healthy balances to those who regularly overdraft — and built an interactive dashboard showing real user balance trajectories for each persona. Key findings:

- **32.6% of classifiable users are "In the Red"** — they go negative in 3+ pay periods, making them the largest group and the primary audience for an overdraft risk tool.
- **Tightrope Walkers (14.6%) retain the best** at 90.4% month-over-month because they depend on cashouts to stay afloat.
- **The #1 reason users churn is payroll disruption** — 47% of negative-balance users who leave cite payroll issues, with 82% of those being "NotPayrollActive."
- **Users with $500–$2,500 in the bank retain the best** (88.1%), while users with negative balances retain the worst (64.8%).

---

## Why This Analysis

We're designing an **overdraft risk projection tool** — a feature that shows users when they're likely to run out of money in their current pay period. To design it with real users in mind (not hypothetical archetypes), we:

1. Studied how bank balances distribute across our active user base
2. Analyzed cashout behavior and retention patterns by financial health segment
3. Investigated why users churn
4. Defined 5 data-driven financial personas from real user trajectories
5. Visualized actual bank balance timelines per persona to give designers empathetic, grounded user stories

---

## Part 1: User Financial Health Landscape

### Bank Balance at Pay Period Start (August 2025 Cohort)

We looked at users who cashed out at least once in August 2025 and measured their average bank balance at the start of their pay periods across July–August 2025. This gives a stable snapshot of the financial starting point for ~1.37 million users.

| Avg Balance at Pay Period Start | Users | % of Total |
|-------------------------------|------:|-----------:|
| < $0 (overdrafted) | 188,325 | 13.7% |
| $0 to $50 | 329,717 | 24.0% |
| $50 to $500 | 415,395 | 30.3% |
| $500 to $2,500 | 316,354 | 23.1% |
| $2,500+ | 119,702 | 8.7% |

**Nearly 38% of users who cash out start their pay period with less than $50 in the bank.** These users are the most vulnerable to overdraft within the cycle.

### What Happens Within a Pay Period (Users Starting < $50)

For users starting their pay period with under $50, we traced the average balance trajectory day-by-day within completed cycles. The pattern is striking:

- **Day 0–3**: Balance hovers near $0–$30 (residual from previous cycle)
- **Day 4–8**: A paycheck deposit often arrives, briefly lifting balance to $200–$500
- **Day 8–end**: Balance steadily drains back toward $0, often dipping negative before the next paycheck

This "boom-and-bust" pattern within a single pay period is the core problem an overdraft projection tool would address.

---

## Part 2: Retention by Financial Health

### Month-over-Month Retention (August 2025 Cashout Cohort)

Starting with all users who cashed out in August 2025, we tracked what percentage continued cashing out each subsequent month, broken down by their financial health (avg balance at pay period start in July–August):

| Balance Bucket | Aug Users | Sept | Oct | Nov | Dec |
|---------------|----------:|-----:|----:|----:|----:|
| < $0 | 188,326 | 77.4% | 70.5% | 63.8% | 60.0% |
| $0 to $50 | 329,717 | 82.3% | 75.1% | 68.6% | 65.3% |
| $50 to $500 | 415,394 | 85.3% | 78.2% | 72.0% | 68.7% |
| $500 to $2,500 | 316,354 | 88.1% | 82.9% | 77.6% | 75.3% |
| $2,500+ | 119,702 | 81.2% | 74.2% | 67.8% | 65.9% |

**Key observations:**
- **$500–$2,500 users retain the best** (88.1% M1, 75.3% M4) — they have enough buffer to stay engaged without being forced out by financial distress.
- **< $0 users retain the worst** (77.4% M1, 60.0% M4) — they may lose eligibility or become too distressed to engage.
- **$2,500+ users also retain below average** — they have less need for the product, so engagement is lower.
- The decay curve is roughly linear: 5–7pp lost per month across all segments.

### Cashout Intensity Among Retained Users

Among users who *do* continue cashing out, the frequency and amount increase over time:

| Balance Bucket | Avg Cashouts/Month (Sept) | Avg Cashouts/Month (Dec) | Avg Total Cashed Out (Sept) | Avg Total Cashed Out (Dec) |
|---------------|:---:|:---:|:---:|:---:|
| < $0 | 5.0 | 6.0 | $313 | $382 |
| $0 to $50 | 5.8 | 6.7 | $390 | $466 |
| $50 to $500 | 6.4 | 7.5 | $445 | $534 |
| $500 to $2,500 | 5.2 | 6.2 | $397 | $474 |
| $2,500+ | 3.7 | 4.3 | $333 | $397 |

Users who stick around tend to rely on the product *more* over time, not less.

---

## Part 3: Why Users Churn

We joined the August 2025 cohort against the internal churn attribution table to understand why users who cashed out in August didn't come back in September.

### Churn Reasons for < $0 Balance Users (42,503 churned)

| Churn Reason | Users | % |
|-------------|------:|--:|
| **Payroll issue** | 20,146 | **47.4%** |
| Risk: loss | 5,762 | 13.6% |
| Bank issue | 3,826 | 9.0% |
| User: deleted account | 3,073 | 7.2% |
| User: negative balance | 1,988 | 4.7% |
| Risk: restore failed | 1,815 | 4.3% |
| User: good to go, no competitor | 1,147 | 2.7% |
| User: no login, no competitor | 1,115 | 2.6% |
| Other | ~4,631 | 10.5% |

### Payroll Issue Drill-Down

The 47% "payroll issue" category breaks down as:

| L3 Reason | % of Payroll Churners |
|-----------|----------------------:|
| **NotPayrollActive** | **82.1%** |
| Received paycheck payroll system didn't recognize | 7.9% |
| Risk: no paycheck | 6.1% |
| Not payroll setup eligible | 4.0% |

**The #1 churn driver across all balance segments is the system losing its payroll connection** — not users choosing to leave. This is consistent across all balance buckets (82–86% of payroll churners are NotPayrollActive regardless of balance level).

---

## Part 4: Financial Personas

Based on the patterns above, we defined 5 financial personas by analyzing how a user's bank balance behaves across their **last 5 completed pay periods**. These are not hypothetical — each persona was calibrated against real data distributions.

### Persona Definitions

| Persona | Description | Classification Logic (across last 5 completed pay periods) |
|---------|-------------|------|
| **In the Red** | Frequently overdrafted. Balance goes negative for multiple days in most cycles. These users struggle to keep their head above water. | Balance is negative for 2+ days in at least 3 cycles, OR minimum balance dips below -$50 in 3+ cycles. |
| **Tightrope Walkers** | Living paycheck to paycheck. Median daily balance hovers between -$50 and $150. They rely heavily on cashouts to avoid going under. | Median balance is in the -$50 to $150 range in 3+ cycles AND average cashouts per cycle >= 2. |
| **Roller Coasters** | Dramatic balance swings — they receive large deposits then spend them down fast. High volatility but rarely go fully negative. | Balance standard deviation > $300 with dips below $200 in 2+ cycles, AND negative-balance cycles <= 1. |
| **Payday Surfers** | Classic spend-down pattern. Balance peaks after payday (often >$500) then drains steadily toward near-$0 by end of cycle. | Max balance >= $500, min balance < $50, and average balance between $50–$400 in 2+ cycles (and not consistently above $200). |
| **Steady Savers** | Healthy financial cushion. Minimum balance stays above $200 across nearly all cycles. Low volatility, low risk. | Minimum balance >= $200 in at least 4 out of 5 cycles (or 4 out of however many cycles are available). |

### Sizing (Users Who Logged In Last 30 Days, March 2026)

We classified ~1.6 million active users (after excluding users with inactive bank connections or dormant accounts). Used a 1/32 hash sample and extrapolated:

| Persona | Estimated Users | % of Classified |
|---------|----------------:|----------------:|
| **In the Red** | ~509,000 | 32.6% |
| **Tightrope Walkers** | ~228,000 | 14.6% |
| **Roller Coasters** | ~202,000 | 13.0% |
| **Payday Surfers** | ~136,000 | 8.7% |
| **Steady Savers** | ~116,000 | 7.4% |
| Unclassified | ~371,000 | 23.7% |

**Nearly half of our classifiable users (47.2%) are either "In the Red" or "Tightrope Walkers"** — the two groups that would benefit most from an overdraft risk projection tool.

### Pre-Filtering: Who Gets Excluded

Before classifying, we exclude users whose bank data is unreliable:
1. **Dead bank connection** — both `is_upbc_active` and `is_bfpbc_active` are false (no live data feed).
2. **Dormant/stale balance** — the same non-zero balance for 10+ consecutive days (linked account not actively used).

Importantly, we do **not** exclude users who lost their jobs or have no recent paycheck — they may be the ones who need the overdraft tool the most.

---

## Part 5: Persona Retention (January 2026 Cohort)

We classified users based on their January 2026 activity and tracked cashout retention into February and March 2026:

| Persona | Jan Cohort (1/32 sample) | Feb Retention | Mar Retention | Feb→Mar Drop |
|---------|------------------------:|:---:|:---:|:---:|
| **Tightrope Walkers** | 8,084 | **90.4%** | **81.8%** | -8.6pp |
| **Roller Coasters** | 5,880 | 76.4% | 70.9% | -5.5pp |
| **Payday Surfers** | 4,429 | 72.1% | 65.9% | -6.2pp |
| **Steady Savers** | 3,197 | 68.9% | 64.3% | -4.6pp |
| **In the Red** | 18,183 | 64.8% | 60.0% | -4.8pp |

**Key insight**: Tightrope Walkers are the stickiest persona (90.4% M1) — they depend on cashouts to survive each cycle, so they keep coming back. In the Red users retain the worst (64.8% M1) — many lose eligibility or become too financially distressed to engage. Steady Savers have moderate retention because they don't *need* the product as urgently.

---

## Part 6: Real User Balance Trajectories

For each persona, we sampled 5 real users and plotted their daily bank balance across the pay period that best illustrates their persona pattern. These visualizations are interactive on the [live dashboard](https://zeweizong.github.io/persona-trajectories/) and include:

- **Line charts** showing each user's day-by-day balance within a pay period
- **Triangle markers** indicating when and where cashout events occurred
- **Side panel** listing exact cashout dates and amounts per user
- **$0 reference line** to clearly show when users cross into overdraft territory

### What the trajectories look like per persona:

**In the Red**: Balance starts near or below $0 and stays negative for extended stretches. Some users briefly recover after a deposit only to fall back below zero within days.

**Tightrope Walkers**: Balance fluctuates in a narrow band just above and below $0. Frequent small cashouts (often $10–$60) are visible as clusters of triangles, propping the balance up repeatedly throughout the cycle.

**Roller Coasters**: Dramatic spikes when large deposits arrive (sometimes $2,000–$4,000), followed by rapid drawdowns to near $0. The amplitude of the swings is the defining feature.

**Payday Surfers**: A clear paycheck spike early in the cycle followed by a steady, predictable decline. By the end of the period, balance is near $0 or slightly negative.

**Steady Savers**: Balance stays comfortably above $500 (often $1,000+) throughout the entire cycle. Gentle oscillations but no real risk of overdraft.

---

## Data Sources

| Purpose | Table |
|---------|-------|
| Login activity | `main_prod.dbo.logindetails` |
| Bank connection status | `main_prod.bank_dm.fact_user_bank_connection` |
| Daily bank balance | `main_prod.bank_balance.user_bank_balance` |
| Pay period definitions | `main_prod.earnings.earningscycle` |
| Cashout transactions | `main_prod.finance_dm.fact_transactions_all` |
| Churn attribution | `main_prod.churn_analysis_mom_final_attribution_automation_updated` |

---

## Implications for Product Design

1. **The overdraft projection tool has a large addressable audience**: ~735,000 active users (In the Red + Tightrope Walkers) are in acute financial stress and would directly benefit from seeing when they'll run out of money.

2. **Design for the Tightrope Walker first**: They're the most engaged (90% retention), most frequent cashout users (5–7x/month), and most responsive to interventions. Showing them "you'll hit $0 in 3 days" could meaningfully shift their behavior.

3. **In the Red users need the tool but may not act on it**: With the worst retention and deepest financial distress, projections alone may not be enough — consider pairing with actionable recommendations or automatic scheduling.

4. **Roller Coasters and Payday Surfers are natural expansion audiences**: Their predictable spend-down patterns mean projections would be highly accurate for them, even though their need is less urgent.

5. **Steady Savers may still benefit from peace of mind**: Even users with healthy balances worry about money. A projection showing "you're on track" reinforces product value and prevents unnecessary cashouts.

---

## Interactive Dashboard

The full visualization — including charts, retention analysis, and data methodology — is live at:

**[zeweizong.github.io/persona-trajectories](https://zeweizong.github.io/persona-trajectories/)**
