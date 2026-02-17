# Sample Size Calculation Guide

## Table of Contents
1. [Core Concepts](#core-concepts)
2. [Key Components](#key-components)
3. [Formulas by Outcome Type](#formulas-by-outcome-type)
4. [Adjustments and Corrections](#adjustments-and-corrections)
5. [Practical Examples](#practical-examples)
6. [Software Tools](#software-tools)
7. [Common Mistakes](#common-mistakes)
8. [Quick Reference Tables](#quick-reference-tables)

---

## Core Concepts

### Why Sample Size Matters

**Too Small:**
- Insufficient power to detect true effects (Type II error)
- Waste of resources and participant time
- Ethical concerns

**Too Large:**
- Unnecessarily expensive
- Delays completion
- Exposes more people to risks than needed

**Just Right:**
- Adequate power to answer research question
- Efficient use of resources
- Ethically justified

---

## Key Components

Every sample size calculation requires:

### 1. Effect Size (δ or Δ)
The magnitude of difference you want to detect

**Sources:**
- Clinical importance (what would change practice?)
- Previous studies
- Minimal clinically important difference (MCID)

### 2. Variability (σ or SD)
How much outcomes vary in the population

**For continuous outcomes:** Standard deviation
**For binary outcomes:** p(1-p)
**For time-to-event:** Event rates

### 3. Significance Level (α)
Probability of Type I error (false positive)

**Standard:** α = 0.05 (two-sided)
**More stringent:** α = 0.01

### 4. Power (1 - β)
Probability of detecting an effect if it exists

**Minimum:** 80% (β = 0.20)
**Preferred:** 90% (β = 0.10)
**Very high:** 95% (β = 0.05)

### 5. Study Design
- Parallel groups
- Crossover
- Cluster randomized
- Non-inferiority

### 6. Allocation Ratio
- Usually 1:1 (equal groups)
- Sometimes unequal (e.g., 2:1)

---

## Formulas by Outcome Type

### 1. Continuous Outcomes

**Comparing Two Independent Groups:**

```
n = 2 × (Z_α/2 + Z_β)² × σ² / δ²
```

**Where:**
- n = sample size per group
- Z_α/2 = 1.96 for α=0.05 (two-sided)
- Z_β = 0.84 for 80% power, 1.28 for 90% power
- σ = standard deviation
- δ = effect size (mean difference)

**Simplified formulas:**
- For α=0.05, power=80%: `n = 16σ² / δ²`
- For α=0.05, power=90%: `n = 21σ² / δ²`

**Example:**
```
Goal: Detect 10 mmHg difference in systolic BP
SD = 15 mmHg
α = 0.05, Power = 80%

n = 2 × (1.96 + 0.84)² × 15² / 10²
n = 2 × 7.84 × 225 / 100
n = 35.3 per group
→ Need 36 per group, 72 total
```

---

### 2. Binary/Proportion Outcomes

**Comparing Two Proportions:**

```
n = [Z_α/2√(2p̄(1-p̄)) + Z_β√(p₁(1-p₁) + p₂(1-p₂))]² / (p₁ - p₂)²
```

**Where:**
- p₁ = proportion in group 1
- p₂ = proportion in group 2
- p̄ = (p₁ + p₂) / 2

**Simplified (equal groups, α=0.05, power=80%):**
```
n ≈ 16p̄(1-p̄) / (p₁-p₂)²
```

**Example:**
```
Control: 30% response rate (p₂ = 0.30)
Treatment: 50% response rate (p₁ = 0.50)
α = 0.05, Power = 80%

p̄ = 0.40

n = [1.96√(0.48) + 0.84√(0.46)]² / 0.04
n = 92.9 per group
→ Need 93 per group, 186 total
```

---

### 3. Survival/Time-to-Event Outcomes

**Log-rank Test (Schoenfeld, 1981):**

```
Number of events (D) = 4(Z_α/2 + Z_β)² / (log(HR))²

Sample size (n) = D / (event rate)
```

**Where:**
- HR = hazard ratio
- D = required number of events
- Event rate = proportion expected to have event

**Example:**
```
Target HR = 0.75 (25% reduction)
α = 0.05, Power = 80%
Expected 2-year event rate = 40%

D = 4(1.96 + 0.84)² / (log(0.75))²
D = 378 events

n = 378 / 0.40 = 945 per group
→ Need 950 per group, 1,900 total
```

---

### 4. Non-Inferiority Trials

**For Proportions:**

```
n = [Z_α + Z_β]² × [p₁(1-p₁) + p₂(1-p₂)] / (p₁ - p₂ - Δ)²
```

**Where:**
- Δ = non-inferiority margin (allowable difference)
- Use Z_α (one-sided), not Z_α/2

**Example:**
```
Standard: 80% success (p₂ = 0.80)
New treatment: 80% expected (p₁ = 0.80)
Non-inferiority margin: Δ = 0.10
α = 0.025 (one-sided), Power = 80%

n = [1.96 + 0.84]² × 0.32 / 0.01
n = 251 per group
```

---

## Adjustments and Corrections

### 1. Dropout/Loss to Follow-up

```
n_adjusted = n / (1 - dropout_rate)
```

**Example:**
```
Initial n = 100 per group
Expected dropout = 20%

n_adjusted = 100 / 0.80 = 125 per group
```

---

### 2. Unequal Allocation Ratio

For allocation ratio r:1 (e.g., 2:1)

```
n₁ = n × r(1+r) / (1+r)²  (treatment group)
n₂ = n × (1+r) / (1+r)²   (control group)
```

**Example:**
```
Equal allocation needs n=100 per group
Want 2:1 ratio

n_treatment = 100 × 2(3) / 4 = 150
n_control = 100 × 3 / 4 = 75
Total = 225 (vs. 200 for 1:1)
```

---

### 3. Cluster Randomization

**Design Effect:**

```
n_cluster = n_individual × [1 + (m-1) × ICC]
```

**Where:**
- m = average cluster size
- ICC = intraclass correlation coefficient

**Example:**
```
Individual randomization needs n=200
Cluster size m=20 patients/clinic
ICC=0.05

Design effect = 1 + (19)×0.05 = 1.95
n_cluster = 200 × 1.95 = 390 patients
Number of clusters = 390/20 = 20 clinics
```

---

### 4. Multiple Comparisons

**Bonferroni correction:**

```
α_adjusted = α / number of comparisons
```

**Example:**
```
Testing 3 primary outcomes
Overall α=0.05

α_adjusted = 0.05 / 3 = 0.0167 per test
Use Z = 2.39 instead of 1.96
→ Increases sample size ~20%
```

---

## Practical Examples

### Example 1: RCT for Depression Treatment

**Study:** New drug vs. placebo

**Parameters:**
- Primary outcome: Change in depression score (continuous)
- Expected treatment effect: 5-point reduction
- SD: 12 points
- Significance: α = 0.05 (two-sided)
- Power: 90%
- Expected dropout: 15%

**Calculation:**

```
Step 1: Basic sample size
n = 21 × σ² / δ²  (formula for 90% power)
n = 21 × 144 / 25
n = 121 per group

Step 2: Adjust for dropout
n_adjusted = 121 / (1-0.15)
n_adjusted = 142 per group

Step 3: Final
Total: 284 patients (142 per group)
```

---

### Example 2: Cancer Response Rate Trial

**Study:** New chemotherapy vs. standard

**Parameters:**
- Outcome: Complete response (binary)
- Control response rate: 25%
- Treatment response rate: 45%
- α = 0.05, Power = 80%
- Expected dropout: 10%

**Calculation:**

```
Step 1: Basic sample size
p̄ = (0.45 + 0.25) / 2 = 0.35
n ≈ 16 × 0.35 × 0.65 / (0.20)²
n = 91 per group

Step 2: Adjust for dropout
n_adjusted = 91 / 0.90
n_adjusted = 101 per group

Step 3: Final
Total: 202 patients (101 per group)
```

---

### Example 3: Survival Trial

**Study:** New cancer therapy vs. standard

**Parameters:**
- Outcome: Overall survival (time-to-event)
- Target HR: 0.70 (30% reduction in hazard)
- Expected 3-year mortality: 50%
- α = 0.05, Power = 90%
- Expected dropout: 5%

**Calculation:**

```
Step 1: Calculate events needed
D = 4(1.96 + 1.28)² / (log(0.70))²
D = 4 × 10.50 / 0.127
D = 331 events

Step 2: Calculate sample size
n = 331 / 0.50 = 662 per group

Step 3: Adjust for dropout
n_adjusted = 662 / 0.95
n_adjusted = 697 per group

Step 4: Final
Total: 1,394 patients (697 per group)
```

---

## Software Tools

### Statistical Software

**Dedicated Sample Size Software:**
- PASS (Power Analysis and Sample Size)
- nQuery
- G*Power (free)
- PS Power and Sample Size (free)

**R Packages:**

```r
# For proportions
library(pwr)
pwr.2p.test(h = ES.h(p1=0.5, p2=0.3), 
            sig.level = 0.05, 
            power = 0.8)

# For means
pwr.t.test(d = 0.5,  # effect size
           sig.level = 0.05,
           power = 0.8,
           type = "two.sample")

# For survival
library(powerSurvEpi)
ssizeCT.default(power = 0.8,
                k = 1,
                pE = 0.4,
                pC = 0.5,
                RR = 0.8)
```

**Stata:**

```stata
power twoproportions 0.30 0.50, power(0.80) alpha(0.05)

power twomeans 100 110, sd(15) power(0.80) alpha(0.05)

power cox, hratio(0.75) eventprob(0.4) power(0.80)
```

**Online Calculators:**
- ClinCalc.com
- Sealed Envelope
- OpenEpi
- UCSF Sample Size Calculators

---

## Common Mistakes

### 1. Using Wrong Variability Estimate
❌ **Wrong:** Using SEM (standard error of mean)
✅ **Correct:** Use SD (standard deviation)

### 2. Forgetting Dropouts
❌ **Wrong:** Calculate n and stop
✅ **Correct:** Always inflate for expected attrition

### 3. Multiple Testing Without Adjustment
❌ **Wrong:** Test 5 outcomes at α=0.05 each
✅ **Correct:** Adjust α or clearly designate single primary outcome

### 4. Unrealistic Effect Sizes
❌ **Wrong:** Assuming huge effects to get smaller n
✅ **Correct:** Use clinically meaningful and realistic effects

### 5. Ignoring Clustering
❌ **Wrong:** Use individual sample size for cluster trial
✅ **Correct:** Apply design effect for clustering

### 6. Wrong Test Choice
❌ **Wrong:** One-sided when two-sided appropriate
✅ **Correct:** Use two-sided unless strong justification

### 7. Post-hoc Power Calculations
❌ **Wrong:** Calculate power after study completes
✅ **Correct:** Power is for planning only, not interpretation

---

## Quick Reference Tables

### Z-scores for Common Significance Levels and Power

| α (two-sided) | α (one-sided) | Z_α/2 or Z_α |
|---------------|---------------|--------------|
| 0.10          | 0.05          | 1.645        |
| 0.05          | 0.025         | 1.960        |
| 0.01          | 0.005         | 2.576        |

| Power | β    | Z_β   |
|-------|------|-------|
| 80%   | 0.20 | 0.842 |
| 90%   | 0.10 | 1.282 |
| 95%   | 0.05 | 1.645 |

---

### Quick Formula Reference

| Outcome Type | Quick Formula (α=0.05, 80% power) |
|--------------|-----------------------------------|
| Continuous (two groups) | n = 16σ²/δ² |
| Binary (two proportions) | n ≈ 16p̄(1-p̄)/(p₁-p₂)² |
| Survival (events) | D = 31.4/(log HR)² |

---

### Sample Size Multipliers

**To adjust from 80% to different power levels:**

| Desired Power | Multiply 80% sample size by: |
|---------------|------------------------------|
| 70%           | 0.75                         |
| 85%           | 1.16                         |
| 90%           | 1.31                         |
| 95%           | 1.64                         |

**To adjust from two-sided to one-sided:**

| Original α (two-sided) | One-sided equivalent | Multiply n by: |
|------------------------|----------------------|----------------|
| 0.05                   | 0.025                | 0.80           |
| 0.01                   | 0.005                | 0.82           |

---

## Checklist for Sample Size Calculation

- [ ] Define primary outcome clearly
- [ ] Specify clinically meaningful effect size (with justification)
- [ ] Obtain realistic variability estimate from literature/pilot
- [ ] Choose appropriate significance level (usually 0.05)
- [ ] Select adequate power (≥80%, prefer 90%)
- [ ] Account for study design features (clustering, etc.)
- [ ] Adjust for expected dropout/loss to follow-up
- [ ] Consider multiple testing if relevant
- [ ] Verify feasibility (budget, timeline, recruitment)
- [ ] Document all assumptions
- [ ] Get statistical expert review
- [ ] Include full justification in protocol

---

## Reporting Template

**For Protocol/Paper:**

"Sample size was calculated to detect a [EFFECT SIZE] difference in [OUTCOME] ([JUSTIFICATION FOR EFFECT SIZE]) with [POWER]% power at a [ONE/TWO]-sided significance level of [ALPHA], assuming [VARIABILITY ESTIMATE] based on [SOURCE]. Accounting for an estimated [X]% dropout rate [AND OTHER ADJUSTMENTS], we will randomize [TOTAL N] patients ([N PER GROUP] per group)."

**Example:**

"Sample size was calculated to detect a 5-point difference in depression scores (clinically meaningful difference based on FDA guidance) with 90% power at a two-sided significance level of 0.05, assuming a standard deviation of 12 points based on previous trials in similar populations. Accounting for an estimated 15% dropout rate, we will randomize 284 patients (142 per group)."

---

## Additional Resources

### Key References

- **Textbooks:**
  - Chow SC, Shao J, Wang H. Sample Size Calculations in Clinical Research, 3rd ed.
  - Machin D, et al. Sample Sizes for Clinical, Laboratory and Epidemiology Studies, 4th ed.

- **Papers:**
  - Schulz KF, Grimes DA. Sample size calculations in randomised trials: mandatory and mystical. Lancet. 2005.
  - Bacchetti P. Current sample size conventions: Flaws, harms, and alternatives. BMC Medicine. 2010.

### Guidelines

- ICH E9: Statistical Principles for Clinical Trials
- CONSORT Statement (includes sample size reporting requirements)
- FDA Guidance for Industry: Adaptive Design Clinical Trials

---

## Summary

**Key Principles:**

1. **Sample size is about POWER** - the probability of detecting a real effect
2. **Effect size should be CLINICALLY MEANINGFUL** - not just statistically detectable
3. **Always account for DROPOUTS and design features**
4. **Document ALL ASSUMPTIONS** - transparency is critical
5. **Balance SCIENCE and FEASIBILITY** - ideal vs. achievable

**Remember:** A well-calculated sample size ensures:
- Ethical use of resources and participants
- Adequate probability of answering your research question
- Credible, publishable results
- Regulatory acceptance (for clinical trials)

---

*Last updated: February 2026*
