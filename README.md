# Electronics Sales Analysis (2019)

## Business problem

The business needs a clear view of how electronics sales varied across the year, locations, products, and order times. This analysis uses the recorded transactions to identify where sales value was concentrated, which products had the highest unit volume, and which periods deserve further investigation. The goal is to support descriptive decisions around inventory readiness, geographic focus, and operational planning—not to claim causal drivers that are not present in the dataset.

## Talk about the data

The notebook loads twelve monthly CSV files for 2019 and concatenates them into one table. The original fields are:

| Field | Description |
|---|---|
| `Order ID` | Order identifier |
| `Product` | Product name |
| `Quantity Ordered` | Units ordered in the transaction row |
| `Price Each` | Recorded unit price |
| `Order Date` | Date and time text for the transaction |
| `Purchase Address` | Purchase address, used to derive the city |
| `month` | Month label added from the source filename |

The combined imported table contains **186,850 rows**. Initial profiling reports 545 nulls in each of the six transaction fields and 1,140 duplicated rows. The notebook then removes incomplete rows and repeated header rows, producing **185,950 rows** for the downstream analysis. It derives sales value as `quantity_ordered * price_each`, extracts city/state from the address, and extracts the clock time from the order-date text.

### Data-quality notes

- The 1,140 duplicates are detected during initial profiling, but the displayed cleaning pipeline does not remove duplicates. Treat this as an unresolved validation item.
- The notebook displays `1.75%` from total missing cells divided by row count. That is not the conventional percentage of all data cells that are missing; the formula's denominator is rows rather than rows × columns.
- `order_date` remains a text/object column in the displayed cleaned schema.
- Currency is not explicitly stated in the displayed fields, so monetary figures below are reproduced as recorded sales values without assigning a currency.

## Show the results of your analysis

### Overall results

| Metric | Result |
|---|---:|
| Imported rows | 186,850 |
| Rows after cleaning | 185,950 |
| Distinct order IDs | 178,437 |
| Distinct products after cleaning | 19 |
| Cities represented | 10 |
| Total recorded sales value | 34,492,035.97 |
| Total units in product aggregation | 209,079 |

### Monthly sales value

December is the highest month at **4,619,297.12** (about **13.39%** of the annual value). January is lowest at **1,815,335.12**. December's value is about **154.46%** higher than January's. The top three months—December, October, and April—contribute approximately **34.07%** of the annual total. This is a one-year comparison and does not establish recurring seasonality.

![Monthly sales value by month](images/monthly_revenue.png)

| Month | Recorded sales value |
|---|---:|
| December | 4,619,297.12 |
| October | 3,736,884.05 |
| April | 3,396,059.11 |
| November | 3,198,909.23 |
| May | 3,144,584.80 |
| March | 2,809,063.30 |
| July | 2,646,899.69 |
| June | 2,578,293.30 |
| August | 2,244,412.31 |
| February | 2,203,481.24 |
| September | 2,098,816.70 |
| January | 1,815,335.12 |

### City-level sales value

San Francisco leads at **8,262,203.91**, followed by Los Angeles at **5,452,570.80** and New York City at **4,664,317.43**. Together, these three cities represent approximately **53.29%** of the city-level recorded sales value. Revenue is not normalized by customer population, store coverage, marketing spend, or operating cost.

![Sales value by city](images/city_revenue.png)

| City | Recorded sales value |
|---|---:|
| San Francisco (CA) | 8,262,203.91 |
| Los Angeles (CA) | 5,452,570.80 |
| New York City (NY) | 4,664,317.43 |
| Boston (MA) | 3,661,642.01 |
| Atlanta (GA) | 2,795,498.58 |
| Dallas (TX) | 2,767,975.40 |
| Seattle (WA) | 2,747,755.48 |
| Portland (OR) | 1,870,732.34 |
| Austin (TX) | 1,819,581.75 |
| Portland (ME) | 449,758.27 |

### Product unit volume

The most frequently ordered product by units is **AAA Batteries (4-pack)** with **31,017 units**, followed by **AA Batteries (4-pack)** with **27,635 units**. The top five products account for **126,401 of 209,079 units** (about **60.46%**). This ranking measures quantity—not sales value or profit.

![Product quantity ordered](images/product_quantity.png)

| Product | Units ordered |
|---|---:|
| AAA Batteries (4-pack) | 31,017 |
| AA Batteries (4-pack) | 27,635 |
| USB-C Charging Cable | 23,975 |
| Lightning Charging Cable | 23,217 |
| Wired Headphones | 20,557 |
| Apple Airpods Headphones | 15,661 |
| Bose SoundSport Headphones | 13,457 |
| 27in FHD Monitor | 7,550 |
| iPhone | 6,849 |
| 27in 4K Gaming Monitor | 6,244 |
| 34in Ultrawide Monitor | 6,199 |
| Google Phone | 5,532 |
| Flatscreen TV | 4,819 |
| Macbook Pro Laptop | 4,728 |
| ThinkPad Laptop | 4,130 |
| 20in Monitor | 4,129 |
| Vareebadd Phone | 2,068 |
| LG Washing Machine | 666 |
| LG Dryer | 646 |

### Order-time analysis

The notebook groups sales value by the exact `HH:MM` time string across the year. The top displayed clock-minute is **19:01** at **54,503.14**, followed by **12:21** at **54,181.96**. These are minute-level totals, not hourly totals; they do not prove that 7 PM is the peak shopping hour.

![Top exact-minute sales-value aggregates](images/top_exact_minute_revenue.png)

## Business recommendations

1. Monitor replenishment for high-volume batteries, charging cables, and wired headphones. Validate stockout history, lead times, and gross margin before changing purchase quantities.
2. Review December inventory and fulfillment capacity ahead of the period, while using more years of data to verify whether this pattern repeats.
3. Investigate demand, service levels, and delivery performance in San Francisco, Los Angeles, and New York City before allocating additional local marketing or inventory budgets.
4. Rebuild the time analysis with hourly aggregation and compare both order counts and sales value before making staffing decisions.
5. Implement a documented duplicate-resolution rule and source-file validation before using the results in financial reporting.
6. Add unit cost, margin, discounts, refunds/returns, and inventory availability to distinguish high-volume products from high-profit products.

## Next steps and limitations you faced

- **Source-file availability:** only the notebook was supplied for this update. The twelve CSVs referenced by the notebook (`Sales/Sales_January_2019.csv` through `Sales/Sales_December_2019.csv`) are not included. The embedded outputs and charts are retained, but a fresh end-to-end run requires those CSVs in the expected `Sales/` directory.
- **Duplicates:** 1,140 duplicated rows are identified in the initial scan; the code does not show a subsequent duplicate-removal step. Recalculate after validating a suitable business key.
- **Missingness metric:** the notebook's 1.75% figure uses row count as the denominator, not the total number of cells.
- **Time grain:** exact-minute grouping is too granular for an hourly peak-period conclusion.
- **Time coverage:** only 2019 is analyzed, so year-over-year growth and recurring seasonality cannot be established.
- **Profitability:** there is no cost, margin, discount, tax, shipping, return, or refund information.
- **Geographic context:** sales value is not normalized for population, customer counts, store coverage, or acquisition spend.
- **Currency:** no currency field or explicit currency declaration appears in the notebook schema.
- **Interpretation:** the findings are descriptive associations and do not establish causal explanations.

## Tools and techniques

Python, Pandas, Matplotlib, Seaborn, data concatenation, missing-value inspection, duplicate inspection, type conversion, feature engineering, group-by aggregation, and exploratory data visualization.

## Project files

- `Sales_analysis_with_Insights.ipynb` — original notebook with added Markdown descriptions, insights, and final recommendations. Existing code cells are preserved.
- `images/` — chart images exported from the notebook's saved outputs.
- `Sales/` — expected location for the twelve monthly CSV source files; the files themselves were not part of the supplied upload.
