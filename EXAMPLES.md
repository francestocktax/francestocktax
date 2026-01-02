
# Examples

You can simulate those examples using [Official French Taxes Simulator](https://simulateur-ir-ifi.impots.gouv.fr/calcul_impot/2025/complet/index.htm)

## Pre-requisites: income taxes in Python

Compute the income taxes for 2025 year.

```python
import sys
import math
def roundu(x: float) -> int:
    return math.floor(x + 0.5) if x >= 0 else math.ceil(x - 0.5)
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

Compute the CEHR taxes :

```python
def computeCEHRTaxes(RFR: float, tax_shares: float) -> float:
    taxes = 0.0
    if tax_shares == 1.0:
        if RFR > 250_000.0:
            amount = (500_000.0 - 250_000.0) if RFR > 500_000.0 else (RFR - 250_000.0)
            taxes += amount * 0.03
        if RFR > 500_000.0:
            amount = (1_000_000.0 - 500_000.0) if RFR > 1_000_000.0 else (RFR - 500_000.0)
            taxes += amount * 0.04
        if RFR > 1_000_000.0:
            amount = RFR - 1_000_000.0
            taxes += amount * 0.04
    else:
        if RFR > 500_000.0:
            amount = (1_000_000.0 - 500_000.0) if RFR > 1_000_000.0 else (RFR - 500_000.0)
            taxes += amount * 0.03
        if RFR > 1_000_000.0:
            amount = RFR - 1_000_000.0
            taxes += amount * 0.04
    return roundu(taxes)
```

## 1. Employee / Single / Hollande stocks

An employee has an annual salary (**1AJ**) of **90000 euros**, sold **Hollande** "28 Sep 2012 – 07 Aug 2015" stocks with **acqusition gains** of **100000 euros** (**1TT**) and **capital gains** of **50000 euros** (**3VG**).

### Reference Fiscal Revenue / Revenu Fiscal de Reference 

```python
var_1AJ = 90_000
var_1BJ = 0
var_1TT = 100_000
var_1UT = 0
var_1TZ = 0
var_1UZ = 0
var_1WZ = 0
var_3VG = 50_000
var_6DE = 0
tax_shares = 1.0
MAX_10PERCENT_DEDUCTION = 14_426
MIN_10PERCENT_DEDUCTION = 504
DEDUCTION1 = min(MAX_10PERCENT_DEDUCTION, max(round((var_1AJ + var_1TT) * 0.10), \
    MIN_10PERCENT_DEDUCTION if (var_1AJ + var_1TT) >= MIN_10PERCENT_DEDUCTION else (var_1AJ + var_1TT)))
DEDUCTION2 = min(MAX_10PERCENT_DEDUCTION, max(round((var_1BJ + var_1UT) * 0.10), \
    MIN_10PERCENT_DEDUCTION if (var_1BJ + var_1UT) >= MIN_10PERCENT_DEDUCTION else (var_1BJ + var_1UT)))
INCOME = var_1AJ + var_1TT - DEDUCTION1 +  var_1BJ + var_1UT  - DEDUCTION2 + var_1TZ - var_6DE
RFR = INCOME + var_1UZ + var_1WZ + var_3VG # 225574
```

### Total Taxes

```python
SOCIAL_ACQ_GAINS_TAXES = round((var_1TT + var_1UT) * 0.092) + round((var_1TT + var_1UT) * 0.005) + \
    round((var_1TZ + var_1WZ + var_1UZ) * 0.097) +  round((var_1TZ + var_1WZ + var_1UZ) * 0.075)
EMPLOYEE_CONTRIBUTION_TAXES = round((var_1TT + var_1UT) * 0.10)
CAPITAL_GAINS_INCOME_TAXES = round(var_3VG*0.128)
CAPITAL_GAINS_SOCIAL_TAXES = round((var_3VG*0.097) + (var_3VG*0.075))
CEHR_TAXES = computeCEHRTaxes(RFR, tax_shares)
TAXES = round(incomeTaxes(0, INCOME)) + \
    SOCIAL_ACQ_GAINS_TAXES + \
    EMPLOYEE_CONTRIBUTION_TAXES + \
    CAPITAL_GAINS_INCOME_TAXES + \
    CAPITAL_GAINS_SOCIAL_TAXES + \
    CEHR_TAXES # 90630
```

### Withholding tax rate / Taux de prélèvement à la source (taux foyer)

To know how the tax rates are computed, you can click on the link "Détail du taux" in the last summary page of the [Official French Taxes Simulator](https://simulateur-ir-ifi.impots.gouv.fr/calcul_impot/2025/complet/index.htm)

```python
IR = round(incomeTaxes(0, INCOME))
Rinclus = var_1AJ - round(DEDUCTION1*(var_1AJ)/(var_1AJ+var_1TT))
RNI = Rinclus + var_1TT - round(DEDUCTION1*(var_1TT)/(var_1AJ+var_1TT)) + var_1TZ
Rras = var_1AJ + var_1BJ
Taux_foyer = (IR*(Rinclus/RNI))/Rras # 29.4%
```

## 2. Employee / Single / Macron 1 stocks

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
var_6DE = 0
tax_shares = 1.0
MAX_10PERCENT_DEDUCTION = 14_426
DEDUCTION1 = min(MAX_10PERCENT_DEDUCTION, max(round((var_1AJ + var_1TT) * 0.10), \
    MIN_10PERCENT_DEDUCTION if (var_1AJ + var_1TT) >= MIN_10PERCENT_DEDUCTION else (var_1AJ + var_1TT)))
DEDUCTION2 = min(MAX_10PERCENT_DEDUCTION, max(round((var_1BJ + var_1UT) * 0.10), \
    MIN_10PERCENT_DEDUCTION if (var_1BJ + var_1UT) >= MIN_10PERCENT_DEDUCTION else (var_1BJ + var_1UT)))
INCOME = var_1AJ + var_1TT - DEDUCTION1 +  var_1BJ + var_1UT  - DEDUCTION2 + var_1TZ - var_6DE
RFR = INCOME + var_1UZ + var_1WZ + var_3VG # 481000
```

### Total Taxes

```python
SOCIAL_ACQ_GAINS_TAXES = round((var_1TT + var_1UT) * 0.092) + round((var_1TT + var_1UT) * 0.005) + \
    round((var_1TZ + var_1WZ + var_1UZ) * 0.097) +  round((var_1TZ + var_1WZ + var_1UZ) * 0.075)
EMPLOYEE_CONTRIBUTION_TAXES = round((var_1TT + var_1UT) * 0.10)
CAPITAL_GAINS_INCOME_TAXES = round(var_3VG*0.128)
CAPITAL_GAINS_SOCIAL_TAXES = round((var_3VG*0.097) + (var_3VG*0.075))
CEHR_TAXES = computeCEHRTaxes(RFR, tax_shares)
TAXES = round(incomeTaxes(0, INCOME)) + \
    SOCIAL_ACQ_GAINS_TAXES + \
    EMPLOYEE_CONTRIBUTION_TAXES + \
    CAPITAL_GAINS_INCOME_TAXES + \
    CAPITAL_GAINS_SOCIAL_TAXES  + \
    CEHR_TAXES # 174063
```
### Withholding tax rate / Taux de prélèvement à la source (taux foyer)

```python
IR = round(incomeTaxes(0, INCOME))
Rinclus = var_1AJ - round(DEDUCTION1*(var_1AJ)/(var_1AJ+var_1TT))
RNI = Rinclus + var_1TT - round(DEDUCTION1*(var_1TT)/(var_1AJ+var_1TT)) + var_1TZ
Rras = var_1AJ + var_1BJ
Taux_foyer = (IR*(Rinclus/RNI))/Rras # 32.3%
```

## 3. Employee / Married / Macron 3 stocks

An employee has an annual salary (**1AJ**) of **150000 euros**, sold **Macron 3** "01 Jan 2018 onwards" stocks with **acqusition gains** of **350000 euros** (**1TZ = 150000**, **1TT = 50000**, **1WZ = 150000**)  and **capital gains** of **50000 euros** (**3VG**). His wife has a salary of **130000 euros** (**1BJ**).

### Reference Fiscal Revenue / Revenu Fiscal de Reference

```python
var_1AJ = 150_000
var_1BJ = 130_000
var_1TT = 50_000
var_1UT = 0
var_1TZ = 150_000
var_1UZ = 0
var_1WZ = 150_000
var_3VG = 50_000
var_6DE = 0
tax_shares = 2.0
MAX_10PERCENT_DEDUCTION = 14_426
DEDUCTION1 = min(MAX_10PERCENT_DEDUCTION, max(round((var_1AJ + var_1TT) * 0.10), \
    MIN_10PERCENT_DEDUCTION if (var_1AJ + var_1TT) >= MIN_10PERCENT_DEDUCTION else (var_1AJ + var_1TT)))
DEDUCTION2 = min(MAX_10PERCENT_DEDUCTION, max(round((var_1BJ + var_1UT) * 0.10), \
    MIN_10PERCENT_DEDUCTION if (var_1BJ + var_1UT) >= MIN_10PERCENT_DEDUCTION else (var_1BJ + var_1UT)))
INCOME = var_1AJ + var_1TT - DEDUCTION1 +  var_1BJ + var_1UT  - DEDUCTION2 + var_1TZ - var_6DE
RFR = INCOME + var_1UZ + var_1WZ + var_3VG # 652574.0
```

### Total Taxes

```python
SOCIAL_ACQ_GAINS_TAXES = round((var_1TT + var_1UT) * 0.092) + round((var_1TT + var_1UT) * 0.005) + \
    round((var_1TZ + var_1WZ + var_1UZ) * 0.097) +  round((var_1TZ + var_1WZ + var_1UZ) * 0.075)
EMPLOYEE_CONTRIBUTION_TAXES = round((var_1TT + var_1UT) * 0.10)
CAPITAL_GAINS_INCOME_TAXES = round(var_3VG*0.128)
CAPITAL_GAINS_SOCIAL_TAXES = round((var_3VG*0.097) + (var_3VG*0.075))
CEHR_TAXES = computeCEHRTaxes(RFR, tax_shares)
TAXES = round(incomeTaxes(0, round(INCOME/2.0)) * 2.0) + \
    SOCIAL_ACQ_GAINS_TAXES + \
    EMPLOYEE_CONTRIBUTION_TAXES + \
    CAPITAL_GAINS_INCOME_TAXES + \
    CAPITAL_GAINS_SOCIAL_TAXES + \
    CEHR_TAXES  # 238152
```

### Withholding tax rate / Taux de prélèvement à la source (taux foyer)

```python
IR = round(incomeTaxes(0, INCOME/2)*2)
Rinclus = var_1AJ - round(DEDUCTION1*(var_1AJ)/(var_1AJ+var_1TT)) + var_1BJ \
    - round(DEDUCTION2*(var_1BJ)/(var_1BJ+var_1UT))
RNI = Rinclus + var_1TT - round(DEDUCTION1*(var_1TT)/(var_1AJ+var_1TT)) + var_1UT \
    - round(DEDUCTION2*(var_1UT)/(var_1BJ+var_1UT)) + var_1TZ
Rras = var_1AJ + var_1BJ
Taux_foyer = (IR*(Rinclus/RNI))/Rras # 31.8%
```

### Individualized Withholding tax rate / Taux de prélèvement à la source individualisé 1BJ

We start with 1BJ because var_1AJ + var_1TT > var_1BJ + var_1UT

```python
INCOME2 = var_1BJ + var_1UT - DEDUCTION2 + var_1TZ/2 - var_6DE/2
IR2=round(incomeTaxes(0,  INCOME2))
Rinclus2 = var_1BJ - round(DEDUCTION2*(var_1BJ)/(var_1BJ+var_1UT))
RNI2 = Rinclus2 + var_1UT - round(DEDUCTION2*(var_1UT)/(var_1BJ+var_1UT)) + var_1TZ/2
Rras2 = var_1BJ # salaries before deduction
IR2*(Rinclus2/RNI2)/(Rras2) # 29.6%
```

### Individualized Withholding tax rate / Taux de prélèvement à la source individualisé 1AJ

```python
(IR*(Rinclus/RNI) - IR2*(Rinclus2/RNI2)/(Rras2) * (var_1BJ)
     -  IR*(Rinclus/RNI)/(Rras)* 0)/(var_1AJ) # 33.6%
```

## 4. Employee / Married / Macron 1 stocks

An employee has an annual salary (**1AJ**) of **90000 euros**. His wife has a salary of **140000 euros** (**1BJ**) sold **Macron 1** stocks with **acqusition gains** of **340000 euros** (**1TZ = 150000**, **1UT = 40000**, **1UZ = 150000**)  and **capital gains** of **60000 euros** (**3VG**)

### Reference Fiscal Revenue / Revenu Fiscal de Reference 

```python
var_1AJ = 90_000
var_1BJ = 140_000
var_1TT = 0_000
var_1UT = 40_000
var_1TZ = 150_000
var_1UZ = 150_000
var_1WZ = 0
var_3VG = 60_000
var_6DE = 0
tax_shares = 2.0
MAX_10PERCENT_DEDUCTION = 14_426
DEDUCTION1 = min(MAX_10PERCENT_DEDUCTION, max(round((var_1AJ + var_1TT) * 0.10), \
    MIN_10PERCENT_DEDUCTION if (var_1AJ + var_1TT) >= MIN_10PERCENT_DEDUCTION else (var_1AJ + var_1TT)))
DEDUCTION2 = min(MAX_10PERCENT_DEDUCTION, max(round((var_1BJ + var_1UT) * 0.10), \
    MIN_10PERCENT_DEDUCTION if (var_1BJ + var_1UT) >= MIN_10PERCENT_DEDUCTION else (var_1BJ + var_1UT)))
INCOME = var_1AJ + var_1TT - DEDUCTION1 +  var_1BJ + var_1UT  - DEDUCTION2 + var_1TZ - var_6DE
RFR = INCOME + var_1UZ + var_1WZ + var_3VG # 606574.0
```

### Total Taxes

```python
SOCIAL_ACQ_GAINS_TAXES = round((var_1TT + var_1UT) * 0.092) + round((var_1TT + var_1UT) * 0.005) + \
    round((var_1TZ + var_1WZ + var_1UZ) * 0.097) +  round((var_1TZ + var_1WZ + var_1UZ) * 0.075)
EMPLOYEE_CONTRIBUTION_TAXES = round((var_1TT + var_1UT) * 0.10)
CAPITAL_GAINS_INCOME_TAXES = round(var_3VG*0.128)
CAPITAL_GAINS_SOCIAL_TAXES = round((var_3VG*0.097) + (var_3VG*0.075))
CEHR_TAXES = computeCEHRTaxes(RFR, tax_shares)
TAXES = round(incomeTaxes(0, round(INCOME/2.0)) * 2.0) + \
    SOCIAL_ACQ_GAINS_TAXES + \
    EMPLOYEE_CONTRIBUTION_TAXES + \
    CAPITAL_GAINS_INCOME_TAXES + \
    CAPITAL_GAINS_SOCIAL_TAXES + \
    CEHR_TAXES  # 212602
```

### Withholding tax rate / Taux de prélèvement à la source (taux foyer)

```python
IR = round(incomeTaxes(0, round(INCOME/2))*2)
Rinclus = var_1AJ - round(DEDUCTION1*(var_1AJ)/(var_1AJ+var_1TT)) + var_1BJ \
    - round(DEDUCTION2*(var_1BJ)/(var_1BJ+var_1UT))
RNI = Rinclus + var_1TT - round(DEDUCTION1*(var_1TT)/(var_1AJ+var_1TT)) + var_1UT \
    - round(DEDUCTION2*(var_1UT)/(var_1BJ+var_1UT)) + var_1TZ
Rras = var_1AJ + var_1BJ
Taux_foyer = (IR*(Rinclus/RNI))/Rras # 30.3%
```

### Individualized Withholding tax rate / Taux de prélèvement à la source individualisé 1AJ

We start with 1AJ because var_1AJ + var_1TT < var_1BJ + var_1UT

```python
INCOME1 = var_1AJ + var_1TT - DEDUCTION1 + var_1TZ/2 - var_6DE/2
IR1=round(incomeTaxes(0,  INCOME1))
Rinclus1 = var_1AJ - round(DEDUCTION1*(var_1AJ)/(var_1AJ+var_1TT))
RNI1 = Rinclus1 + var_1TT - round(DEDUCTION1*(var_1TT)/(var_1AJ+var_1TT)) + var_1TZ/2
Rras1 = var_1AJ
IR1*(Rinclus1/RNI1)/(Rras1) # 27.6%
```

### Individualized Withholding tax rate / Taux de prélèvement à la source individualisé 1BJ

```python
(IR*(Rinclus/RNI) - IR1*(Rinclus1/RNI1)/(Rras1) * (var_1AJ)
     -  IR*(Rinclus/RNI)/(Rras)* 0)/(var_1BJ) # 32.1%
```

## 5. Employee / Married / No Stocks / Deductibles expenses

An employee has an annual salary (**1AJ**) of **120000 euros**. His wife has a salary of **160000 euros** (**1BJ**). They have deductible expenses (**6DE**) of **7000 euros**.

### Reference Fiscal Revenue / Revenu Fiscal de Reference 

```python
var_1AJ = 120_000
var_1TT = 0
var_1BJ = 160_000
var_1UT = 0
var_1TZ = 0
var_1UZ = 0
var_1WZ = 0
var_3VG = 0
var_6DE = 7_000
tax_shares = 2.0
MAX_10PERCENT_DEDUCTION = 14_426
DEDUCTION1 = min(MAX_10PERCENT_DEDUCTION, max(round((var_1AJ + var_1TT) * 0.10), \
    MIN_10PERCENT_DEDUCTION if (var_1AJ + var_1TT) >= MIN_10PERCENT_DEDUCTION else (var_1AJ + var_1TT)))
DEDUCTION2 = min(MAX_10PERCENT_DEDUCTION, max(round((var_1BJ + var_1UT) * 0.10), \
    MIN_10PERCENT_DEDUCTION if (var_1BJ + var_1UT) >= MIN_10PERCENT_DEDUCTION else (var_1BJ + var_1UT)))
INCOME = var_1AJ + var_1TT - DEDUCTION1 +  var_1BJ + var_1UT  - DEDUCTION2 + var_1TZ - var_6DE
RFR = INCOME + var_1UZ + var_1WZ + var_3VG  # 246574
```

### Total Taxes

```python
SOCIAL_ACQ_GAINS_TAXES = round((var_1TT + var_1UT) * 0.092) + round((var_1TT + var_1UT) * 0.005) + \
    round((var_1TZ + var_1WZ + var_1UZ) * 0.097) +  round((var_1TZ + var_1WZ + var_1UZ) * 0.075)
EMPLOYEE_CONTRIBUTION_TAXES = round((var_1TT + var_1UT) * 0.10)
CAPITAL_GAINS_INCOME_TAXES = round(var_3VG*0.128)
CAPITAL_GAINS_SOCIAL_TAXES = round((var_3VG*0.097) + (var_3VG*0.075))
CEHR_TAXES = computeCEHRTaxes(RFR, tax_shares)
TAXES = round(incomeTaxes(0, round(INCOME/2.0)) * 2.0) + \
    SOCIAL_ACQ_GAINS_TAXES + \
    EMPLOYEE_CONTRIBUTION_TAXES + \
    CAPITAL_GAINS_INCOME_TAXES + \
    CAPITAL_GAINS_SOCIAL_TAXES + \
    CEHR_TAXES # 68985
```

### Withholding tax rate / Taux de prélèvement à la source (taux foyer)

```python
IR = round(incomeTaxes(0, round(INCOME/2)))*2
Rinclus = var_1AJ - round(DEDUCTION1*(var_1AJ)/(var_1AJ+var_1TT)) + var_1BJ \
    - round(DEDUCTION2*(var_1BJ)/(var_1BJ+var_1UT))
RNI = Rinclus + var_1TT - round(DEDUCTION1*(var_1TT)/(var_1AJ+var_1TT)) + var_1UT \
    - round(DEDUCTION2*(var_1UT)/(var_1BJ+var_1UT)) + var_1TZ
Rras = var_1AJ + var_1BJ
Taux_foyer = (IR*(Rinclus/RNI))/Rras # 24.6%
```

### Individualized Withholding tax rate / Taux de prélèvement à la source individualisé 1AJ

We start with 1AJ because var_1AJ + var_1TT < var_1BJ + var_1UT

```python
INCOME1 = var_1AJ + var_1TT - DEDUCTION1 + var_1TZ/2 - var_6DE/2
IR1=round(incomeTaxes(0,  INCOME1))
Rinclus1 = var_1AJ - round(DEDUCTION1*(var_1AJ)/(var_1AJ+var_1TT))
RNI1 = Rinclus1 + var_1TT - round(DEDUCTION1*(var_1TT)/(var_1AJ+var_1TT)) + var_1TZ/2
Rras1 = var_1AJ
IR1*(Rinclus1/RNI1)/(Rras1) # 22.3
```

### Individualized Withholding tax rate / Taux de prélèvement à la source individualisé 1BJ

```python
(IR*(Rinclus/RNI) - IR1*(Rinclus1/RNI1)/(Rras1) * (var_1AJ) \
    - IR*(Rinclus/RNI)/(Rras)* 0)/(var_1BJ) # 26.4%
```

## 6. Employee / Married / Hollande and Macron 1 stocks / Deductibles expenses

An employee has an annual salary (**1AJ**) of **120000 euros**. He sold **Hollande** stocks with **acquisition gains** of **25626 euros**. And he also sold **Macron 1** with acquisition gains of **6509 euros** (**1TZ = 6509**, **1UZ = 6509**).
His wife has a salary of **160000 euros** (**1BJ**) sold **Hollande** stocks with **acqusition gains** of **16273 euros** (**1UT = 16273**).
They have a **capital gains** of **34612 euros** (**3VG**) and deductible expenses (**6DE**) of **7000 euros**.


### Reference Fiscal Revenue / Revenu Fiscal de Reference 

```python
var_1AJ = 120_000
var_1TT = 25_626
var_1BJ = 160_000
var_1UT = 16_273
var_1TZ = 6_509
var_1UZ = 6_509
var_1WZ = 0
var_3VG = 34_612
var_6DE = 7_000
tax_shares = 2.0
MAX_10PERCENT_DEDUCTION = 14_426
DEDUCTION1 = min(MAX_10PERCENT_DEDUCTION, max(round((var_1AJ + var_1TT) * 0.10), \
    MIN_10PERCENT_DEDUCTION if (var_1AJ + var_1TT) >= MIN_10PERCENT_DEDUCTION else (var_1AJ + var_1TT)))
DEDUCTION2 = min(MAX_10PERCENT_DEDUCTION, max(round((var_1BJ + var_1UT) * 0.10), \
    MIN_10PERCENT_DEDUCTION if (var_1BJ + var_1UT) >= MIN_10PERCENT_DEDUCTION else (var_1BJ + var_1UT)))
INCOME = var_1AJ + var_1TT - DEDUCTION1 +  var_1BJ + var_1UT  - DEDUCTION2 + var_1TZ - var_6DE
RFR = INCOME + var_1UZ + var_1WZ + var_3VG  # 333677
```

### Total Taxes

```python
SOCIAL_ACQ_GAINS_TAXES = round((var_1TT + var_1UT) * 0.092) + round((var_1TT + var_1UT) * 0.005) + \
    round((var_1TZ + var_1WZ + var_1UZ) * 0.097) +  round((var_1TZ + var_1WZ + var_1UZ) * 0.075)
EMPLOYEE_CONTRIBUTION_TAXES = round((var_1TT + var_1UT) * 0.10)
CAPITAL_GAINS_INCOME_TAXES = round(var_3VG*0.128)
CAPITAL_GAINS_SOCIAL_TAXES = round((var_3VG*0.097) + (var_3VG*0.075))
CEHR_TAXES = computeCEHRTaxes(RFR, tax_shares)
TAXES = round(incomeTaxes(0, round(INCOME/2.0)) * 2.0) + \
    SOCIAL_ACQ_GAINS_TAXES + \
    EMPLOYEE_CONTRIBUTION_TAXES + \
    CAPITAL_GAINS_INCOME_TAXES + \
    CAPITAL_GAINS_SOCIAL_TAXES + \
    CEHR_TAXES  # 108714
```

### Withholding tax rate / Taux de prélèvement à la source (taux foyer)

```python
IR = round(incomeTaxes(0, round(INCOME/2))*2)
Rinclus = var_1AJ - round(DEDUCTION1*(var_1AJ)/(var_1AJ+var_1TT)) + var_1BJ \
    - round(DEDUCTION2*(var_1BJ)/(var_1BJ+var_1UT))
RNI = Rinclus + var_1TT - round(DEDUCTION1*(var_1TT)/(var_1AJ+var_1TT)) + var_1UT \
    - round(DEDUCTION2*(var_1UT)/(var_1BJ+var_1UT)) + var_1TZ
Rras = var_1AJ + var_1BJ
Taux_foyer = (IR*(Rinclus/RNI))/Rras # 26.7%
```

### Individualized Withholding tax rate / Taux de prélèvement à la source individualisé 1AJ

We start with 1AJ because var_1AJ + var_1TT < var_1BJ + var_1UT

```python
INCOME1 = var_1AJ + var_1TT - DEDUCTION1 + var_1TZ/2 - var_6DE/2
IR1=round(incomeTaxes(0,  INCOME1))
Rinclus1 = var_1AJ - round(DEDUCTION1*(var_1AJ)/(var_1AJ+var_1TT))
RNI1 = Rinclus1 + var_1TT - round(DEDUCTION1*(var_1TT)/(var_1AJ+var_1TT)) + var_1TZ/2
Rras1 = var_1AJ
IR1*(Rinclus1/RNI1)/(Rras1) # 25.2
```

### Individualized Withholding tax rate / Taux de prélèvement à la source individualisé 1BJ

```python
(IR*(Rinclus/RNI) - IR1*(Rinclus1/RNI1)/(Rras1) * (var_1AJ) \
    - IR*(Rinclus/RNI)/(Rras)* 0)/(var_1BJ) # 27.8%

```
# CDHR Examples

The Contribution Differentielle sur les Hauts Revenus is quite complex to compute, this is what you need to compute it.

## Pre-requisites for CDHR computation

```python
def computeTaxesForCDHR(
    INCOME_TAXES: float,
    var_3VG: float,
    var_2DC: float,
    var_2TR: float,
    CEHR_TAXES: float,
    RFR_AUTONOME: float,
    tax_shares: float,
) -> float:
    capital_income = var_3VG + var_2DC + var_2TR
    capital_taxes = roundu(capital_income * 0.128)
    TOTAL_TAXES = INCOME_TAXES + capital_taxes + CEHR_TAXES
    if tax_shares >= 2.0 and RFR_AUTONOME >= 500_000.0:
        TOTAL_TAXES += 12_500.0
    return TOTAL_TAXES

def computeCDHRTaxes(
    total_taxes_for_cdhr: float,
    RFR_AUTONOME: float,
    tax_shares: float,
) -> float:
    if RFR_AUTONOME != 0.0:
        average_tax_rate = roundu(total_taxes_for_cdhr / RFR_AUTONOME * 1000.0) / 10.0
    else:
        average_tax_rate = 0.0
    is_applicable = (average_tax_rate < 20.0) and (
        (tax_shares == 1.0 and RFR_AUTONOME >= 250_000.0)
        or (tax_shares >= 2.0 and RFR_AUTONOME >= 500_000.0)
    )
    if not is_applicable:
        return 0.0
    target_tax = roundu(RFR_AUTONOME * 0.20)
    discount = 0.0
    if tax_shares == 1.0 and RFR_AUTONOME <= 330_000.0:
        discount = target_tax - 0.825 * (RFR_AUTONOME - 250_000.0)
    elif tax_shares >= 2.0 and RFR_AUTONOME <= 660_000.0:
        discount = target_tax - 0.825 * (RFR_AUTONOME - 500_000.0)
    cdhr_taxes = 0.0
    if (target_tax - discount) > total_taxes_for_cdhr:
        cdhr_taxes = target_tax - discount - total_taxes_for_cdhr
    return cdhr_taxes
```
## Employee / Married / Hollande, Macron 1 and Macron 3 stocks

An employee has an annual salary (**1AJ**) of **10000 euros**. He sold **Hollande** stocks with **acquisition gains** of **89_300 euros**. And he also sold **Macron 1** with acquisition gains of **89_300 euros** and **Macron 3** with acquisition gains of **44_650 euros** (**1TZ = 133_950**, **1UZ = 89_300**, **1WZ = 44_650**).
His wife has a salary of **0 euros** (**1BJ**).
They have a **capital gains** of **18_602_800 euros** (**3VG**).


### Reference Fiscal Revenue / Revenu Fiscal de Reference 

```python
var_1AJ = 10_000
var_1TT = 89_300
var_1BJ = 0
var_1UT = 0
var_1TZ = 133_950
var_1UZ = 89_300
var_1WZ = 44_650
var_3VG = 18_602_800
var_2DC = 0
var_2TR = 0
var_6DE = 0
tax_shares = 2.0
MAX_10PERCENT_DEDUCTION = 14_426
DEDUCTION1 = min(MAX_10PERCENT_DEDUCTION, max(roundu((var_1AJ + var_1TT) * 0.10), \
    MIN_10PERCENT_DEDUCTION if (var_1AJ + var_1TT) >= MIN_10PERCENT_DEDUCTION else (var_1AJ + var_1TT)))
DEDUCTION2 = min(MAX_10PERCENT_DEDUCTION, max(roundu((var_1BJ + var_1UT) * 0.10), \
    MIN_10PERCENT_DEDUCTION if (var_1BJ + var_1UT) >= MIN_10PERCENT_DEDUCTION else (var_1BJ + var_1UT)))
INCOME = var_1AJ + var_1TT - DEDUCTION1 +  var_1BJ + var_1UT  - DEDUCTION2 + var_1TZ - var_6DE
RFR = INCOME + var_1UZ + var_1WZ + var_3VG  # 18960070
RFR_AUTONOME = INCOME + var_1UZ + var_3VG  # 18915420
```

### Total Taxes

```python
CRDS_CSG = roundu((var_1TZ + var_1UZ + var_1WZ + var_3VG + var_2DC + var_2TR) * 0.097)
CRDS =  roundu((var_1TT + var_1UT) * 0.005)
CSG =  roundu((var_1TT + var_1UT) * 0.092)
EMPLOYEE_CONTRIBUTION_TAXES  = roundu((var_1TT + var_1UT) * 0.10)
PRELEVEMENT_SOLIDARITES =  roundu((var_1TZ + var_1UZ + var_1WZ + var_3VG + var_2DC + var_2TR) * 0.075)
CAPITAL_GAINS_INCOME_TAXES = roundu((var_3VG + var_2DC + var_2TR)*0.128)
CEHR_TAXES = computeCEHRTaxes(RFR, tax_shares) # 733403
INCOME_TAXES = roundu(incomeTaxes(0, INCOME/2.0) * 2.0)
TAXES = INCOME_TAXES + \
    CRDS_CSG + \
    CRDS + \
    CSG + \
    EMPLOYEE_CONTRIBUTION_TAXES + \
    PRELEVEMENT_SOLIDARITES + \
    CAPITAL_GAINS_INCOME_TAXES + \
    CEHR_TAXES  # 6437366
TAXES_FOR_CDHR = computeTaxesForCDHR(INCOME_TAXES, var_3VG, var_2DC, var_2TR, CEHR_TAXES, RFR_AUTONOME, tax_shares)
CDHR_TAXES = computeCDHRTaxes(TAXES_FOR_CDHR, RFR_AUTONOME, tax_shares) # 596572
TOTAL_TAXES = TAXES + CDHR_TAXES # 7033938
```

