# Bond Interest Calculator

A single-page calculator for Indian bonds and NCDs. Enter a bond's terms and it shows the interest you'll receive on each payment date, the TDS deducted, the amount credited to your bank, a financial-year summary, and the real return (YTM) implied by your purchase price.

Everything runs in the browser. There's no server, no tracking and no build step; the whole tool is one `index.html` file.

**Live site:** `https://<your-username>.github.io/bond-calculator/`

---

## Features

- Payout schedule for each interest date, with the days, gross interest, TDS, net credit and running total
- Financial-year view (April to March) to match your AIS / Form 26AS at tax time
- Monthly, quarterly, half-yearly and annual payouts
- Fixed interest day (e.g. the 10th of every month) and payment months for quarterly, half-yearly and annual bonds
- Actual-day interest (Actual/Actual or Actual/365), the method most Indian NCDs use
- Short final period when the last coupon date falls before maturity
- Rounding that matches how brokers credit interest
- YTM implied by your purchase price, to cross-check the quoted YTM
- Estimate of accrued interest when you buy between coupon dates
- Tax estimate at your income-tax slab, compared with the TDS already deducted
- Light and dark themes; works on phones

---

## Inputs

| Field | What to enter |
|---|---|
| **Face value** | Face value of one bond (e.g. ₹1,000 or ₹1,00,000) |
| **No. of bonds** | Number of bonds you hold |
| **Purchase price per bond** | Clean price you paid per bond. Use the face value if you bought at par. |
| **Coupon rate** | Annual interest rate printed on the bond, in % |
| **Payout** | Monthly, Quarterly, Half-yearly or Annual |
| **YTM** | Yield to maturity quoted by the broker, in % |
| **Start date** | Execution / settlement date of your purchase |
| **Maturity date** | Date the bond is redeemed |
| **Interest paid on** | Day of the month interest is paid (1st–31st). For days 29–31, shorter months use the last day of the month. "Same day as maturity" counts back from the maturity date. |
| **In months** | For quarterly, half-yearly and annual bonds: which months the interest is paid in (e.g. Mar, Jun, Sep, Dec) |
| **TDS** | Tax deducted at source, in % (usually 10%) |
| **Your slab** | Your income-tax slab, used to estimate the tax you actually owe |
| **Day count** | How each payout is calculated (see below) |
| **Rounding** | How the amount credited is rounded (see below) |

---

## How the calculation works

### 1. Yearly coupon

```
Annual coupon = Face value × No. of bonds × Coupon rate
```

Interest is always calculated on the face value, never on the price you paid.

### 2. Payment dates

Payment dates are built from the **Interest paid on** day and the payment months, stepping forward one period at a time (1, 3, 6 or 12 months).

- **First payout:** the first payment date *after* your start date. A coupon that falls on or before the start date belongs to the seller.
  - Example: interest paid on the 10th. Executing on 13 Nov 2026 makes the first payout 10 Dec 2026. Executing on 8 Nov makes it 10 Nov.
- **Last payout:** if the last regular payment date falls before maturity, a final payout covers the short period from that date to the maturity date and is paid on the maturity date.
- **Month-end days:** for days 29–31, a shorter month uses its last day (e.g. the 31st becomes 30 Apr or 28 Feb), and the next month goes back to the 31st.

### 3. Interest on each payout (day count)

Each payout covers the period from the previous payment date up to its own date.

| Method | Formula for each payout |
|---|---|
| **Actual/Actual** (default) | Annual coupon × days in period ÷ 365, or ÷ 366 if the period includes 29 February |
| **Actual/365** | Annual coupon × days in period ÷ 365 |
| **Equal payouts** | Annual coupon ÷ payouts per year. A short final period is pro-rated by days. |

Under the actual-day methods, a 31-day month pays more than a 30-day month and February pays the least. This is the most common reason a simple "annual ÷ 12" figure doesn't match a broker's statement.

### 4. TDS and the amount credited

```
TDS        = Gross payout × TDS %
Net credit = Gross payout − TDS
```

### 5. Rounding

| Option | What it does |
|---|---|
| **Net rounded up** (default) | Gross and TDS stay in paise; the net credit is rounded up to the next rupee. This matches how many brokers and issuers credit interest. |
| **Nearest ₹1** | Gross rounded to the nearest rupee, TDS calculated on that and rounded, net = gross − TDS |
| **None** | Exact amounts in paise |

The TDS reported in Form 26AS is the paise figure. The extra rupee from rounding up is just the payment rounded in your favour.

### 6. Tax

```
Tax owed on interest per year = Annual coupon × Your slab
Extra to pay / refund         = Tax owed − TDS deducted
```

TDS isn't an extra cost. It's an advance payment of your tax and is adjusted when you file your return. The slab is applied as entered, without cess or surcharge.

### 7. Return measures

| Measure | Formula |
|---|---|
| **Amount invested** | Purchase price × No. of bonds |
| **Current yield** | Annual coupon ÷ Amount invested |
| **Return per year at your YTM** | Amount invested × YTM |
| **Total coupons till maturity** | Sum of every gross payout in the schedule |
| **Gain / loss at redemption** | (Face value − Purchase price) × No. of bonds |
| **Net rupee gain to maturity** | Total coupons + Gain / loss at redemption (pre-tax) |
| **Approx. post-tax yield** | YTM × (1 − Slab) |

### 8. YTM implied by your price

The calculator solves for the yield `r` that makes the present value of all your cash flows equal to the purchase price:

```
Price = Σ  Cᵢ / (1 + r/f)^(tᵢ × f)  +  Face value / (1 + r/f)^(T × f)
```

- `Cᵢ` is each payout per bond, from the schedule
- `tᵢ` is the years from the start date to that payout (actual days ÷ 365)
- `T` is the years to maturity
- `f` is the number of payouts per year

It's solved numerically by bisection. If the result differs from the quoted YTM by more than 0.25%, the calculator flags it. Common causes are a wrong price, wrong dates, or a quoted price that includes accrued interest.

### 9. Premium, discount and accrued interest

- **Premium** (price above face value): YTM is below the coupon rate, because part of what you paid isn't returned at maturity.
- **Discount** (price below face value): YTM is above the coupon rate, because you get back more than you paid.
- **Accrued interest:** if you buy between payment dates, you usually pay the seller the interest earned since the last payment date, on top of the price. It's estimated as:

```
Accrued interest ≈ First payout × (Start date − Previous payment date) ÷ (First payment date − Previous payment date)
```

You get it back in your first full payout.

---

## Worked example

| Input | Value |
|---|---|
| Face value × bonds | ₹1,00,000 × 3 = ₹3,00,000 |
| Coupon | 11.50%, paid monthly on the 10th |
| Start date | 13 Nov 2026 |
| TDS | 10% |
| Day count / rounding | Actual/Actual, net rounded up |

| Payout | Period | Days | Gross | TDS | Exact net | Credited |
|---|---|---|---|---|---|---|
| 10 Dec 2026 | 10 Nov – 10 Dec | 30 | ₹2,835.62 | ₹283.56 | ₹2,552.05 | **₹2,553** |
| 10 Jan 2027 | 10 Dec – 10 Jan | 31 | ₹2,930.14 | ₹293.01 | ₹2,637.12 | **₹2,638** |

December: 3,00,000 × 11.5% × 30 ÷ 365 = ₹2,835.62. After 10% TDS that's ₹2,552.05, credited as ₹2,553.

---

## Limitations

- **Record date:** if you buy after a coupon's record date (usually a few days to two weeks before the payment date), that coupon still goes to the seller. The calculator assumes you're entitled to every coupon after your start date.
- **Broken first period:** the first coupon of a newly issued bond may cover a non-standard period. The calculator assumes regular periods except for the final one.
- **Payments on holidays:** if a payment date falls on a holiday, the issuer may pay on the next working day. The calculator uses the scheduled date.
- **Capital gains:** a gain or loss on redemption or sale has its own capital-gains tax treatment, which isn't modelled.
- **Charges:** brokerage, stamp duty and other charges aren't included.
- **Floating-rate, step-up and amortising bonds** aren't supported; the calculator assumes a fixed coupon and full repayment at maturity.

---

## Hosting on GitHub Pages

1. Create a public repository (e.g. `bond-calculator`).
2. Upload `index.html` and this `README.md` to the root of the `main` branch.
3. Go to **Settings → Pages**, set the source to **Deploy from a branch**, choose **main** and **/(root)**, and click **Save**.
4. After a minute or two the site is live at `https://<your-username>.github.io/bond-calculator/`.

To update, replace `index.html` and commit. Pages redeploys automatically.

### Run locally

Open `index.html` in any modern browser. An internet connection is needed only for the web fonts; the calculator works without them.

---

## Disclaimer

This tool gives estimates for personal reference. It isn't financial, investment or tax advice. Always check figures against your broker's statement, the bond's offer document and Form 26AS / AIS, and consult a qualified advisor for decisions.
