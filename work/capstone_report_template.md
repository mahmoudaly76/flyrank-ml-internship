# Capstone Report — Structured Content Archetype Clustering

- **Author:** Mahmoud Aly Fawal
- **Lane:** Lane 3: Structured Content Archetype Clustering
- **Repo:** https://github.com/mahmoudaly76/flyrank-ml-internship
- **Date:** October 14, 2026

# Capstone: Real Estate Digital Portfolio Management
**Lane 3: Structured Content Archetype Clustering**

## 0. Abstract
How can we automate digital asset management for a massive real estate portfolio to maximize search traffic ROI? Using the FlyRank ML Internship dataset, we analyzed the search performance of 50,000 property and content pages. We applied an unsupervised K-Means clustering model to segment these pages based on log-transformed impressions, CTR, and average position. The model successfully isolated four actionable performance archetypes, including 697 high-visibility "Traffic Titans" that suffer from poor conversion. This output provides SEO and marketing teams with a prioritized, automated playbook to Protect, Improve, Refresh, or Prune digital assets.

## 1. Problem framing
* **Decision supported:** Allocating SEO and digital marketing resources (budget and copywriting hours) efficiently across a massive portfolio of real estate pages.
* **Unit of analysis:** The individual web page (URL).
* **Output:** A segmented cluster assignment (4 archetypes) for each page.
* **Action:** A human editor uses this to decide whether to Protect, Improve (change meta-titles), Update (refresh prices/content), or Prune (delete/redirect) a page.
* **Cost of a wrong call:** Wasting copywriting budget on dead pages (sold-out projects) or accidentally deleting/altering a high-performing "Golden" asset, causing a drop in leads.
* **Why ML helps:** Manual audits using rigid heuristic rules fail to capture the nuanced relationships between impressions, CTR, and ranking across tens of thousands of pages.

## 2. Data safety
* **Data used:** `fact_content_daily_performance` parquet files from the FlyRank warehouse via DuckDB.
* **Excluded data:** Pages with fewer than 100 total impressions were deliberately excluded to remove statistical noise (zero-click anomalies) and focus on assets with sufficient search presence.
* **Leakage risks:** Since this is an unsupervised clustering task, no future-window target labels were used. Pseudonymous IDs (`client_hash_id`) were ignored, and the model was trained strictly on aggregated performance metrics.
* **Safety confirmation:** Aggregated at the URL hash level. No client names, raw search queries, URLs, domains, or PII appear anywhere in the `/work` directory.

## 3. Baseline
* **The baseline:** A transparent, static rules-based heuristic (e.g., IF impressions > 5,000 AND CTR < 0.02 THEN "Needs Review"). 
* **Why it's fair:** This is the standard industry practice for SEO audits (using fixed filters in Excel or Google Search Console). 
* **Comparison:** On this 50k dataset, the baseline produces a massive, flat list of URLs that requires manual sorting. In contrast, our K-Means model evaluates the multi-dimensional distance between assets, resulting in a prioritized, segmented portfolio rather than a binary "pass/fail" list.

## 4. Model / analysis
* **Method:** Unsupervised Machine Learning (K-Means Clustering). It perfectly fits Lane 3 (Structured Content Archetype Clustering).
* **Feature list:** `log_impressions` (log-transformed to prevent extreme outliers from skewing distances), `ctr` (clicks/impressions), and `avg_position`.
* **Left out on purpose:** Raw impressions were excluded (replaced by log) because a few mega-pages would dominate the variance and collapse the clusters.
* **Target definition:** An unsupervised segmentation of digital asset health into 4 distinct business archetypes.

## 5. Evaluation
* **Split:** The model was trained and evaluated on the full 50,000 aggregated URL dataset (unsupervised segmentation does not require a train/test split for prediction).
* **Model vs Baseline:** The baseline heuristic groups massive-impression/low-CTR pages together with low-impression/low-CTR pages as simply "Bad". The K-Means model successfully separated them, finding a distinct cluster of 697 "Traffic Titans"—pages with massive reach but poor conversion.
* **Errors / Limitations:** The model is directional and observational. A well-understood limitation is that it ignores off-platform real estate seasonality (e.g., summer vacation homes peaking in June) which could explain CTR drops, not necessarily content decay.

## 6. Interpretation
The K-Means model clustered the portfolio into 4 clear profiles:
1. **Cluster 2 (Golden Assets - 2,976 pages):** High CTR, moderate-high visibility. These are converting well.
2. **Cluster 3 (Traffic Titans - 697 pages):** Massive impressions but extremely low CTR. 
3. **Cluster 0 (Underperformers - 21,742 pages):** Moderate visibility, decaying engagement.
4. **Cluster 1 (Dead Assets - 24,585 pages):** Low visibility, low CTR.
* **Surprise:** The "Traffic Titans" group was surprisingly small (just 1.4% of the portfolio) but holds the highest immediate ROI potential for the business.

## 7. Recommendation
Here is the ranked action playbook for a FlyRank editor or Real Estate marketing team:
1. **Priority 1 - Improve (The 697 Traffic Titans):** Export this list to copywriters immediately. Add high-intent modifiers to meta-titles (e.g., "Installments", "Ready to Move") to capture the massive demand already seeing these pages.
2. **Priority 2 - Refresh (The 21k Underperformers):** Schedule for Q2 content updates. Refresh property prices, images, and delivery dates to signal content freshness to search engines.
3. **Priority 3 - Prune (The 24k Dead Assets):** Bulk archive or 301-redirect these pages (likely sold-out projects) to category pages to conserve crawl budget.
4. **Ongoing - Protect (The 2k Golden Assets):** Do not alter core URLs or headings. Monitor for any drops.
* **Confidence & Limits:** High confidence in cluster distinctness, but actions are decision-support, not guaranteed causal fixes.

## 8. Reproducibility
To re-run this analysis from a fresh clone:
1. **Environment:** `pip install duckdb pandas scikit-learn seaborn matplotlib`
2. **Execution:** Open `work/notebooks/capstone.ipynb` and Run All Cells.
3. **Random Seed:** The K-Means model uses `random_state=42` and `n_init=10` to guarantee identical cluster assignments on every run.
4. **Output:** The run will automatically generate the clustered CSV output in `work/outputs/capstone_clustered_portfolio.csv`.

## 9. Acknowledgments & data credit
Built on the FlyRank ML Internship dataset.
Explore the data and platform at [https://flyrank.ai](https://flyrank.ai)
