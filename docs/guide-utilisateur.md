# 📚 Documentation

## 📥 E-Trade Gain and Losses (Sold Holdings)

You’ll need to download the **Gains & Losses XLSX file**.
Here’s how:

1. **Log in to your E*TRADE account.**
2. Go to **Portfolio > My Account > Gains and Losses**.
3. Select the **financial year** you want to export (e.g. 2024).
4. Click on **Download**, then select **Download Expanded**.

This will give you an **XLSX file** that contains all your gains and losses transactions, ready to import into the app.

---

## 📥  E-Trade By Benefit Type (Sellable Holdings)

To track which shares are still sellable (vested RSUs and ESPP), download your **Holdings XLSX file** from E*TRADE:

1. **Log in to your E*TRADE account.**
2. Navigate to **Portfolio > Holdings**.
3. Select **View By Type** to export your shares.
4. Click **Download**, then choose **Download Expanded**.

This will give you an **XLSX file** listing all your current holdings by type, which the app can import in the database. ⚠️ Non qualified RSUs and ESPP are converted to Stocks because you already paid income taxes, only capital gains taxes remain.

---

## 🧮 Simulate selling your Apple stocks at a target price

1. In the **`Sell/Select Multiple Holdings`** form in the app.
2. Enter:
   - **Ticker:** e.g. `AAPL`
   - **Date of sale:** this can be **today** or any future date.
   - **Target sale price:** e.g. `250.0`.
     - If you don’t specify a price, the app will use the **vested price or purchase price** by default to calculate gains.

3. The **currency rate (€/$)** will be automatically fetched for that date.

4. Review the results to see:
   - Your gross proceeds
   - Estimated tax due (acquisition gains, capital gains and social contributions...)
   - Net cash you would receive after taxes

This helps you plan your sales strategy or anticipate your future tax bill.

If you want to **reset the simulation**, simply click on **`Rollback`**.

This will restore your holdings and calculations to their original state, letting you start a new scenario from scratch.

✅ Tip: You can repeat this with different target prices or dates to see **what‑if scenarios** and pick the best timing.

---

## 💰 How to add your E*TRADE AAPL dividends

To add all your **E*TRADE AAPL 2024 dividends** in **France Stock Tax**, you’ll need your **Single Account Statements** for the following dates:

- `02/29/24`
- `05/31/24`
- `08/31/24`
- `11/30/24`

### 📥 Download your Single Account Statements from E*TRADE

1. Log in to your **E*TRADE account**.
2. Go to **Documents > Documents and Statements**.
3. Select **Document type**: **Statements**.
4. Set **Timeframe:** to `2024` and click **Apply**.
5. Download the four statements corresponding to the above dates.

### 📝 Locate your dividends

In each of the four statements, find the section:

> **Activity > Cash Flow Activity by Date**

This will show the dividend payments for your AAPL shares.

### ➕ Enter the dividends in France Stock Tax

Use the **“Add New Dividends”** form in the app and add each dividend manually:

| Field              | Value                          |
|---------------------|-------------------------------|
| **Ticker Name**     | `AAPL`                        |
| **Payment Date**    | Activity date from statement  |
| **Payment Country** | `Etats-Unis`                  |
| **Dividend Amount** | Amount of **Qualified Dividend** |
| **Tax Withholding** | Auto-filled by the app        |
| **USD/EUR Rate**    | Auto-filled by the app        |

Repeat this process **4 times**, once for each dividend.

✅ That’s it! Your AAPL dividends for 2024 will now be properly tracked for French tax purposes.

## 💰 How to add your E*TRADE interests

To add all your **E*TRADE interests** in **France Stock Tax**, you’ll need the last **Single Account Statements** of the year.

### 📥 Download the last Single Account Statement from E*TRADE

1. Log in to your **E*TRADE account**.
2. Go to **Documents > Documents and Statements**.
3. Select **Document type**: **Statements**.
4. Set **Timeframe:** to `2024` and click **Apply**.
5. Download the last **Single Account Statement** (12/31/2024).

### 📝 Locate your interests

In the last statement, find the **CASH FLOW** section:

> **CASH FLOW** > **OPENING CASH, BDP, MMFS** > **This Year (1/1/24-12/31/24)**

### ➕ Enter the interests in France Stock Tax

Use the **“Add New Interests”** form in the app and add interests manually:

| Field                    | Value                            |
|--------------------------|----------------------------------|
| **Account Name**         | `E-Trade`                        |
| **Payment Date**         | 12/31/2024                       |
| **Payment Country**      | `Etats-Unis`                     |
| **Interest Amount**      | Amount of **Interests**          |
| **Tax Withholding**      | Auto-filled by the app           |
| **Yearly Exchange Rate** | Average USD/EUR Rate of the Year |

You can compute the **Yearly Exchange Rate** with the average of the 1st January 2024 and the 31th December 2024, e.g: (0.92 + 0.96)/2 = 0.94.

✅ That’s it! Your E-Trade interests for 2024 will now be properly tracked for French tax purposes.
