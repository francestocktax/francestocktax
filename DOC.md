# 📚 Documentation

## 📥 E-Trade Gain and Losses (Sold Holdings)

You’ll need to download the **Gains & Losses XLSX file**.
Here’s how:

1. **Log in to your E*TRADE account.**
2. Go to **Portfolio > My Account > Gains and Losses**.
3. Select the **financial year** you want to export (e.g. 2024).
4. Click on **Download**, then select **Download Expanded**.

This will give you an **XLSX file** that contains all your gains and losses transactions, ready to import into the app.

## 📥  E-Trade By Benefit Type (Sellable Holdings)

To track which shares are still sellable (vested RSUs and ESPP), download your **Holdings XLSX file** from E*TRADE:

1. **Log in to your E*TRADE account.**
2. Navigate to **Portfolio > Holdings**.
3. Select **View By Type** to export your shares.
4. Click **Download**, then choose **Download Expanded**.

This will give you an **XLSX file** listing all your current holdings by type, which the app can import in the database. ⚠️ Non qualified RSUs and ESPP are converted to Stocks because you already paid income taxes, only capital gains taxes remain.

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
