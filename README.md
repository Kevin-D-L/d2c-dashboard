# D2C Analytics Challenge — Channel & Product Performance

SQL and Python analysis of a D2C beauty brand's 2024 order data, plus a live dashboard.

**Live dashboard:** https://kevin-d-l.github.io/d2c-dashboard/

## The data

Four related tables: 50 orders, 26 customers, 18 products, and 90 product-channel price rows
(18 products x 5 channels: App, Website, Instagram, Amazon, WhatsApp).

Net revenue is defined throughout as `MRP x quantity x (1 - discount_pct / 100)`, using each
product's discount on the channel where the order was placed.

## Findings

| Question | Answer |
|---|---|
| Orders & avg MRP by segment | Gold 22 orders (₹1,327.64), Silver 18 (₹1,282.11), Bronze 10 (₹1,282.90) |
| Revenue per product | ₹1,83,356.86 total. Top: Vitamin C Serum (₹30,730.35, 16.8% of revenue) |
| Highest-discount channel | Amazon — 17.43% average, 16.33% effective, 22% max |
| Revenue by category | Wellness ₹65,337.06 (16 orders); Skincare ₹64,722.59 (15, highest AOV at ₹4,314.84) |

Three observations worth more than the headline numbers:

- **Segments differ by frequency, not basket size.** Average MRP is nearly identical across
  Gold, Silver and Bronze, but Gold places 2.75 orders per customer against Bronze's 1.25.
- **Highest discount rate is not highest discount cost.** Amazon discounts hardest (17.43%),
  but Instagram gave away more money (₹5,288 vs ₹2,734) because it carries more orders.
- **Discounts reorder the product ranking.** Ashwagandha outsells Conditioner on gross value
  (₹15,510 vs ₹15,345) but falls behind on net revenue.

## Method

`orders` joins `product_pricing` on **both** `product_name` and `channel`. Each product has five
price rows, one per channel, so joining on `product_name` alone fans 50 orders into 250 and
inflates revenue to ₹9,01,936 — roughly 5x. `discount_pct` is a whole number, so it is divided
by `100.0`; integer division would silently zero every discount and return the undiscounted
gross of ₹2,00,644.

The notebook includes a validation cell (key uniqueness, referential integrity, join cardinality)
before any analysis, a side-by-side comparison of the correct and incorrect calculations, and a
reconciliation asserting that the pandas and SQL totals agree.

## Dashboard

The notebook exports joined, pre-calculated order-level data as JSON; an LLM was prompted to
build the presentation layer around it, so the dashboard aggregates verified figures rather than
computing from raw tables. The prompt used is in `prompt.txt` and in the notebook.

`index.html` is self-contained — all CSS and JS inline, data embedded, Chart.js from a CDN as the
only external dependency. It runs from disk with no server.

## Files

| File | |
|---|---|
| `assignment.ipynb` | Full analysis: loading, validation, SQL answers, pandas answer, dashboard export |
| `index.html` | The dashboard |
| `prompt.txt` | The prompt used to generate it |
| `dashboard_data.json` | Order-level data exported from the notebook |

Built with pandas, SQLite (in-memory), and Chart.js.
