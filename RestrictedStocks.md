#  Qualified Restricted Stocks Taxes in France

**Version : 21 July 2025**  
**Scope : French tax residents**  

This memo summarises how the **acquisition gain** ("gain d’acquisition", Gain d'Acquisition) and the **capital gain** (Plus Value de Cession) of restricted stocks (*attributions gratuites d’actions* – AGA) are taxed in France, depending on the french subplan of your company that authorised the plan. In this documentation, four successive regimes are commonly referred to by the name of the government that introduced them: **“Hollande”**, **“Macron 1”**, **“Macron 2”**, and **“Macron 3”**.

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

### Income taxes in Python

Compute the income taxes for 2025 year.

```python
import sys
def calculImpot(current_sum, gain_acq_imposable):
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

```python
RFR = (90_000+100_000 - 14_426) + 50000 # 225574
MAX_DEDUCTION_FORFAITAIRE_10 = 14_426
SOCIAL_ACQ_GAINS_TAXES = 100_000 * 0.097
EMPLOYEE_CONTRIBUTION = 100_000 * 0.10
FLAT_TAX_CAPITAL_GAINS = 50_000*0.30
TAXES = calculImpot(0, 90_000+100_000 - MAX_DEDUCTION_FORFAITAIRE_10) + SOCIAL_ACQ_GAINS_TAXES + EMPLOYEE_CONTRIBUTION + FLAT_TAX_CAPITAL_GAINS # 90630.29
```
