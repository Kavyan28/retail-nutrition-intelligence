
# Which Products Should Grocery Stores Review First?

### Prioritizing healthier grocery assortments using USDA nutrition data and 33.8 million Instacart purchases

**Boston University | BA780: Introduction to Data Analytics | Team A09**

---

## Project Overview

Grocery retailers carry thousands of products, but not every product contributes equally to the nutritional profile of customer purchases.

A product may exceed recommended nutritional thresholds but account for very few sales. Another product may be purchased repeatedly by thousands of customers, making it a much more important candidate for review.

**Research question: Which grocery products and aisles should retailers prioritize when trying to improve the nutritional quality of their assortments?**

We approach this as a retail analytics and product prioritization problem, integrating two complementary datasets:

- **USDA FoodData Central:** Product-level nutritional composition, including sugar, saturated fat, and sodium.
- **Instacart Market Basket Analysis:** Transaction-level purchasing behavior, including product frequency, aisle classification, and repeat purchases.

By combining nutritional information with observed purchasing patterns, we identify the products and categories where assortment changes could have the greatest potential impact.

### Key Results

| Metric | Finding |
|---|---|
| Packaged products exceeding nutritional thresholds | ~60% |
| Unhealthy purchases concentrated in 10 aisles | 59% |
| Repeat purchase rate: healthier products | 59.6% |
| Repeat purchase rate: unhealthy products | 52.4% |
| Baseline unhealthy purchase share | 41.2% |
| Illustrative post-intervention share | 35.1% |
| Potential reduction in unhealthy purchases | ~492,000 |

> **Central finding:** The categories with the highest proportion of unhealthy products are not necessarily responsible for the greatest volume of unhealthy purchases. Effective assortment decisions require both nutritional and transactional data.

---

## 1. Data Sources

### USDA FoodData Central: Branded Foods

**Source:** [USDA FoodData Central](https://fdc.nal.usda.gov/download-datasets)  
**Release:** April 2026

The USDA Branded Foods dataset contains nutritional information for commercially packaged food products.

**Key variables:**
- Product description and brand
- Total sugar per 100 g
- Saturated fat per 100 g
- Sodium per 100 g
- Product category
- Nutritional label information

| Data processing stage | Records |
|---|---:|
| Original nutritional label records | ~2 million |
| Cleaned and deduplicated products | 442,097 |

### Instacart Market Basket Analysis

**Source:** [Instacart Online Grocery Shopping Dataset](https://www.kaggle.com/datasets/psparks/instacart-market-basket-analysis)  
**Dataset:** 2017 release

| Dataset characteristic | Size |
|---|---:|
| Customers | 206,209 |
| Orders | 3.35 million |
| Purchased items | 33.8 million |
| Source files | 6 |

**Key variables:** `product_id`, `order_id`, `user_id`, `aisle_id`, `department_id`, `reordered`, `order_number`

These variables support product-level demand analysis, aisle comparisons, customer segmentation, and repeat purchasing analysis.

---

## 2. Data Engineering and Integration

### The Integration Challenge

The USDA and Instacart datasets do not share a universal product identifier.

USDA identifies branded food products using its own identifiers, while Instacart uses platform-specific product IDs.

A direct join on product identifiers is therefore not possible.

We implemented a **deterministic product-name matching pipeline** using Python and pandas.

### Product Name Standardization

The matching pipeline applies consistent text-processing rules to both datasets:

1. Convert product names to lowercase.
2. Replace `&` with `and`.
3. Remove punctuation and special characters.
4. Generate standardized product-name keys.
5. Deduplicate USDA product records.
6. Merge datasets using the standardized keys.
7. Validate join cardinality using pandas `validate=` checks.

**Matching approach:** Deterministic string matching. No generative AI, fuzzy matching, or embedding-based matching was used to link product records.

### Join Coverage

| Coverage metric | Result |
|---|---:|
| Instacart products matched | 18.5% |
| Packaged-food purchase volume covered | 36.9% |
| Matched purchases analyzed | ~8.07 million |

Although fewer than one-fifth of Instacart products matched, those products represented a substantially larger share of purchasing activity.

This suggests that frequently purchased products were disproportionately represented in the matched sample.

> **Coverage consideration:** Nutritional conclusions apply to the matched sample and should not be generalized to the complete Instacart catalog without accounting for potential selection bias.

---

## 3. Nutritional Classification Framework

### Defining High-Nutrient Products

We classified products using the United Kingdom's front-of-pack nutritional labeling thresholds.

A product was classified as **unhealthy** if it exceeded at least one threshold for sugar, saturated fat, or salt.

#### Food Thresholds (per 100 g)

| Nutrient | High threshold |
|---|---:|
| Total sugar | >22.5 g |
| Saturated fat | >5 g |
| Salt | >1.5 g |
| Sodium equivalent | >600 mg |

#### Beverage Thresholds (per 100 ml)

| Nutrient | High threshold |
|---|---:|
| Total sugar | >11.25 g |
| Saturated fat | >2.5 g |
| Salt | >0.75 g |

This classification is an operational measure used to compare products, not a comprehensive assessment of dietary quality.

### Sensitivity Analysis: UK vs. FDA

We compared the UK classification framework with US FDA per-serving nutritional criteria.

| Classification framework | Products flagged for high sodium |
|---|---:|
| UK per-100 g thresholds | 21% |
| FDA per-serving criteria | 5% |

**Interpretation:** The proportion of products classified as high in sodium depends substantially on the nutritional benchmark used.

This reinforces the importance of documenting classification assumptions when comparing products or categories.

---

## 4. Analytical Framework

The project addresses **15 research questions across five analytical stages**.

| Stage | Focus | Research questions |
|---|---|---|
| 1. Product composition | Nutritional characteristics and category differences | Q1–Q3 |
| 2. Supplier benchmarking | Brand comparisons and missing data | Q4–Q6 |
| 3. Label validation | Nutritional claims and regulatory consistency | Q7–Q8 |
| 4. Consumer behavior | Purchase frequency, baskets, and loyalty | Q9–Q13 |
| 5. Decision support | Aisle prioritization and scenario analysis | Q14–Q15 |

### Stage 1: Product Composition

- Identify categories with the highest proportion of unhealthy products.
- Determine which nutrients most frequently exceed thresholds.
- Examine whether lower-sodium alternatives exist within relevant categories.

### Stage 2: Supplier Benchmarking

- Compare brands against category-adjusted nutritional benchmarks.
- Evaluate store-brand versus national-brand nutritional profiles.
- Quantify missing nutritional information.

### Stage 3: Label Validation

- Evaluate whether products advertising claims such as "low sodium" satisfy FDA definitions.
- Compare classifications across UK and FDA nutritional frameworks.

### Stage 4: Consumer Purchasing Behavior

- Measure unhealthy purchase concentration by aisle.
- Analyze the nutritional composition of shopping baskets.
- Compare repeat purchase rates.
- Identify high-volume products.
- Examine purchasing behavior across customer-frequency groups.

### Stage 5: Retail Decision Support

- Rank aisles by unhealthy purchasing volume.
- Identify categories with existing healthier alternatives.
- Distinguish promotional opportunities from reformulation opportunities.
- Model illustrative product-substitution scenarios.

---

## 5. Statistical Methods

The analysis combines descriptive statistics, hypothesis testing, category benchmarking, and scenario modeling.

| Statistical method | Application |
|---|---|
| Chi-square test | Associations between categorical variables |
| Two-proportion z-test | Differences in proportions across groups |
| Binomial test | Observed proportions against reference values |
| McNemar test | Paired binary classification comparisons |
| Wilcoxon test | Nonparametric comparisons |
| One-sample t-test | Numerical measures against reference values |

**Interpretation considerations:**

- Statistical significance is evaluated alongside effect sizes and practical relevance.
- Large sample sizes may produce statistically significant results for relatively small differences.
- Observed associations are not interpreted as causal relationships.

---

## 6. Key Findings

### Finding 1: Nutritional Risk Is Concentrated, but Volume Changes Priorities

Approximately **60% of packaged products** exceeded at least one nutritional threshold.

Certain categories had particularly high proportions of flagged products:

| Category | Products classified as unhealthy |
|---|---:|
| Chocolate | 99% |
| Candy | 98% |
| Cakes | 96% |

However, categories with the highest unhealthy product percentages were not necessarily responsible for the most unhealthy purchases.

High-volume categories such as **cheese, cream, and lunch meat** contributed substantially to unhealthy purchasing activity.

**Business implication:** Prioritize purchasing volume alongside nutritional composition rather than ranking categories solely by the percentage of unhealthy products.

### Finding 2: Nutritional Problems Differ Across Categories

| Category | Primary nutritional concern |
|---|---|
| Jams and preserves | Sugar |
| Cold cuts and lunch meats | Sodium |
| Cheese | Multiple nutrients |

Additionally, **42% of unhealthy products exceeded two or more nutritional thresholds**.

**Business implication:** Product reformulation strategies should be tailored to category-specific nutritional characteristics.

### Finding 3: Brand Performance Varies Beyond Product Mix

We compared brand-level nutritional profiles against category-adjusted benchmarks.

This helps distinguish differences attributable to a brand's product mix from differences within comparable food categories.

| Brand-level comparison | Difference from expected category mix |
|---|---:|
| Worst-performing brand owners | Up to 65 percentage points worse |
| Best-performing brand owners | Up to 57 percentage points better |

**Business implication:** Category-adjusted benchmarking can identify suppliers whose products warrant closer nutritional review.

### Finding 4: Nutritional Data Quality Requires Attention

| Data quality issue | Finding |
|---|---:|
| Products missing nutritional values | 17.5% |
| "Low sodium" products not meeting FDA criteria | Approximately 1 in 8 |

**Business implication:** Reliable nutritional benchmarking requires careful treatment of missing values and verification of label claims.

### Finding 5: Frequent Shoppers Drive Purchase Volume

Customers with at least 21 orders represented:

| Metric | Share |
|---|---:|
| Customers | 24% |
| Unhealthy purchases | 58% |

This concentration was primarily associated with greater purchasing activity rather than a disproportionately unhealthy product mix.

**Business implication:** Purchasing frequency should be distinguished from nutritional purchasing preferences when analyzing customer segments.

### Finding 6: Healthier Products Exhibit Higher Repeat Purchase Rates

| Product classification | Repeat purchase rate |
|---|---:|
| Healthier products | 59.6% |
| Unhealthy products | 52.4% |
| Difference | +7.2 percentage points |

Healthier products also had higher repeat purchase rates in **48 of 69 aisles**.

**Business implication:** Healthier alternatives can demonstrate strong repeat purchasing behavior, although this does not establish that promoting them would increase sales or customer retention.

---

## 7. Product and Aisle Prioritization

### Prioritization Framework

Our final framework considers two dimensions:

1. **Unhealthy purchasing volume:** How many purchases are associated with products exceeding nutritional thresholds?
2. **Availability of healthier alternatives:** Are healthier products already being purchased within the same aisle?

The **10 highest-priority aisles account for approximately 59% of unhealthy purchases** in the analyzed data.

These aisles are divided into two strategic groups.

### Strategy A: Promote Existing Healthier Alternatives

| Priority aisles | Recommended actions |
|---|---|
| Chips | Improve visibility of healthier alternatives |
| Nuts | Test targeted product promotions |
| Other creams and cheeses | Increase visibility of healthier substitutes |
| Oils | Compare product positioning and repeat purchasing |

**Rationale:** Healthier alternatives already exist and are purchased by customers within these categories.

### Strategy B: Work With Suppliers on Product Reformulation

| Priority aisles | Recommended actions |
|---|---|
| Cheese | Evaluate lower-saturated-fat and lower-sodium formulations |
| Cream | Explore alternative product formulations |
| Lunch meat | Prioritize sodium reduction |
| Butter | Review saturated-fat content and alternatives |
| Spreads | Evaluate category-specific reformulation opportunities |
| Condiments | Review sodium and sugar concentrations |

**Rationale:** These categories have relatively limited healthier alternatives within the analyzed assortment.

> **Decision principle:** Promote healthier substitutes where they already exist; prioritize supplier engagement where healthier alternatives are limited.

---

## 8. Scenario Analysis

### Estimating the Potential Impact of Product Substitution

We modeled an illustrative scenario in which **25% of unhealthy purchases within the 10 priority aisles** are replaced with healthier alternatives.

| Metric | Baseline | Scenario |
|---|---:|---:|
| Unhealthy purchase share | 41.2% | 35.1% |
| Change | — | -6.1 percentage points |
| Reduction in unhealthy purchases | — | ~492,000 |

The scenario demonstrates how targeting a limited number of high-volume aisles could materially change the nutritional composition of observed purchases.

### Scenario Assumptions and Interpretation

**These estimates are scenario calculations, not forecasts.**

The model does not account for:

- Customer willingness to substitute products
- Product prices and affordability
- Changes in demand
- Supplier reformulation costs
- Retailer margins
- Substitution outside the analyzed categories

**Recommended next step:** Conduct a controlled pilot in one high-priority aisle to evaluate actual purchasing responses.

---

## 9. Technical Stack

| Technology | Purpose |
|---|---|
| Python | Data processing and analysis |
| pandas | Cleaning, joins, aggregation |
| NumPy | Numerical computation |
| SciPy | Statistical hypothesis testing |
| statsmodels | Statistical testing |
| Matplotlib | Data visualization |
| kagglehub | Dataset retrieval |
| Google Colab / Jupyter | Notebook execution |

### Technical Skills Demonstrated

| Area | Methods |
|---|---|
| Data engineering | Multi-source integration, normalization, deduplication |
| Data validation | Join-cardinality checks, missing-data analysis |
| Statistical analysis | Hypothesis testing, proportion comparisons |
| Retail analytics | Product demand, basket analysis, customer segmentation |
| Benchmarking | Category-adjusted supplier comparisons |
| Decision support | Aisle prioritization, scenario modeling |
| Visualization | Analytical charts and business reporting |

---

## 10. Reproducing the Analysis

### Environment Requirements

| Environment | Recommendation |
|---|---|
| Google Colab | High-RAM runtime |
| Local Jupyter | Approximately 16 GB RAM |

### Install Dependencies

```bash
pip install pandas numpy matplotlib scipy statsmodels kagglehub
```

### Execution

1. Clone or download the repository.
2. Open the main analysis notebook in Google Colab or Jupyter.
3. Select a high-memory runtime.
4. Execute all cells sequentially.

The notebook automatically retrieves the required datasets.

| Dataset | Approximate download size | Retrieval method |
|---|---:|---|
| USDA Branded Foods | 449 MB | Direct ZIP download |
| Instacart | 700 MB | `kagglehub` |

The USDA data is read directly from the ZIP archive without manual extraction.

No API keys or user authentication are required for the documented workflow.

**Pipeline:** Data acquisition → Cleaning → Standardization → Integration → Statistical analysis → Visualization → Recommendations

---

## 11. Limitations

| Area | Limitation | Implication |
|---|---|---|
| **Product matching** | Only 18.5% of Instacart products matched, covering 36.9% of packaged-food purchases. | Potential selection bias; findings may not generalize to unmatched products. |
| **Temporal alignment** | Instacart transactions are from 2017, while USDA nutritional records span 2019–2026. | Product formulations and brand portfolios may have changed. Approximately 95% of recipes were estimated to remain unchanged. |
| **Nutritional classification** | UK front-of-pack thresholds are used as the primary classification framework. | Results depend on the benchmark and do not capture every aspect of nutritional quality. |
| **Observational design** | The analysis examines existing purchasing patterns without experimental intervention. | Associations do not establish causality or predict customer responses to assortment changes. |
| **Commercial variables** | Product prices, margins, supplier costs, and inventory availability are not included. | Financial feasibility and profitability of interventions cannot be directly evaluated. |

---

## 12. Future Extensions

| Priority | Extension | Objective |
|---|---|---|
| 1 | **Improve product matching** | Incorporate brand identifiers, UPC/barcodes, and additional product attributes to increase coverage. |
| 2 | **Introduce price-sensitive substitution analysis** | Identify healthier alternatives within comparable price ranges. |
| 3 | **Expand regulatory benchmarking** | Compare classifications against additional FDA guidance and sodium reduction targets. |
| 4 | **Develop a product prioritization score** | Combine purchasing volume, nutritional characteristics, substitute availability, and commercial variables. |
| 5 | **Validate recommendations through a retail pilot** | Measure actual changes in purchasing behavior, customer retention, and commercial performance. |

---

## 13. Team

**Boston University | Questrom School of Business**  
**BA780: Introduction to Data Analytics | Team A09**

Sanjana Tanna · Emily Zhao · Sienna Huang · Kavya Narayan · Vanshika Gupta

---

## 14. References

1. U.S. Department of Agriculture. [FoodData Central](https://fdc.nal.usda.gov/download-datasets). Branded Foods, April 2026.
2. Instacart. [Online Grocery Shopping Dataset 2017](https://www.kaggle.com/datasets/psparks/instacart-market-basket-analysis).
3. U.S. Food and Drug Administration. *Daily Value on the Nutrition and Supplement Facts Labels.*
4. U.S. Food and Drug Administration. *21 CFR 101.9, 101.54, 101.60, and 101.61.*
5. U.S. Food and Drug Administration. *Use of the Term "Healthy" on Food Labeling* (2024).
6. U.S. Food and Drug Administration. *Front-of-Package Nutrition Labeling* (Proposed Rule, 2025).
7. UK Department of Health and Food Standards Agency. *Guide to Creating a Front of Pack Nutrition Label* (2016).
8. Wickham, H. (2014). Tidy Data. *Journal of Statistical Software*, 59(10).

---

## 15. AI Usage Disclosure

Generative AI (Claude) assisted with coding, chart styling, and editing.

The team designed the analytical approach, validated the results, interpreted the findings, and developed the final recommendations.

Full disclosure is provided in the project notebook.

---

**Project takeaway:** Nutritional composition alone does not determine which products retailers should review first. Combining nutritional data with purchasing behavior provides a more actionable framework for product and aisle-level prioritization.
