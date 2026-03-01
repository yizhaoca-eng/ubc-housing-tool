# UBC Housing Decision Tool — Requirements

## Project Overview

An interactive single-file HTML tool to help a UBC student compare renting vs buying a home near UBC Vancouver. The tool includes a financial calculator, buy listings, and rent listings, all within a tabbed interface.

---

## 1. Location Constraints

- Apartment must be within **45 minutes via public transit** to UBC Vancouver campus
- Car travel time must be within **30 minutes** (car-share or Uber assumed; limited parking on campus)

---

## 2. Rent Scenario Requirements

- Must include **air conditioning**
- Monthly cost must be **less than CAD $2,500/month**
- Two sub-scenarios supported via toggle:
  - **1BR Alone** — full rent paid by occupant
  - **2BR Shared** — 50/50 split with roommate; occupant pays half the 2BR total rent
- The **1BR/2BR toggle applies to both the Rent scenario and the Bosa Rent scenario**
- Total rent paid calculated using: `totalRentPaid += monthlyRent × (1 + rentInflation)^year` compounded annually

---

## 3. Buy Scenario Requirements

- Down payment: **CAD $300,000**
- Monthly mortgage payment must be **less than CAD $2,500**
- **Sublease option**: one room rented to another student to offset mortgage payment
  - Toggle: On / Off
  - Input: monthly sublease income
- Property equity at exit = `(purchasePrice × (1 + appreciation)^years) − remainingMortgageBalance`

---

## 4. Calculator — Inputs & Assumptions

### Shared Assumptions (sliders/inputs)
- Time horizon: 1–30 years
- Annual property appreciation rate (%)
- Annual investment return rate (%) — applied to down payment opportunity cost
- Annual rent inflation rate (%)
- Mortgage interest rate (%)
- Amortization period (years)

### Buy Scenario Inputs
- Purchase price (CAD$)
- Down payment (CAD$)
- Monthly strata/fees (CAD$)

### Sublease Option
- Toggle: On / Off
- Monthly sublease income (CAD$)

### Rent Scenario Inputs
- 1BR monthly rent (CAD$)
- 2BR total rent (CAD$) with live "your share" display

### BPI Equity Program (in calculator)
- Toggle: Enabled / Disabled
- Future Bosa purchase price (CAD$) — single authoritative input, displayed read-only in the estimator tab
- When enabled: accumulates 25% of `bpi-rent × bpi-months`, capped at 5% of future purchase price
- BPI calculation runs **before** the amortization loop so it is available for chart data

---

## 5. Calculator — Outputs

### Summary Metrics Row
- Total Net Cost — Rent (net of opportunity gain)
- Total Net Cost — Buy (net of equity)
- Break-even Year (year buying becomes cheaper than renting)

### Cumulative Net Cost Chart
- Line chart (Chart.js) showing annual cumulative net cost over the scenario horizon
- Lines: Rent (green), Buy (navy), Bosa+BPI (gold dashed — visible only when Bosa card selected)
- Interactive tooltips
- Legend updates dynamically

### Three Scenario Boxes (prominent, side-by-side)

#### Rent Scenario (green top border)
- Monthly Rent (yr 1)
- Total Rent Paid
- Opportunity Gain on Down Payment
- Net Cost (pure rent, no BPI — BPI is shown in the Bosa box only)

#### Buy Scenario (navy top border)
- Monthly Mortgage
- Total Paid (mortgage + fees)
- Sublease Income
- Property Equity at Exit
- Net Cost

#### Bosa Rent + BPI (gold top border)
- Visible/active only when a Bosa location card is selected in the Rent Listings tab
- Dimmed with "No location selected" when inactive
- Address of selected Bosa location displayed
- Bosa Monthly Rent (your share per 1BR/2BR toggle)
- Total Bosa Rent Paid (Bosa rate for tenancy months, then regular rent inflated for remainder)
- Opportunity Gain on Down Payment
- Usable BPI Credit (deducted)
- Net Cost

### Monthly Budget Note
- Displays effective monthly buy cost (mortgage + strata − sublease)
- Flags red if exceeds $2,500 budget, green if within

---

## 6. Buy Listings Tab

Two area panels, each with a direct Realtor.ca pre-filtered link:

### UBC Area Panel
- Link: `realtor.ca` pre-filtered to 2BR condos in University Endowment Lands
- Transit: 15–20 min (R4/99 bus)
- Drive: 10–15 min
- Tenure: Leasehold (UBC Properties Trust ground lease — confirm expiry)
- Price range: ~$850K–$1.4M
- Strata: ~$580–$850/mo
- Transit zone: 1-zone fare
- Note: leasehold terms apply

### Capstan Village (Richmond) Panel
- Link: `realtor.ca` pre-filtered to 2BR condos in Richmond
- Transit: ~40–50 min (Canada Line + bus transfer) — flagged amber as borderline
- Drive: 15–20 min
- Tenure: Freehold
- Price range: ~$650K–$950K
- Strata: ~$400–$600/mo
- Transit zone: 2-zone fare
- Note: type "Capstan" in keyword search on Realtor.ca to narrow results

---

## 7. Rent Listings Tab

### Bedroom Toggle
- Toggle at top: **1 Bedroom** / **2 Bedroom (Shared)**
- Updates Rentals.ca and Craigslist URLs dynamically
- Apartments.com bedroom filter applied manually on-site

### UBC Area — Three Platform Links
| Platform | URL | Notes |
|---|---|---|
| Apartments.com | UBC off-campus housing page | Bedroom + A/C filter manual |
| Rentals.ca | `rentals.ca/vancouver/west-point-grey?beds=N` | `?beds=` param reliable |
| Craigslist | `vancouver.craigslist.org/search/apa?query=ubc&min_bedrooms=N&max_bedrooms=N` | Keyword + bedroom param |

### Capstan Village — Three Platform Links
| Platform | URL | Notes |
|---|---|---|
| Apartments.com | Richmond apartments page | Narrow via map |
| Rentals.ca | `rentals.ca/richmond?beds=N` | `?beds=` param reliable |
| Craigslist | `vancouver.craigslist.org/search/apa?query=capstan+richmond&min_bedrooms=N&max_bedrooms=N` | May be sparse |

### BPI Equity Program Estimator Widget
Inputs:
- Monthly Rent (CAD$)
- Rental Duration (months)
- Future Bosa Purchase Price — read-only display, set from Calculator tab

Outputs:
- Monthly Credit (25% of rent)
- Total Accumulated
- 5% Cap on Purchase Price
- Usable BPI Credit = `min(accumulated, cap)`
- Status note (under cap or capped)

Updates live as inputs change. Also updates when a Bosa location card is selected.

### Bosa Rental Locations
Three selectable cards (closest Bosa locations to UBC):

| Location | Address | Transit to UBC | Starting Rent |
|---|---|---|---|
| West End | 1108 Pendrell St | ~25 min (Bus 4/44) — within threshold | 1BR ~$2,595/mo |
| Olympic Village / Chinatown | Switchmen · 183 E Georgia | ~35–40 min (Bus 99 / transfer) — borderline | Studio ~$1,995/mo |
| *(placeholder)* | — | No other Bosa locations within 45 min | — |

**Select behaviour:**
- Clicking a card highlights it (green border), marks button as "✓ Selected"
- Syncs `bpi-rent` in the estimator to the location's default rent
- Triggers BPI estimator recalculation
- Shows selected location callout with: Accumulated, 5% Cap, Usable BPI Credit
- Activates the Bosa Rent + BPI scenario box in the Calculator
- Adds the gold dashed Bosa+BPI line to the cumulative chart

---

## 8. BPI Equity Program Logic

- **Rate**: 25% of monthly rent accumulated monthly
- **Cap**: maximum usable credit = 5% of future Bosa purchase price
- **Usable credit** = `min(rent × 25% × months, purchasePrice × 5%)`
- **Validity**: fund available for 24 months after tenancy ends
- **Scope**: applicable to any Bosa Properties home across BC
- **BPI rent basis**: always on full unit rent (regardless of 1BR/2BR split) — this is per program terms
- **Calculator integration**: BPI must be calculated before the amortization loop; `bpi-rent` and `bpi-months` from the estimator are the authoritative inputs

---

## 9. UI & Design

- Single-file HTML, no backend, Chart.js loaded from CDN
- Typography: Cormorant Garamond (serif, headings/values) + Jost (sans-serif, labels/UI)
- Colour palette: warm off-white background, dark charcoal text, forest green (rent), navy (buy), gold/amber (Bosa/BPI), muted borders
- Three-tab layout: **Calculator** | **Buy Listings** | **Rent Listings**
- Active tab indicated by bottom border underline — only active tab underlined (fixed via `onclick` attribute matching)
- Sidebar (controls) and results panel flush at bottom (`align-items: stretch`)
- Scenario boxes are the dominant visual element below the chart — coloured top borders, stat blocks, large serif figures
- Verdict strip at top of calculator shows directional recommendation with colour-coded pill
- Toast notification for any load-into-calculator actions

---

## 10. Hosting

- Tool is a standalone `.html` file with no backend dependencies
- Recommended hosting: GitHub Pages, Netlify (free tier), or direct file share
- Chart.js dependency loaded from CDN — requires internet access to render chart
