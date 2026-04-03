# 📊 Retail Analytics: Data & Business Intelligence Model

## Executive Project Summary

This project involves the development of a **comprehensive sales report for a retail company**, specifically designed for executive management. Its purpose is to provide clear visibility into key performance metrics (KPIs) and facilitate data-driven strategic decision-making.

**What was done?** A large dataset of historical sales transactions was analyzed, cleaned, and normalized. We corrected multiple anomalies (duplicate records, financial outliers, and null values) to build a robust data model. Based on this structured model, an interactive reporting dashboard was designed, focused on the user experience and user interface (UI/UX).

**Tools used:** 
- **Python (Pandas, NumPy):** Used for data extraction, transformation, technical cleaning, and data auditing.
- **Power BI (DAX):** Used for data modeling, business intelligence, and management visualization.

---

## 1. Business Context and Project Description

This project consists of a data pipeline. The central objective of this initiative is to consolidate, clean, and establish relational integrity over 4 raw transactional and dimensional datasets of a retail company: **Customers**, **Products**, **Sales Headers (Orders)**, and **Sales Lines (Order Lines)**.

Through this ingestion, historical anomalies, cross-data errors, lack of standardization in nomenclatures, and critical duplications are corrected. The result is a robust, mathematically audited data model structured in its First Normal Form (1NF), ready to be integrated into Business Intelligence (BI) visualization tools, accounting reports, and predictive models, guaranteeing total reliability of the business's financial KPIs (Key Performance Indicators).

---

## 2. Technology Stack

The project is entirely deployed on the Python analytical ecosystem.

- **Main Language:** Python 3.x
- **Data Manipulation and Analysis:** Pandas, NumPy
- **Exploratory Execution Environment:** Jupyter Notebooks (`.ipynb`)
- **Ingestion/Egress Formats:** Comma-separated values files (`.csv`), UTF-8 encoding.

---

## 3. Project Structure

The directory flow is logically separated by stages of the data lifecycle:

```text
📁 Root Directory
 ┣ 📂 Raw               # Contains the original un-processed raw files (.csv)
 ┣ 📂 Processed         # Output folder dynamically generated to host clean, crossable data
 ┣ 📜 Limpieza_Clientes.ipynb
 ┣ 📜 Limpieza_Productos.ipynb
 ┣ 📜 Limpieza_Ventas_Order.ipynb
 ┗ 📜 Limpieza_Ventas_Order_Line.ipynb
```
> **Note:** The `/Processed` folder is dynamically created and populated when running the notebooks, hosting the final "Golden Tables".

---

## 4. Professional Justification: Pre-Power BI Transformations

Performing data cleaning and transformation in a previous processing layer (like Python or SQL) instead of doing it directly inside Power BI (Power Query or DAX) is a **fundamental and strategic practice in database engineering and BI**:

1. **Performance Optimization:** Power BI and its analytical engine are highly specialized for in-memory calculations oriented towards fast visualization. Forcing the environment to execute heavy structural transformations row by row severely impacts file size, monopolizes local RAM resources, and significantly increases overall refresh times.
2. **Technological Decoupling:** Maintaining the purified state of data in a neutral environment like Python gives us a single source of truth (*Golden Records*) that is independent and decoupled from a specific visual provider. This way, the resulting set in the `/Processed` folder is easily consumable by Tableau, Qlik, or other ecosystems.
3. **Version Control and Script Auditing:** In native M language for Power Query, tracking and controlling changes with standard tools (like Git) is inefficient. Python/Pandas cells offer step-by-step auditing, peer code reviews in repositories, and local temporal control.
4. **Early Isolation and Transactional Error Correction:** Normalizing nomenclatures, crossing nulls, repairing negative typing errors, and recovering missing metrics before being read by relational databases or semantic analysis prevents logical breaks in corporate reporting, protecting profitability metrics.

---

##  5. Data Cleaning Mini-Summary (ETL Phase)

Summary of quality interventions to ensure the analytical integrity of the four main datasets:

1. **Record Integrity (Duplicates):** *Primary Keys* were tested looking for clones. We successfully purged **20 duplicate order IDs** and **38 identical line detail IDs**, preventing the reporting of phantom transactional revenues (Cartesian Product).
2. **Key Numeric Values:** Prices and Subtotals financially presented strange negative balances in the source system; these numbers were mathematically transformed into absolute values (`abs()`). In addition, quadratic auditing was performed calculating that quantity multiplied by price accurately matches without hidden discounts.
3. **Formulation, Formats, and Encoding:** All CSV extractions were forced to be under the `UTF-8` structure for linguistic protection (ñ, accents). Logical IDs were recasted to `string` preventing any mistaken mathematical functions.
4. **Combined Column Separation:** The concatenated `seller_region` metric was split into two atomic variables to force the First Normal Form (1NF) and independently answer *Who sells and Where*.
5. **Categorical Text Normalization:** Categorical variables with manual input (Sales Channel, Terminals, Brands, Shifts, Categories) presented a lack of homogeneity. They were capitalized (`Title_Case`), stripping extra spaces to centralize under reliable segmentations.
6. **Missing Values Strategy (Imputation):** Facing blanks in critical product prices and margins, the rule proceeded by logically filling prices starting from: `Cost * (1+Margin)`; and if the margin was missing, it was recalculated: `(Price - Cost)/Cost`.
7. **Margin Inconsistencies and Outliers:** Margins atypically exceeding 100% (inputs of >1) were corrected, dividing them by one hundred (`/100`) to level the percentage scale.
8. **Tax Rate:** Through feature engineering on the `iva` (tax) variable, two essential new fiscal columns were created: `total_amount_with_tax` and `tax_amount`.
9. **Analytical Relevance (Lean Tables):** Purely technical logging and pre-split remnant columns were dropped, leaving pure descriptive (Dim) and economic (Fact) axes.

---
## 6. Detailed Documentation per Notebook

Below are the detailed technical and strategic interventions for each step of the pipeline.

### 📝 File: `Limpieza_Clientes.ipynb`
- **Main Objective:** Clean and standardize the customer dimension for further segmentation and retention analysis, ensuring data structure quality.
- **What was done:**
  - Conversion of the `registration_date` field to datetime format controlling possible mixed variations.
  - Casting of `customer_id` to a string format (`string`/`object`).
  - Casting of the variables `province` and `segment` to `category` type.
  - Forensic check of null and duplicate values (exact and by ID).
  - Final verification of data schemas (`dtypes`).
  - Export of the final DataFrame to `Processed/clientes_procesado.csv`.
- **Technical and Business Decisions (The Why):**
  - **ID as Object:** Customer IDs were intentionally altered to pure text because they are nominal identifiers. Preventing them from being numbers stops analytical engines from trying to mathematically operate on them.
  - **Memory Efficiency (Categories):** Casting provinces and segments to category variables drastically saves RAM usage and speeds up subsequent logical joins (`GROUP BY`, `JOIN`), as the engine internally maps integers instead of long character objects.
  - **Temporal Standardization:** Forcing date parsing under the `dayfirst=True` structural parameter solves incidents where transactional loads mix European or Latin American conventions.

### 📝 File: `Limpieza_Productos.ipynb`
- **Main Objective:** Refactor and purge the transactional catalog (Dimension Table), unifying categories, repairing record inconsistencies, and restoring the veracity of list prices without sacrificing stock amplitude.
- **What was done:**
  - Treatment of identifiers to text `string` format for relational conservation.
  - Removal of monetary symbols (`$`), commas, and measurement indicators (`kg`) from columns like `unit_cost` and `weight_kg` with subsequent numeric float casting.
  - Algorithmic cleaning and unification of percentage formats in `profit_margin`.
  - Nomenclature standardization through normalization and capitalization (.title()) in brands and categories.
  - Grouping rules (controlled `hardcoding`) via a Master Dictionary for correct line mapping in `brand` and `category`.
  - Imputation of null records in `list_price` via live-calculated math.
  - Destructive elimination of redundant products sharing the same internal code.
  - Missing (NaN) imputation in `profit_margin` derived from the cost and existing price.
  - Detection and temporal normalization of margin Outliers above 100% (> 1).
  - Data export to `Processed/productos_procesado.csv`.
- **Technical and Business Decisions (The Why):**
  - **Hierarchical Correction via Master Dictionary:** Relational dirt was detected between brands and categories. Using strong dependencies through the Dictionary proactively corrects erroneously ascribed products.
  - **Recursive Price and Margin Imputation:** Blank values in both margins and prices were not removed to avoid artificial catalog reductions. Recalculating missing values rescues products essential to operations.
  - **Logical Margin Outlier Correction:** We found products with anomalous margins (>1, an human error). Adjusting it back to a real percentage format sanitizes critical distortions in the projection of business profits.
  - **Resolution of Duplicated Internal Codes:** The first historical record was prioritized, assuring an indisputable unique entity per product.

### 📝 File: `Limpieza_Ventas_Order.ipynb`
- **Main Objective:** Structurally normalize the sales headers table (Order Header), strictly respecting the "1st Normal Form" and absolutely mitigating over-billing risks from transactional incidents.
- **What was done:**
  - Reconversion of Foreign and Primary Keys (`order_id`, `customer_id`) to pure text.
  - Exhaustive detection and purging of 20 duplicated order IDs prioritizing the pioneer record.
  - Correction of anomalous accents, removal of strange punctuation marks in `sales_channel`, and visual vocabulary normalization.
  - Dimensional split of the concatenated `seller_region` column into atomic fields: `seller` and `sales_region`.
  - Formatting of session history (from SESSION to SES) and uppercase computer terminals (`terminal_id`).
  - Parsing of the logical timestamp `order_date`.
  - Deployment to `Processed/ventas_order_procesado.csv`.
- **Technical and Business Decisions (The Why):**
  - **Purging of Duplicated Transactions:** Suppressing duplicated headers is mandatory. If not extracted, relating them to detailed lines would form a "Cartesian Product" falsely multiplying the Retail Revenue.
  - **Textual Atomicity (Split Seller and Region):** A foundational architectural pillar is not keeping concatenated attributes. Divorcing seller from territory guarantees isolating performances.
  - **Unification of Metadata and Log Systems:** Transforming terminals minimizes breaks if cybersecurity or auditing needs to cross cash closing operations against rigid databases.

### 📝 File: `Limpieza_Ventas_Order_Line.ipynb`
- **Main Objective:** Debug the order details "Fact Table", prioritizing as absolute rigor the fiscal perfection and auditing of each transacted dollar.
- **What was done:**
  - Conversion of the dependent keys bloc (`line_id`, `order_id`, `product_id`) into `string` matrix.
  - Scanning and hygiene of 38 strictly cloned billing rows.
  - Financial transformation to Absolute Value (`abs()`) of the `unit_price_w/o_tax` and `line_amount` metrics.
  - Forensic evaluation and accounting quadrature validating that `quantity * unit_price_w/o_tax` identically matched the yielded subtotal.
  - Fiscal Feature Engineering: Synthetic construction of the `total_amount_with_tax` and `tax_amount` fields.
  - Final casting of native types for all heavy metric columns prior to export.
  - Compendium export to `Processed/ventas_order_line_procesado.csv`.
- **Technical and Business Decisions (The Why):**
  - **Transformation of Negatives to Absolute Values:** In retail, negative metrics indicate severe spelling errors. Formatting them into absolute values prevents "billing" from being erroneously subtracted.
  - **Purging of Duplicated Details:** Removing cloned lines specifically purges double-clicks of failed shopping carts.
  - **Mathematical Auditing:** Verification between net quantities and unitary variables is the only reliable way to guarantee no hidden discounts are dragged in the backend.
  - **Financial Feature Engineering:** Creating new tax metrics simultaneously satisfies control needs for tax filings and visualizes net free cash flow for Retail.

## 7. Data Model (Relations, Cardinality and Filtering)
For this case, the ideal designed model is a **Hybrid Star Schema** (specifically a dimensional model).

- **Dimensional Tables:** `Customers` and `Products` (and the `Calendar` table).
- **Fact Tables:** `Ventas_Order` (Header) and `Ventas_Order_Line` (Detail).

**Relations:**
- `Customers[customer_id]` **(1)** ➔ `Ventas_Order[customer_id]` **(Many / \*)**
- `Ventas_Order[order_id]` **(1)** ➔ `Ventas_Order_Line[order_id]` **(Many / \*)**
- `Products[product_id]` **(1)** ➔ `Ventas_Order_Line[product_id]` **(Many / \*)**

**Decision Justification:**
- **Cardinality (1 to Many):** Master tables have unique identifiers without duplicates. Sales tables register these IDs multiple times.
- **Filter Direction (Single):** Filtering invariably flows from Dimensions to Fact tables. Bi-directionality severely impacts performance (VertiPaq engine).

## 8. Calculated Columns Dimension (DAX)
Heavy analytical calculations were routed to dimensions:

**A. Calendar Descriptive Openings:** 
(Generated via `CALENDARAUTO()`). E.g., `Year`, `Quarter`, `Month Year`.
**B. Natural Ordering Auxiliaries:** 
Serve as silent "Sort By Column" properties:
* `Quarter Order` = `YEAR(Calendar[Date]) * 10 + FORMAT(Calendar[Date], "Q")`
* `Monthly Order` = `YEAR(Calendar[Date]) * 100 + MONTH(Calendar[Date])`
**C. Profitability Target (Products):**
Calculated on `Products` for swift tactical segmentation: `1- High Profitability`, `2- Medium Profitability`, `3- Low Profitability`.

## 9. Core Metrics and KPIs Catalog (Measure Table)
Financial logic was unified into a floating folder (`_Measures`), hiding raw numeric fields:

**Base Commercial Output:**
* **`Total_Sales`:** Raw monetary base.
* **`Transactions_Amount`:** Operational flow evaluated by ticket volume.
* **`Average_Ticket`:** Value gathered by general order.

**Profitability & Profiling:**
* **`Total_Profit`:** Iteration (`SUMX`) of billed volume purged of inventory costs (`RELATED(unit_cost)`).
* **`Real_Margin_%`:** Combined ("Blended") ratio between Total Profit and Total Sales.
* **`Product_Ranking_in_Category`:** Weighted positioning (`RANKX`) of each item limited exclusively to its category peers (`ALLEXCEPT`).

**Time Intelligence & Self-Service UI:**
* **`Sales_LY` & `YoY % Variation`:** Context of the previous year.
* **`Dynamic_Metric` & `Dynamic Window Filter`:** UI slicer engines allowing button-change of graph representations.

## 10. User Experience: Clean Field Pane
Relational Database complex fields remain outside the consumer optics (`Hide from report view`):

1. **Hide All Foreign and Primary Keys (FK/PK):** Users shouldn't use them to filter.
2. **Hide Dates from Fact Tables:** Filter from `Calendar` directly.
3. **Hide Simple Numeric Columns:** Use measures instead.
4. **Hide Product Features columns:** Unused columns are just hidden, not deleted.

## 11. Business Insights and Analytical Findings

### Annual Trend and Seasonality
* **Growth and History:** The year 2024 ended rebounding by a **17% positive improvement** compared to 2023. 
* **Costs vs Interactions:** The *Average Ticket* soared to **$148,000** in 2024 despite fewer interactions.
* **Seasonality Pattern:** A drastic depression of revenue systematically registers during April in all years.

### Portfolio, Customers and Logistics
* **Segment Leadership:** The *"Basic"* tier dominates general box generation and transaction volume, but has poor margins. The *"Premium"* tier consolidates the **Best Real Margin**.

### Product and Critical Profits
* **Contradiction to Volume:** The *"Backpack 25L"* provides massive volume with the **third lowest margin** (1.5%). 
* **Hidden Gems:** The top net profit earner is the *"Waterproof Jacket"*. Furthermore, *"Whole Wheat Cookies"* quietly provides a **13.5%** liquid ratio.
* **Loss Zones:** The *"Sports T-Shirt"* is registering a **Negative Profit Margin**, requiring immediate investigation.

### Daily Operation (Cash and Methodology)
* **Behavior per Shift:** The Afternoon is the most lucrative, followed by Night.
* **Maximum Average Collecting Ticket** emerges at night tied to Credit Cards.
* **Methodology and Geolocation:** Cash is preferred overall. *Buenos Aires* captures the maximum monetary flow core.

## 12. UI/UX Design and Color Palette

**Exclusive Color Palette:**

| Element | Name / Tone | Hexadecimal Code |
|:---|:---|:---|
| **Main Background** | Deep Space Navy | `#14161F` |
| **Containers/Cards** | Slate Blue Dark | `#222533` |
| **Success/Active Color** | Electric Lime | `#39FF14` / `#2ECC71` |
| **Mid Segment** | Emerald Green | `#27AE60` |
| **Low Segment** | Forest Shadow | `#1E5631` |
| **Data Text** | Pure White | `#FFFFFF` |
| **Labels and Axes** | Cool Gray | `#A0AEC0` |

### Visual Design Justification

1. **Visual Hierarchy and Contrast Ratio:** Using brightest greens for Total Sales forces the user to see importance first.
2. **Visual Ergonomics:** Dark blue (`#14161F`) avoids eye strain during prolonged sessions.
3. **Color Psychology:** Deep Blue conveys stability while Green represents profitability.
4. **Brand Consistency:** Alignment with Sudata colors.

## 13. Typography 

* **Values:** `Segoe UI Semibold` (Size 9)
* **Table Headers:** `Segoe UI Semibold` (Size 10)
* **X and Y Axes:** `Segoe UI Semibold` (Size 10)
* **Titles:** `Inter Semibold` (Size 22 from FIGMA)

## 14. Filter Usage Guide (Timeline)

**Mini-guide: How to understand this control?**

> 📸 *Imagine your graph is a camera. These two controls handle the zoom and resolution.*

**A. The Window (The Zoom)**
Defines _"How much time backward do I want to see?"_.
*   **`1M`**: Camera zooms in showing last 30 days.
*   **`1Y`**: Camera pulls out showing full year.

**B. The Aperture (The Resolution)**
Defines _"How many points do I want on my line?"_.
*   **Daily:** A point per day.
*   **Monthly:** A point per month summarizing long-term trends without noise.
