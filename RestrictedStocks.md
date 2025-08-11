#  Qualified Restricted Stocks Taxes in France

**Version : 21 July 2025**  
**Scope : French tax residents**  

This memo summarises how the **acquisition gain** ("gain d’acquisition", Gain d'Acquisition) and the **capital gain** (Plus Value de Cession) of restricted stocks (*attributions gratuites d’actions* – AGA) are taxed in France, depending on the french subplan of your company that authorised the plan. In this documentation, four successive regimes are commonly referred to the name of the government that introduced them: **“Hollande”**, **“Macron 1”**, **“Macron 2”**, and **“Macron 3”**.

> ⚠️ **Official source**: The synthesis below is based primarily on the French tax administration’s FAQ *“Mon entreprise m’a attribué des actions gratuites, comment sera imposé le gain d’acquisition ?”* ([impots.gouv.fr](https://www.impots.gouv.fr/particulier/questions/mon-entreprise-ma-attribue-des-actions-gratuites-comment-sera-impose-le-gain)).

---

## 1. Key concepts & timeline

### 1.1 Two separate gains

- **Acquisition gain (Gain d'Acquisition)** – the value of the shares on the **definitive vesting date** (end of the vesting period), net of any symbolic price paid by the employee. The tax treatment of the GA depends on the date specified in the french subplan of your company (see regime tables below).
- **Capital gain (Plus Value de Cession)** – the difference between the **sale price** and the **value already taxed as GA**. PVC follows the ordinary rules for capital gains on securities (PFU by default or the progressive scale upon election, with possible holding‑period allowances).

### 1.2 Dates that matter

The decisive criterion is the date of the Assemblée Générale Extraordinaire (**AGE**) wrote in the **french subplan** of your company, not the delivery date of the shares, not the year's AGE date corresponding to the shares' delivery date. Each reform covers a specific date range.

---

## 2. Overview

| Regime (nickname)      | AGE authorisation date | Income‑tax treatment of the acquisition gain | Social contributions on GA | 10 % employee contribution | Key features |
|------------------------|------------------------|----------------------------------------------|--------------------------|-------------------|--------------|
| **Hollande**           | 28 Sep 2012 – 07 Aug 2015 | Progressive income-tax scale | 17.2 % (investment income) *[see note]* | **Yes** | Least favourable; GA declared as salary. |
| **Macron 1**           | 08 Aug 2015 – 30 Dec 2016 | Progressive income‑tax scale with holding‑period discount | 17.2 % | **No** | Holding-period discount without the 300k threshold |
| **Macron 2**           | 31 Dec 2016 – 31 Dec 2017 | GA ≤ €300 k: Progressive income‑tax scale with holding‑period discount; GA > €300 k: Progressive income-tax scale (no discount) | GA ≤ €300 k: 17.2 %; > €300 k: 9.7 % | GA > €300 k: **Yes** | €300 k threshold splits the tax treatment. |
| **Macron 3**   | 01 Jan 2018 onwards     | GA ≤ €300 k: Progressive income-tax scale **with** 50% discount; GA > €300 k: Progressive income-tax scale without 50% discount| GA ≤ €300 k: 17.2 %; > €300 k: 9.7 % | GA > €300 k: **Yes** | Flat 50 % discount replaces holding‑period discount. |

*Detailed notes, caveats and sources follow in each regime section.*

Holding‑period discount (counted from **vesting** to **sale**):
  - Holding < 2 years: no discount
  - Holding between 2 and 8 years: 50 % discount on the acquisition gain
  - Holding > 8 years: 65 % discount

---

## 3. Capital Gain

Whatever regime applies to the GA, the **PVC** (sale price – GA value) follows the general rules for securities gains: Flat tax (PFU) 30 % 

---

This synthesis does not replace personal tax advice. For significant amounts (notably GA above €300 000), have the calculations validated by a professional (chartered accountant or tax lawyer).

---

## 4. Examples

You can simulate those examples using [Official French Taxes Simulator](https://simulateur-ir-ifi.impots.gouv.fr/calcul_impot/2025/complet/index.htm)

### Pre-requisites: income taxes in Python

Compute the income taxes for 2025 year.

```python
import sys
def incomeTaxes(current_sum, gain_acq_imposable):
    tranches = [(0, 0, 11497), (0.11, 11498, 29315), (0.30, 29316, 83823), (0.41, 83824, 180294), (0.45, 180295, sys.maxsize)]
    to_process = gain_acq_imposable
    impot = 0
    for k in tranches:
        (k, minval, maxval) = k
        a_imposer = 0
        if current_sum <= maxval:
            if (current_sum + to_process) <= maxval:
                a_imposer = to_process
            else:
                a_imposer = maxval-current_sum
            to_process = to_process - a_imposer
            impot = impot + (a_imposer * k)
            current_sum += a_imposer
    return impot
```

### 4.1 Employee / Single / Hollande stocks

An employee has an annual salary (**1AJ**) of **90000 euros**, sold **Hollande** "28 Sep 2012 – 07 Aug 2015" stocks with **acqusition gains** of **100000 euros** (**1TT**) and **capital gains** of **50000 euros** (**3VG**).

#### Reference Fiscal Revenue / Revenu Fiscal de Reference 

```python
var_1AJ = 90_000
var_1BJ = 0
var_1TT = 100_000
var_1UT = 0
var_1TZ = 0
var_1UZ = 0
var_1WZ = 0
var_3VG = 50_000
MAX_10PERCENT_DEDUCTION = 14_426
DEDUCTION1 = min(MAX_10PERCENT_DEDUCTION, (var_1AJ+var_1TT)*0.10)
DEDUCTION2 = min(MAX_10PERCENT_DEDUCTION, (var_1BJ+var_1UT)*0.10)
INCOME = var_1AJ + var_1TT - DEDUCTION1 +  var_1BJ + var_1UT  - DEDUCTION2 + var_1TZ
RFR = INCOME + var_1UZ + var_1WZ + var_3VG # 225574
```

#### Total Taxes

```python
SOCIAL_ACQ_GAINS_TAXES = (var_1TT + var_1UT) * 0.097 + (var_1TZ +  var_1UZ + var_1WZ) * 0.172
EMPLOYEE_CONTRIBUTION_TAXES = (var_1TT + var_1UT) * 0.10
CAPITAL_GAINS_INCOME_TAXES = var_3VG*0.128
CAPITAL_GAINS_SOCIAL_TAXES = var_3VG*0.172
CEHR_TAXES = 0
TAXES = incomeTaxes(0, INCOME) + \
    SOCIAL_ACQ_GAINS_TAXES + \
    EMPLOYEE_CONTRIBUTION_TAXES + \
    CAPITAL_GAINS_INCOME_TAXES + \
    CAPITAL_GAINS_SOCIAL_TAXES + \
    CEHR_TAXES # 90630.29
```

#### Withholding tax rate / Taux de prélèvement à la source (taux foyer)

```python
IR = round(incomeTaxes(0, INCOME))
Rinclus = var_1AJ - round(DEDUCTION1*(var_1AJ)/(var_1AJ+var_1TT))
RNI = Rinclus + var_1TT - round(DEDUCTION1*(var_1TT)/(var_1AJ+var_1TT)) + var_1TZ
Rras = var_1AJ + var_1BJ
Taux_foyer = (IR*(Rinclus/RNI))/Rras # 29.4
```

### 4.2 Employee / Single / Macron 1 stocks

An employee has an annual salary (**1AJ**) of **90000 euros**, sold **Macron 1** "08 Aug 2015 – 30 Dec 2016" stocks two years after vesting with **acqusition gains** of **350000 euros** (**1TZ = 175000**, **1UZ = 175000**)  and **capital gains** of **50000 euros** (**3VG**).

#### Reference Fiscal Revenue / Revenu Fiscal de Reference 

```python
var_1AJ = 90_000
var_1TT = 0
var_1BJ = 0
var_1UT = 0
var_1TZ = 175_000
var_1UZ = 175_000
var_1WZ = 0
var_3VG = 50_000
MAX_10PERCENT_DEDUCTION = 14_426
DEDUCTION1 = min(MAX_10PERCENT_DEDUCTION, (var_1AJ+var_1TT)*0.10)
DEDUCTION2 = min(MAX_10PERCENT_DEDUCTION, (var_1BJ+var_1UT)*0.10)
INCOME = var_1AJ + var_1TT - DEDUCTION1 +  var_1BJ + var_1UT  - DEDUCTION2 + var_1TZ
RFR = INCOME + var_1UZ + var_1WZ + var_3VG # 481000
```

#### Total Taxes

```python
SOCIAL_ACQ_GAINS_TAXES = (var_1TT + var_1UT) * 0.097 + (var_1TZ + var_1UZ + var_1WZ) * 0.172
EMPLOYEE_CONTRIBUTION_TAXES = (var_1TT + var_1UT) * 0.10
CAPITAL_GAINS_INCOME_TAXES = var_3VG*0.128
CAPITAL_GAINS_SOCIAL_TAXES = var_3VG*0.172
CEHR_TAXES = (RFR-250_000)*0.03
TAXES = incomeTaxes(0, INCOME) + \
    SOCIAL_ACQ_GAINS_TAXES + \
    EMPLOYEE_CONTRIBUTION_TAXES + \
    CAPITAL_GAINS_INCOME_TAXES + \
    CAPITAL_GAINS_SOCIAL_TAXES  + \
    CEHR_TAXES # 174063.19
```
#### Withholding tax rate / Taux de prélèvement à la source (taux foyer)

```python
IR = round(incomeTaxes(0, INCOME))
Rinclus = var_1AJ - round(DEDUCTION1*(var_1AJ)/(var_1AJ+var_1TT))
RNI = Rinclus + var_1TT - round(DEDUCTION1*(var_1TT)/(var_1AJ+var_1TT)) + var_1TZ
Rras = var_1AJ + var_1BJ
Taux_foyer = (IR*(Rinclus/RNI))/Rras # 32.3
```

### 4.3 Employee / Married / Macron 3 stocks

An employee has an annual salary (**1AJ**) of **150000 euros**, sold **Macron 3** "01 Jan 2018 onwards" stocks with **acqusition gains** of **350000 euros** (**1TZ = 150000**, **1TT = 50000**, **1WZ = 150000**)  and **capital gains** of **50000 euros** (**3VG**). His wife has a salary of **130000 euros** (**1BJ**).

#### Reference Fiscal Revenue / Revenu Fiscal de Reference 

```python
var_1AJ = 150_000
var_1BJ = 130_000
var_1TT = 50_000
var_1UT = 0
var_1TZ = 150_000
var_1UZ = 0
var_1WZ = 150_000
var_3VG = 50_000
MAX_10PERCENT_DEDUCTION = 14_426
DEDUCTION1 = min(MAX_10PERCENT_DEDUCTION, (var_1AJ+var_1TT)*0.10)
DEDUCTION2 = min(MAX_10PERCENT_DEDUCTION, (var_1BJ+var_1UT)*0.10)
INCOME = var_1AJ + var_1TT - DEDUCTION1 +  var_1BJ + var_1UT  - DEDUCTION2 + var_1TZ
RFR = INCOME + var_1UZ + var_1WZ + var_3VG # 652574.0
```

#### Total Taxes

```python
SOCIAL_ACQ_GAINS_TAXES = (var_1TT + var_1UT) * 0.097 + (var_1TZ + var_1WZ + var_1UZ) * 0.172
EMPLOYEE_CONTRIBUTION_TAXES = (var_1TT + var_1UT) * 0.10
CAPITAL_GAINS_INCOME_TAXES = var_3VG*0.128
CAPITAL_GAINS_SOCIAL_TAXES = var_3VG*0.172
CEHR_TAXES = (RFR-500_000)*0.03
TAXES = incomeTaxes(0, INCOME/2.0) * 2.0 + \
    SOCIAL_ACQ_GAINS_TAXES + \
    EMPLOYEE_CONTRIBUTION_TAXES + \
    CAPITAL_GAINS_INCOME_TAXES + \
    CAPITAL_GAINS_SOCIAL_TAXES + \
    CEHR_TAXES  # 238151.9
```

#### Withholding tax rate / Taux de prélèvement à la source (taux foyer)

```python
IR = round(incomeTaxes(0, INCOME/2)*2)
Rinclus = var_1AJ - round(DEDUCTION1*(var_1AJ)/(var_1AJ+var_1TT)) + var_1BJ \
    - round(DEDUCTION2*(var_1BJ)/(var_1BJ+var_1UT))
RNI = Rinclus + var_1TT - round(DEDUCTION1*(var_1TT)/(var_1AJ+var_1TT)) + var_1UT \
    - round(DEDUCTION2*(var_1UT)/(var_1BJ+var_1UT)) + var_1TZ
Rras = var_1AJ + var_1BJ
Taux_foyer = (IR*(Rinclus/RNI))/Rras # 31.8
```

#### Individualized Withholding tax rate / Taux de prélèvement à la source individualisé 1BJ

```python
INCOME2 = var_1BJ + var_1UT - DEDUCTION2 + var_1TZ/2
IR2=round(incomeTaxes(0,  INCOME2))
Rinclus2 = var_1BJ - round(DEDUCTION2*(var_1BJ)/(var_1BJ+var_1UT))
RNI2 = Rinclus2 + var_1UT - round(DEDUCTION2*(var_1UT)/(var_1BJ+var_1UT)) + var_1TZ/2
Rras2 = var_1BJ + var_1UT # salaries before deduction
IR2*(Rinclus2/RNI2)/(Rras2) # 0.296
```

#### Individualized Withholding tax rate / Taux de prélèvement à la source individualisé 1AJ

```python
(IR*(Rinclus/RNI) - IR2*(Rinclus2/RNI2)/(Rras2) * (var_1BJ + var_1UT)
     -  IR*(Rinclus/RNI)/(Rras)* 0)/(var_1AJ) # 0.336
```
