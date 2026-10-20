# 🛒 Which Products Should Grocery Stores Review First?

### Finding the products that are both *unhealthy* and *popular*, by joining USDA nutrition data with 33.8 million Instacart purchases

**BA780 · Team A09**

---

## The 30-second version

Grocery stores are being asked to make their shelves healthier. But a store carries thousands of products, and a category manager can't review them all. So **where should they start?**

Our answer: **start where unhealthy food is actually bought.** A candy bar nobody buys doesn't matter much. A cheese everybody buys every week does.

| | |
|---|---|
| 🥫 **60%** | of packaged products are "high" in sugar, saturated fat or salt |
| 🧀 **10 aisles** | hold **59%** of all unhealthy purchases, led by packaged cheese, cream and lunch meat |
| 🔁 **60% vs 52%** | healthier products are bought again *more* often than unhealthy ones |
| 📉 **41% → 35%** | the unhealthy share of purchases if just 1 in 4 unhealthy buys in those aisles switched |

---

## The problem

The two facts a store needs live in two different places:

- **USDA FoodData Central** knows what is *inside* every packaged product, but not whether anyone buys it.
- **Instacart** knows what 206,209 shoppers *actually bought*, but not what is in it.

Neither one can answer "which products should we fix first?" on its own. **So we joined them.**

---

## The data

| Dataset | What it gives us | Size after cleaning |
|---|---|---|
| [USDA FoodData Central, Branded Foods](https://fdc.nal.usda.gov/download-datasets) (April 2026) | Sugar, saturated fat, salt and more, per 100 g | 442,097 products (from 2.0M label records) |
| [Instacart Online Grocery Shopping 2017](https://www.kaggle.com/datasets/psparks/instacart-market-basket-analysis) (all 6 files) | Who bought what, in which aisle, and whether they bought it again | 33.8M items in 3.35M orders |

**How we joined them.** The two datasets share no ID, so we built the same cleaned product-name key on both sides: lowercase, `&` → `and`, punctuation removed. Then we joined with ordinary pandas merges, using `validate=` checks on every join. **No AI was used to match records.**

Only 18.5% of products matched, but they're the popular ones: together they cover **36.9% of all packaged-food purchases**.

**What counts as "unhealthy"?** A product is "high" if it is above the UK front-of-pack limits per 100 g:

| Nutrient | "High" if more than |
|---|---|
| Sugar | 22.5 g |
| Saturated fat | 5 g |
| Salt | 1.5 g (600 mg sodium) |

Drinks use half these limits, per 100 ml. All our data is American; we borrowed the UK definition because the US has no final per-100 g standard. We also tested the FDA's per-serving rules: they flag **5%** of products as high in sodium, compared with **21%** under the UK rule.

---

## The analysis: 15 questions, in 5 steps

| Step | Questions |
|---|---|
| **1. The shelf**: what's unhealthy? | Q1 Which categories? · Q2 Which nutrient? · Q3 Do lower-salt versions exist? |
| **2. The suppliers**: who stands out? | Q4 Brands vs their category · Q5 Store brands vs name brands · Q6 Missing nutrition data |
| **3. The labels**: can we trust them? | Q7 Are claims like "low sodium" true? · Q8 UK vs FDA rules |
| **4. The shopper**: what actually gets bought? | Q9 Aisles · Q10 Baskets · Q11 Repeat purchases · Q12 Top products · Q13 Shopper groups |
| **5. The decision**: where to act? | Q14 Priority aisles and actions · Q15 Potential impact |

Statistical tests used along the way: chi-square, two-proportion z-test, binomial, McNemar, Wilcoxon and one-sample t-test.

---

## What we found

- 🍫 **The problem is concentrated.** Chocolate (99%), candy (98%) and cakes (96%) lead by share. But by volume, cheese, cream and lunch meat are where unhealthy buying actually happens.
- 🧂 **Each shelf has its own problem.** Jam is sugar, cold cuts are salt, cheese is several at once. 42% of unhealthy products break two or more limits.
- 🏷️ **Brands matter beyond their category.** Some brand owners are 65 points worse than their food mix predicts; others are 57 points better.
- 🔍 **Labels aren't perfect.** 17.5% of products are missing nutrition values entirely, and about 1 in 8 "low sodium" products don't meet the FDA definition.
- 👥 **Loyal shoppers drive volume.** Customers with 21+ orders are 24% of shoppers but buy 58% of unhealthy items. That's because they buy more, not because they choose worse.
- ✅ **Healthier is safe to push.** Healthier products are reordered more (59.6% vs 52.4%), and they win in 48 of 69 aisles.

---

## The recommendation

The 10 priority aisles split into two groups, depending on whether healthier options already sell there:

| 🟢 Promote healthier swaps | 🔴 Ask makers for healthier versions |
|---|---|
| Healthier products people already buy are on the same shelf | Almost no healthier options exist |
| Chips · Nuts · Other creams and cheeses · Oils | Cheese · Cream · Lunch meat · Butter · Spreads · Condiments |

**What it could achieve:** if 25% of unhealthy purchases in these aisles switched, the unhealthy share would drop from **41.2% to 35.1%**, about **492,000 fewer unhealthy purchases** in this data. *(Illustrative scenarios, not forecasts.)*

---

## How to run it

1. Open the notebook in **Google Colab** with a **high-RAM runtime**, or locally with about 16 GB of RAM.
2. Install the libraries (Colab has most of them already):
   ```bash
   pip install pandas numpy matplotlib scipy statsmodels kagglehub
   ```
3. **Run all.** The notebook downloads the USDA zip (about 449 MB) and reads it straight from the zip, without unzipping. It fetches the Instacart files (about 700 MB) with `kagglehub`. No accounts or API keys are needed.

Everything, from download to the final chart, runs top to bottom in one notebook.

---

## What's in this repo

| File | What it is |
|---|---|
| `A09_Which_products_should_Grocery_Stores_review_first_for_healthier_shelves.ipynb` | The full analysis: cleaning, joins, 15 charts, recommendations |
| `Ba780_FINAL_Which Products Should Grocery Stores Review First.pptx` | The presentation |
| `A09-Appendix.pptx` | Backup slides for Q&A: join method, cleaning, benchmarks and extra charts |

---

## Limitations (we're upfront about these)

- **Partial match.** Nutrition data covers about a third of packaged-food purchases.
- **Time gap.** The orders are from 2017; the labels are from 2019–2026. About 95% of recipes didn't change.
- **Definition choice.** "High" uses the UK per-100 g limits; the FDA per-serving rule flags fewer products.
- **Patterns, not proof.** We show where to look, not that the changes would improve health or sales.

**Next steps:** match on brand names or barcodes, add prices so swaps stay affordable, compare against FDA sodium targets, and pilot swaps in one aisle.

---

## Team A09

Sanjana Tanna · Emily Zhao · Sienna Huang · Kavya Narayan · Vanshika Gupta

*Generative AI (Claude) assisted with coding, chart styling and editing. The team designed the analysis, checked every result and wrote the conclusions. Full disclosure is at the end of the notebook.*

---

## Key references

- U.S. FDA, *Daily Value on the Nutrition and Supplement Facts Labels*; 21 CFR 101.9, 101.54, 101.60, 101.61
- U.S. FDA, *Use of the Term "Healthy" on Food Labeling* (2024) and *Front-of-Package Nutrition Labeling* (proposed, 2025)
- UK Department of Health & FSA, *Guide to creating a front of pack nutrition label* (2016)
- Wickham, H. (2014). Tidy Data. *Journal of Statistical Software*, 59(10)
