# Electronic-Sales-Analysis
This project uses a public/simulated dataset that mirrors real-world electronic sales data across Nigeria. The goal is to showcase advanced data analytics skills covering data cleaning, modelling, exploratory analysis, DAX calculations, visualization design, and secure report delivery (Row-Level Security).
---
# Introduction

The dataset used for this project is a **public/simulated dataset** created to mimic real-world electronic sales transactions across Nigeria. The purpose of the project is to demonstrate end-to-end data analyst skills — from data cleaning and modelling to exploratory analysis, visualization and secure report delivery (row-level security). All figures and visual ordering referenced in this README were taken directly from the project screenshots and should be validated against the raw data when you open the PBIX.

---

# Project description

An interactive **Power BI Sales Dashboard** that provides executive and regional-director level views of electronic product sales (channels, product categories, promotions, and geography). The report contains an Executive/Home page (summary KPIs, channel/product/promo analysis) and a Regional Directors page (zone buttons, bubble map, target gauge, and contact profile).

---

# Project context

**Project link:** *Insert project link here (GitHub / Power BI Service)*
You can view the public project here: **`<INSERT_PROJECT_LINK>`**

This dashboard is intended for internal stakeholders (CEO / Managing Director, Regional Directors, Sales & Marketing, Inventory and Operations teams) to make rapid decisions about inventory, promotions and regional resource allocation.

---

# Problem statement

Provide the Chief Executive / Managing Director with a single authoritative report that answers:

* **Which channels and product categories drive the majority of sales?**
* **Which promotions are most effective?**
* **Which states and zones are underperforming or overperforming?**
* **Who is the responsible Regional Director and how can they be contacted?**
* **How do current sales compare to the assigned Min/Max targets?**
* **How to securely distribute the same report so each Regional Director only sees their region (Row-Level Security)?**

Two specific operational objectives:

1. Deliver a polished executive dashboard for management review and presentation.
2. Implement row-level security (RLS) so each Regional Director sees only the data for their assigned region.

---

# Aim of the project

* Produce a production-quality Power BI report that supports strategic and operational decision making.
* Demonstrate best practices in data cleaning, modelling, DAX measures, visualization design and secure report distribution (RLS).
* Provide reproducible steps and documentation so the report can be maintained and extended.

---

# My Approach — high level workflow

(Organised in the order executed — this sequence follows professional project standards.)

1. **Load data**

   * Import transactional and lookup tables into Power BI / Power Query.
2. **Data cleaning & normalization**

   * Standardize data types, handle nulls, remove duplicates, correct category/label inconsistencies.
3. **Data modelling & engineering**

   * Create a star schema: SalesDetails (fact) with related dimension tables (Product, Channel, Promotion, State/Zone, Directors, Date).
4. **Exploratory Data Analysis (EDA)**

   * Validate totals, inspect distributions, identify outliers, perform top-N analysis and time-series checks.
5. **Measures & calculations**

   * Create robust DAX measures (Total Sales, Quantity, % of total, target comparisons, time intelligence).
6. **Visualization & UX**

   * Build executive Home page and Regional Directors page (maps, KPIs, donut & bar charts, gauge). Add bookmarks and navigation buttons for smooth UX.
7. **Row-Level Security**

   * Implement RLS using Directors table mapped to USERPRINCIPALNAME() so each MD only accesses their region.
8. **Validation & handoff**

   * Validate visuals with stakeholders, fix formatting issues (e.g., gauge scaling), deliver PBIX and executive PowerPoint.

---

# Data Cleaning Checklist (detailed)

* Load these tables into Power Query: **SalesDetails**, **Product**, **StateZone**, **Promotion**, **Channels (primary)** and a created **Directors** table.
* **Data type checks**

  * Ensure `OrderDate` is Date, `UnitPrice` / `SalesAmount` are Currency/Decimal, `Quantity` is integer.
* **Nulls & blanks**

  * Replace or remove null product codes, state names or prices. Flag rows with missing mandatory fields.
* **Duplicates**

  * Identify duplicate `OrderID` or duplicate transaction rows and remove / reconcile them.
* **Inconsistent category names**

  * Uniform casing, trim whitespace, merge synonyms (e.g., “South West” vs “Southwest”).
* **Outlier checks**

  * Identify unrealistically large unit price or quantity values for review.
* **Currency & regional formatting**

  * Ensure consistent currency symbol (₦) and numeric formatting for presentation.
* **Data volume**

  * After cleaning, we retained approximately **15,000 sales records** (example outcome shown in the project context).
* **Populate geo data**

  * Add lat/long or state codes required for mapping visuals.

---

# Context (data sources / shape)

* **Source tables:** SalesDetails (fact), Product, StateZone, Promotion, Channels (primary), Directors (created in Excel)
* **Population table:** Added from Wikipedia (state population 2020) to provide per-capita insights on the map visual.
* **Rows:** ~15,000 cleaned transaction records (project example)
* **Geography:** Nigerian states and zones (North East, South West, Federal Capital Territory, etc.)
* **Directors table fields:** `RegionalMD`, `Zone`, `RegionalMDEmail`, `Directors Photo` 

---

# Modelling & engineering

* Implemented a **star schema**:

  * Fact: `SalesDetails`
  * Dimensions: `Product`, `Channel`, `Promotion`, `StateZone`, `Directors`, `Date`
* Relationships:

  * `SalesDetails[ProductID] -> Product[ProductID]`
  * `SalesDetails[State] -> StateZone[State]`
  * `SalesDetails[RegionalMD] -> Directors[RegionalMD]`
  * `SalesDetails[OrderDate] -> Date[Date]`
* Created derived tables:

  * **Date** table (Date, Day, Month, MonthNumber, Quarter, Year) — marked as Date table.
  * **Product hierarchy** (Category > SubCategory > Product) for drill-down.
  * **Population** table for map density and per-capita analysis.

---

# Exploratory Data Analysis (EDA)

* Top channels by sales (sourced from Home page screenshot):

  * **Outlet** — ₦8.9M (~55.6% of total shown)
  * **Online** — ₦3.6M (~22.8%)
  * **Reseller** — ₦2.2M (~13.9%)
  * **Catalog** — ₦1.2M (~7.8%)
* Top product categories (descending): **Laptops**, Camcorders, Projectors & Screens, Digital SLR Cameras, Home Theater Systems, Televisions, Monitors, Desktops, Smartphones, Digital Cameras.
* Top promotions: **Adventist Promo** (top), Winners Promo, Deeper Promotion, Hammartan Promo, Xmas Holiday Promo, etc.
* Time analysis: snapshot in screenshot shows **Total Sales = ₦16.0M** for a plotted year (2014).
* Regional totals: Regional Directors page displays **Total Sales = ₦56.3M** and **Sum of Order Qty = ₦251K** for the selected scope (verify with source data).
* Map clustering: sales concentrated in Lagos, Abuja, Port Harcourt, Ibadan — South West is strong with Ebony having the highest sales.

---

# Measures & Calculations (DAX examples)

> Replace table/column names to match your model.

```dax
-- Basic aggregations
Total Sales = SUM( SalesDetails[SalesAmount] )

Sum Order Qty = SUM( SalesDetails[Quantity] )

-- Percent of total sales (useful for donut/pie)
% of Total Sales = DIVIDE( [Total Sales], CALCULATE([Total Sales], ALL(SalesDetails)), 0 )

-- Time intelligence (requires Date table)
Total Sales YTD = TOTALYTD( [Total Sales], 'Date'[Date] )

-- Targets (if stored per MD or zone)
Min Target = MIN( Targets[MinTarget] )    -- or from Directors table
Max Target = MAX( Targets[MaxTarget] )

-- Useful for dynamic gauge max (e.g., 120% of highest target)
Gauge Max = MAX( Targets[MaxTarget] ) * 1.2
```

**Note:** In the provided screenshots a gauge displays `₦56.3M` with a small axis maximum (10M) — this indicates a formatting/scale misconfiguration. Use `Gauge Max` to set gauge dynamically or set max to the target table's max.

---

# Row-Level Security (RLS)

* Create a **Directors** lookup table with `RegionalMD` and `RegionalMDEmail`.
* Add a role in Power BI Desktop (Modeling → Manage Roles). Apply a filter on the Directors table such as:

```dax
[Email] = USERPRINCIPALNAME()
```
---

# Visualisation

**Home (Executive) page**

* KPI cards: `Total Sales (₦16.0M)` and `Sum of Order Qty (₦91K)` (example).
* Donut: **Total Sales by Channel** (Outlet, Online, Reseller, Catalog).
* Horizontal bar: **Total Sales by Product Category**.
* Horizontal bar: **Total Sales by Promotion** (top promotions displayed).
* Time scatter/line for `Total Sales by Year/Quarter/Month & Day`.
* Slicers: Zone, Year (buttons), Regional MD (checkbox list).
* Bottom ticker: Zone totals (rolling display).

**Regional Directors page**

* Zone tile buttons (Federal Capital Territory, North Central, North East, North West, South East, South South, South West).
* Bubble map: **Total Sales by State** (size = sales amount).
* Gauge: Total Sales vs Min & Max Target (value example: ₦56.3M).
* KPI cards for `Total Sales` and `Sum of Order Qty` in the selected scope.
* Contact / profile card: Regional MD (example: Bola Tunji — South West — email shown).

**UX details**

* Left navigation buttons (Home, Director) implemented with bookmarks.
* Upload image placeholder for MD photos.
* Consistent corporate color palette and card styling.

---

# Analysis / Work Done — Key insights (from the dashboard screenshots)

> All numbers below are taken from the dashboard screenshots and used as working insights. Validate with raw data for production use.

* **Outlet channel dominates sales** (≈₦8.9M; ~55.6% of the displayed snapshot). Recommendation: prioritize merchandising and inventory for outlet channel.
* **Online is the second largest channel** (≈₦3.6M). Opportunity to invest in e-commerce initiatives and fulfillment.
* **Top product:** Laptops — maintain focused inventory, cross-sell accessories and extended warranties.
* **Best performing promotion:** Adventist Promo — study mechanics and customer targeting to replicate success.
* **Geographic concentration:** South West (including Lagos and Ibadan) shows large clusters of sales; smaller volumes in Federal Capital Territory suggest either an opportunity or data completeness issue.
* **Visualization caution:** Gauge displaying ₦56.3M against a 10M axis — fix gauge scaling to avoid misinterpretation.
* **Contactability:** Each region has an assigned MD and email for quick escalation/follow-up.

---

# Analysis processes

* Data profiling and value distribution checks in Power Query (missing values, cardinality).
* Pivot and slicer testing to ensure measures respond correctly across zones, products and time.
* Map verification: matched state names to coordinates and population table for contextual analysis.
* Repeated measure validation vs raw aggregates to ensure totals reconcile.

---

# Skills and tools used

* **Tools:** Power BI Desktop, Power Query, DAX, Excel (for Directors table and synthetic data), Wikipedia (population), optional Power BI Service for sharing.
* **Skills:** Data cleaning & normalization, star schema modelling, time intelligence, DAX, UX for dashboards, mapping/geospatial visuals, Row-Level Security (RLS), stakeholder storytelling.

---

# KPIs tracked

* **Total Sales (₦)** — primary KPI
* **Sum of Order Quantity**
* **Sales by Channel** (Outlet / Online / Reseller / Catalog)
* **Sales by Product Category**
* **Sales by Promotion**
* **Sales by Zone / State**
* **Sales vs Min / Max Targets**
* **Top N products & promotions**

---

# Key features

* Executive summary / KPI cards for quick decisions.
* Channel & product breakdowns for commercial planning.
* Promotion effectiveness ranking.
* Geospatial bubble map for state-level performance.
* Per-region MD contact card and image placeholder.
* Zone quick-filter buttons for single-click regional views.
* RLS so each MD sees only their region’s data.
* Bookmarks and navigation buttons for a professional UX.

---

# Recommendations (business actions)

* Prioritize **outlet** distribution and stock replenishment for high-selling SKUs (particularly **Laptops**).
* Increase investment and conversion optimization for **Online** channel.
* Replicate successful features of the **Adventist Promo** across other promotions.
* Investigate low totals in zones such as **FCT**: confirm data completeness and consider targeted marketing.
* Fix visualization bugs (gauge axis), and add automated validation checks for extreme values.
* Enable **RLS** and schedule dataset refreshes in Power BI Service, then share role-restricted dashboards with Regional Directors.

---

# Final note

* The **PBIX file** and an **Executive PowerPoint** (summary slides) are attached in this repository for management presentation. Please replace placeholder links and emails with your production values before publishing.
* Values shown in screenshots are illustrative and should be cross-checked against the source data. If you want a synthetic dataset to accompany the PBIX for reviewers, I can produce a sample CSV that matches the schema used.

---

# Files included (recommended repo structure)

```
/assets
  - Home.png
  - Regional Directors.png
/data
  - sales_data_sample.csv     <-- (optional synthetic dataset)
  - population_by_state.csv
/pbix
  - Electronic_Sales_Dashboard.pbix
README.md
LICENSE
```

---

# How to run / reproduce locally

1. Install **Power BI Desktop** (latest stable).
2. Clone this repository.
3. Place your real dataset as `data/sales_data.csv` (or use the provided sample).
4. Open `pbix/Electronic_Sales_Dashboard.pbix` in Power BI Desktop.
5. Update each data source path (Home → Transform data → Data source settings).
6. Validate relationships, refresh data and review the visuals.
7. For RLS: Modeling → Manage Roles → create role with `Directors[RegionalMDEmail] = USERPRINCIPALNAME()`. Test using View as Roles.
8. Publish to Power BI Service and configure role assignments for actual user accounts.

---

# Contact

* **Name:** *Your Name (e.g., Bola Tunji — example used in screenshots)*
* **Email:** *[your.email@example.com](mailto:your.email@example.com)* (replace)
* **GitHub:** `https://github.com/<yourusername>`
* **LinkedIn:** `https://www.linkedin.com/in/<yourprofile>` (optional)

nd I will create it directly for inclusion in this repo.
