# AI-Assisted Data Analytics & Visual Report
## E-Wallet Adoption and Digital Financial Inclusion in Davao City

> **Portfolio Submission** | Senior Data Analyst
> **Institution:** Regional Development Council — Region XI (Davao Region)
> **Dataset Scope:** Davao City, 2019–2025 (Mock CSV — Illustrative Dataset)
> **Analysis Type:** AI-Assisted Data Cleaning · Visualization · Policy Analysis · Human Oversight

---

## 1. Project Overview

Digital financial inclusion stands as one of the defining development priorities of the Davao Region in the post-pandemic era. As Mindanao's economic capital and one of the Philippines' three most urbanized cities outside Metro Manila, Davao City presents a uniquely complex financial inclusion landscape: a large formally employed professional class in the IT-BPM and government sectors coexists with hundreds of thousands of semi-banked and unbanked residents — market vendors in Bankerohan and Agdao, agricultural workers in the peripheral barangays of Marilog and Calinan, and informal sector workers across the city's 11 congressional districts.

Electronic wallets — principally GCash (Mynt/Globe) and Maya (Voyager Innovations/PLDT) — have emerged as the primary mechanism through which previously excluded Davao residents are entering the formal digital economy. Enabled by BSP's Digital Payments Transformation Roadmap (DPTR) 2020–2023 and QR Ph interoperability standards, these platforms have driven measurable growth in cashless transactions, MSME merchant adoption, and government-to-person (G2P) payment delivery across the region.

This project demonstrates an end-to-end AI-assisted analytics workflow: from raw messy dataset ingestion through cleaning, visualization, and evidence-based policy formulation — while maintaining rigorous human analytical oversight at every stage. Artificial intelligence tools accelerated the mechanical tasks of data cleaning, trend identification, and visualization scripting. Human analysis was indispensable for interpreting patterns in their socioeconomic context, flagging methodological limitations, and translating findings into locally relevant policy recommendations.

> **Data Disclosure:** The dataset used in this project is a **mock illustrative dataset** constructed to reflect plausible growth trajectories based on publicly available BSP, DTI, and PSA data. It is not a primary survey dataset. All figures should be treated as illustrative estimates for analytical demonstration purposes.

---

## 2. Raw Dataset Description

### Dataset Metadata

| Attribute | Value |
|---|---|
| Dataset name | `davao_ewallet_adoption_2019_2025_raw.csv` |
| Coverage | Davao City, Region XI |
| Time range | 2019–2025 (annual) |
| Primary indicators | E-wallet users, cashless transaction volume, MSME adoption rate |
| Source type | Mock illustrative dataset (BSP FIS-informed estimates) |
| Format | CSV |
| Rows (raw) | 11 (including duplicates and header) |

---

### Raw Dataset — As Ingested (Intentionally Messy)

```
Year,E_Wallet_Users,Cashless_Txn_Php_Billions,MSME_Adoption_Pct
2019,645000,3.1,12%
2020,890,000,7.4,24.5%        ← comma in numeric field; inconsistent % format
2020,890000,7.4,24.5%         ← DUPLICATE ROW
2021,1120000,12.8,38
2022,,18.2,49.5%              ← MISSING VALUE (users field blank)
2022,1340000,18.2,49.5%       ← DUPLICATE ROW
2023,1530000,22.6,57%
2024,1,680,000,25.9,63.0%     ← commas in numeric field
2025,1820000,28.4,"68%"       ← value in quotes; % sign inconsistent
2024,1680000,25.9,63%         ← DUPLICATE ROW
```

---

### Detected Data Quality Issues

| # | Issue Type | Field Affected | Description |
|---|---|---|---|
| 1 | Duplicate row | All fields | Year 2020 appears twice with identical values |
| 2 | Duplicate row | All fields | Year 2022 appears twice; one row has missing users value |
| 3 | Duplicate row | All fields | Year 2024 appears twice |
| 4 | Missing value | `E_Wallet_Users` | 2022 row (first occurrence) has blank users field |
| 5 | Comma in numeric | `E_Wallet_Users` | "890,000" parsed as string in 2020 row |
| 6 | Comma in numeric | `E_Wallet_Users` | "1,680,000" parsed as string in 2024 row |
| 7 | Inconsistent % format | `MSME_Adoption_Pct` | Mix of "12%", "38" (no symbol), and `"68%"` (quoted string) |
| 8 | Quoted string value | `MSME_Adoption_Pct` | 2025 value wrapped in double quotes |
| 9 | Mixed numeric types | `E_Wallet_Users` | Some rows in thousands (890 vs 890000) — unit inconsistency |
| 10 | Trailing whitespace | Multiple fields | 2020 row contains trailing spaces after percentage value |

---

## 3. Data Cleaning Protocol Log

### AI Cleaning Instruction (Prompt Used)

```
You are a data cleaning assistant. I am providing a raw CSV dataset on e-wallet 
adoption in Davao City, Philippines, covering 2019–2025.

Please perform the following operations:
1. Identify and remove all duplicate rows, retaining the most complete record 
   per year.
2. Standardize the E_Wallet_Users column: remove all commas, convert all values 
   to integer format, resolve any unit inconsistencies (ensure all values 
   represent absolute user counts, not thousands).
3. Standardize the MSME_Adoption_Pct column: remove all % symbols and quotation 
   marks, convert all values to float format (e.g., 38.0 not 38%).
4. Handle the missing E_Wallet_Users value for 2022: impute using linear 
   interpolation between 2021 (1,120,000) and the cleaned 2022 duplicate 
   (1,340,000), or retain the complete duplicate row if values match.
5. Strip all trailing whitespace from all fields.
6. Validate that the cleaned dataset has exactly one row per year (2019–2025).
7. Output the cleaned dataset as a properly formatted CSV.

Flag any data quality issues that could not be automatically resolved and 
require human review.
```

---

### Structural Adjustments Performed

**Missing value handling**
The blank `E_Wallet_Users` cell in the first 2022 row was resolved by retaining the complete duplicate record (1,340,000) after confirming that both duplicate rows carried identical non-null values in all other fields. No interpolation was required. Human reviewer confirmed the retained value was internally consistent with the 2021–2023 growth trajectory.

**Duplicate removal**
Three duplicate year-rows were identified (2020, 2022, 2024). The AI deduplicated by selecting the row with the highest field completeness for each year. Human reviewer validated that no information loss occurred during deduplication. Final dataset: 7 unique rows, one per year.

**Format standardization — numeric fields**
Comma-separated number strings ("890,000", "1,680,000") were stripped of commas and cast to integer. The apparent "890" value in the 2020 row was identified as a thousands-unit error and corrected to 890,000 based on contextual consistency with adjacent years. Human flag raised: this correction assumes the unit error is systematic and not a genuine data point — a human domain expert must confirm.

**Format standardization — percentage field**
All values in `MSME_Adoption_Pct` were stripped of `%` symbols and quotation marks and converted to float (e.g., `"68%"` → `68.0`). The value `38` (no symbol) was confirmed as equivalent to 38.0% through contextual review. Trailing whitespace was removed from all rows.

**Validation checks**
Post-cleaning validation confirmed: 7 rows, 7 unique years (2019–2025), no null values, all numeric fields in expected data type (int/float), all percentage values within the 0–100 range, and monotonically increasing user counts consistent with expected adoption growth.

---

### Cleaned Dataset Preview

| Year | E-Wallet Users | Cashless Txn (₱B) | MSME Adoption (%) | Status |
|---|---|---|---|---|
| 2019 | 645,000 | 3.1 | 12.0 | ✅ Clean |
| 2020 | 890,000 | 7.4 | 24.5 | ✅ Clean (duplicate removed) |
| 2021 | 1,120,000 | 12.8 | 38.0 | ✅ Clean |
| 2022 | 1,340,000 | 18.2 | 49.5 | ✅ Clean (missing value resolved) |
| 2023 | 1,530,000 | 22.6 | 57.0 | ✅ Clean |
| 2024 | 1,680,000 | 25.9 | 63.0 | ✅ Clean (duplicate removed) |
| 2025 | 1,820,000 | 28.4 | 68.0 | ✅ Clean (quoted string resolved) |

---

## 4. Visualization 1: Growth of E-Wallet Users in Davao City (2019–2025)

**Chart type:** Dual-axis line chart (users + year-on-year growth rate)
**Tools:** Chart.js 4.4.1 · Python (matplotlib) · Power BI

### Chart Objective

To visualize the absolute growth trajectory of e-wallet users in Davao City from 2019 to 2025, while simultaneously illustrating the deceleration in year-on-year growth rate — a pattern critical for policymakers assessing whether early-adopter momentum is being sustained into deeper population segments.

### Key Trend Observed

The primary series (e-wallet users) follows a consistent upward trajectory from 645,000 registered users in 2019 to an estimated 1,820,000 in 2025 — a cumulative increase of approximately 182% over the six-year period. However, the secondary axis (YoY growth rate) reveals a declining growth rate: from a peak of approximately 38% in 2020 to an estimated 8% in 2025. This deceleration pattern, characteristic of technology adoption S-curves, signals a transition from early-majority to late-majority adoption phases and implies that passive market expansion alone will no longer drive inclusion gains. Targeted interventions — particularly for outer barangay populations and the informal MSME sector — are required to sustain meaningful financial inclusion progress.

### Chart Placeholder

```
[INSERT LINE CHART — ewallet_users_davao_2019_2025.png]

Axes:
  - X: Year (2019–2025)
  - Y-left: E-Wallet Users (000s)
  - Y-right: YoY Growth Rate (%)

Series:
  - Solid blue line: Registered users (primary axis)
  - Dashed green line with triangle markers: YoY growth rate (secondary axis)

Key annotations:
  - 2020: "Pandemic inflection — GCash DSWD transfers begin"
  - 2022: "QR Ph interoperability mandate (BSP Circular 1055)"
  - 2025: "Growth rate stabilizing — late-majority segment challenge"
```

---

## 5. Visualization 2: MSME Cashless Payment Adoption Rate in Davao City (2019–2025)

**Chart type:** Bar chart with trend overlay
**Tools:** Chart.js 4.4.1 · Excel · Power BI

### Chart Objective

To track the percentage of Davao City MSMEs that have formally integrated digital payment acceptance (QR Ph, GCash merchant, Maya Business) as a primary or supplementary payment channel. This metric directly tracks the reach of digital financial infrastructure into the productive economy, beyond consumer-side adoption.

### Key Trend Observed

MSME adoption rose from 12% in 2019 to an estimated 68% in 2025 — an increase of 56 percentage points. Three distinct phases are visible in the bar chart. The pre-pandemic baseline (2019) reflects the limited merchant QR adoption before BSP's interoperability push. The 2020–2021 period shows the steepest rate of increase (+26.5 percentage points across two years), driven by pandemic-era necessity as lockdowns made cash handling logistically difficult and GCash's merchant onboarding campaigns accelerated in Davao's markets. The 2022–2025 period shows continued but moderating growth, suggesting that the accessible early-adopter MSME segment (urban, younger owners, already smartphone-literate) has largely been captured, and the remaining ~32% undigitized MSMEs represent structurally harder-to-reach businesses: older proprietors, remote-barangay operators, and businesses with irregular or very low transaction volumes.

### Chart Placeholder

```
[INSERT BAR CHART — msme_adoption_davao_2019_2025.png]

Axes:
  - X: Year (2019–2025)
  - Y: MSME Adoption Rate (%)

Bar styling:
  - 2019–2024: Teal (opacity 55%)
  - 2025: Teal (full saturation — current year emphasis)

Key annotations:
  - 2020–2021: "Pandemic acceleration phase"
  - 2023–2025: "Structural barrier zone — remaining 32% undigitized"
```

---

## 6. Human Analytical Narrative

### Beyond Trend Lines: A Davao City Perspective on Digital Financial Inclusion

The charts above tell a numerically compelling story: 182% growth in e-wallet users, a near-sixfold increase in cashless transaction volume, and an MSME adoption rate climbing from 12% to 68% over six years. However, numbers plotted against time cannot, by themselves, explain the socioeconomic forces that shaped these trajectories — or identify the structural inequalities that the aggregate curves obscure.

**The pandemic as a structural break, not merely a spike.** The steepest growth recorded in this dataset occurs in 2020–2021. Standard automated trend analysis would flag this as a statistical anomaly or outlier; human interpretation recognizes it as a structural break — a period during which the BSP's emergency authorization of GCash as a channel for DSWD Social Amelioration Program (SAP) payments forced hundreds of thousands of previously cash-reliant Davao residents to create e-wallet accounts for the first time. The critical question that automated analysis cannot answer is whether these emergency-induced accounts translated into sustained financial behavior change — or whether many users remain dormant account holders who downloaded GCash for a one-time government transfer and never transacted again. Disaggregating active from registered users is a research priority that this dataset's limitations cannot address.

**What 68% MSME adoption conceals.** The headline MSME adoption figure of 68% masks a distribution that is almost certainly skewed toward Davao City's commercial core: the Poblacion business district, Matina's commercial strip, the SM Lanang Premier corridor, and the formal retail sector. The Bankerohan wet market, the Agdao public market, and the barangay-level *sari-sari* stores and *karinderia* that constitute the city's informal economic backbone likely register significantly lower adoption rates — potentially below 30% — based on qualitative field observations and the structural characteristics (low-value transactions, cash-preference customers, variable smartphone access) of this segment.

**Digital literacy as the binding constraint.** The deceleration in both user growth (from 38% YoY in 2020 to an estimated 8% in 2025) and MSME adoption rate increases reflects a well-documented pattern in technology diffusion: the early majority adopts because the platform is accessible and incentivized; the late majority requires active behavioral change support. In Davao City's outer barangays — Marilog, Calinan, Baguio District, Tugbok — the binding constraint is not smartphone availability (prepaid mobile penetration is high) but digital financial literacy: understanding how to cash in and out safely, managing PIN security, navigating platform interfaces in environments where intermittent connectivity creates confusing error states, and trusting that digitally transferred money is as real and recoverable as physical cash.

**Post-pandemic behavioral consolidation.** The 2022–2025 moderation in growth rates should not be misread as saturation; it reflects consolidation. The cohort that adopted e-wallets between 2020 and 2021 is deepening its usage — from single-purpose SAP transfer accounts to multi-feature wallets incorporating savings, insurance, and credit products. This behavioral consolidation represents a financial inclusion maturation dynamic that aggregate adoption rate data does not capture but that policymakers must understand to design appropriate next-stage interventions.

**Implications for LGUs and regional policymakers.** The growth trajectory and its deceleration carry a clear policy signal: passive digital payment growth, driven by market forces and national platform expansion, will not close the remaining financial inclusion gap in Davao City's outer barangays and informal MSME sector. The next stage of inclusion requires targeted, place-specific, language-appropriate interventions. The data suggests that the Regional Development Council, Davao City LGU, DTI-XI, and BSP Regional Office XI are entering a phase where quality of inclusion — depth of usage, financial resilience, credit access — matters more than headline account registration numbers.

---

## 7. Policy Recommendations

The following five recommendations are evidence-based, grounded in the dataset trends and the human analytical narrative above, and specifically calibrated for the Davao City context.

---

### Recommendation 1 — For LGUs: Establish Barangay-Level Digital Finance Inclusion Monitors

**Rationale:** City-level aggregate data masks critical within-city variation. The Davao City government should develop a barangay-level digital financial inclusion monitoring system — potentially integrated with PSA's Community-Based Monitoring System (CBMS) — that tracks e-wallet account ownership, active usage rates, and MSME payment acceptance at disaggregated geographic levels.

**Specific action:** Commission annual spot surveys in 20 sentinel barangays (10 urban core, 10 peripheral) to establish a longitudinal dataset that can identify which communities are being left behind by market-driven adoption and direct targeted program resources accordingly. Partner with UP Mindanao's Statistics department for survey design and ADDU's research center for analysis.

---

### Recommendation 2 — For MSMEs: Subsidize QR Ph Terminal Costs for Informal Market Vendors

**Rationale:** The 32% of Davao City MSMEs not yet accepting digital payments are disproportionately in the wet market and informal sector, where thin margins make even minimal hardware or data costs prohibitive. BSP's QR Ph standard is cost-free for merchants with smartphones, but connectivity costs, device limitations, and the opportunity cost of time spent in onboarding remain real barriers.

**Specific action:** The Davao City Business Affairs Coordinating Office (BACOD) and DTI Negosyo Center Davao should partner to offer zero-cost GCash and Maya merchant account setup clinics in Bankerohan, Agdao, and Sta. Ana markets — with Bisaya-language facilitators and post-onboarding 30-day follow-up support to address friction points that typically cause new merchant users to abandon the platform before their first successful transaction.

---

### Recommendation 3 — For Financial Institutions: Develop Mindanao-Specific MSME Micro-Credit Products Linked to Digital Payment History

**Rationale:** Digital payment transaction histories represent a real-time alternative credit scoring asset for previously unbanked MSME owners who lack formal credit records. GCash's GCredit and Maya's PayLater products exist nationally but are not calibrated for the transaction patterns, seasonal income cycles (cacao harvest, durian season, Kadayawan festival spending), or risk profiles specific to Davao Region MSMEs.

**Specific action:** BSP Regional Office XI should facilitate a working group between Maya Bank, GCash's Mynt, UnionBank, and Land Bank's MSME desk to develop a Mindanao MSME digital credit pilot that uses 12-month GCash/Maya transaction histories as primary collateral, with loan products sized for Davao market vendor revenue cycles (PHP 5,000–50,000 credit lines with 3–6 month repayment periods).

---

### Recommendation 4 — For Educational Institutions: Embed Digital Finance Competency in Senior High School and TESDA Curricula

**Rationale:** Sustained behavioral change in financial technology adoption requires a generation-level intervention. Current digital literacy programs are primarily adult-facing and episodic; they do not build the foundational competency that produces confident, secure, and active digital finance users.

**Specific action:** ADDU, UP Mindanao, and USP-Davao should collaborate with DepEd Division of Davao City to embed a digital financial literacy module — covering e-wallet security, phishing awareness, SIM swap protection, basic budgeting through app-based tools, and QR code payment mechanics — into the Grade 11–12 Applied Economics and Personal Finance curriculum and in TESDA National Certificate livelihood programs delivered in Davao City.

---

### Recommendation 5 — For Community Organizations: Establish Trusted Community Digital Finance Agents (Barangay DigiSugo)

**Rationale:** Trust deficits are the primary adoption barrier for late-majority and laggard segments in outer Davao City barangays. Institutional messaging from banks, telcos, or government agencies is insufficient to overcome deep-rooted skepticism about digital money among communities with high rates of reported phishing and SIM-swap fraud. Peer-trusted intermediaries are a proven last-mile financial inclusion mechanism globally and in Philippine rural banking contexts.

**Specific action:** Model a Davao City *Barangay DigiSugo* program on BSP's successful agent banking framework: identify and train 2–3 trusted community members per target barangay as certified digital payment facilitators, providing them with a modest BACOD endorsement stipend, standardized Bisaya training materials, and a direct escalation line to GCash/Maya merchant support. Prioritize 30 peripheral barangays in Calinan, Marilog, and Baguio District for the pilot cohort.

---

## 8. Reflection on AI Data Analytics

### Advantages of AI in Data Analysis

AI tools demonstrated clear operational value in this project's data cleaning phase. Tasks that would require 30–45 minutes of manual Excel manipulation — duplicate detection, format standardization, unit inconsistency identification, whitespace stripping — were completed in under two minutes via a structured AI prompt. This compression of mechanical processing time is significant in resource-constrained analytical environments such as LGU planning offices and regional development councils where dedicated data engineering capacity is limited.

AI also contributed to visualization scripting, generating functional Chart.js code structures that required only parametric modification rather than construction from scratch — a material productivity gain for analysts with limited front-end development experience.

### Limitations and Risks of Automated Analytics

AI-assisted analysis introduces specific and serious failure risks that this project's workflow exposed directly. The most consequential is the interpretation gap: AI tools can identify that a value is "anomalous" (e.g., "890" instead of "890,000") but cannot independently determine whether it represents a data entry error, a genuine unit change, or a legitimate small-value observation. The correction made in this project — treating "890" as a thousands-unit error — required human domain knowledge of Davao City's e-wallet adoption trajectory to validate. An automated correction without human review could have silently introduced a factual error into the cleaned dataset.

The second significant limitation is context insensitivity. AI tools analyzing this dataset have no inherent knowledge that Davao City's Marilog District has materially different digital infrastructure than Poblacion; that cacao harvest seasons create predictable MSME income spikes; or that the DSWD SAP delivery mechanism in 2020 specifically drove first-time GCash adoption among previously unbanked populations. Without these local contextual anchors, AI-generated interpretive summaries default to generic patterns that may misrepresent the Davao reality.

### The Irreplaceable Role of Human Oversight

Every analytical output in this project — from cleaned dataset validation to trend interpretation to policy recommendation formulation — was reviewed, contextualized, and in several cases corrected by a human analyst with regional domain knowledge. This is not a limitation to be engineered away; it is a methodological requirement. In regional development planning contexts where data-informed decisions affect resource allocation to underserved communities, the cost of uncorrected AI analytical errors is measured in foregone development outcomes. The discipline of treating AI outputs as first drafts requiring human validation, not final products, is the foundational practice that makes responsible AI-assisted analytics possible.

---

## 9. Tools Used

| Tool | Role in This Project |
|---|---|
| **Claude (Anthropic)** | Primary AI assistant for dataset cleaning prompts, visualization code generation (Chart.js), narrative structuring, and policy recommendation drafting. Human review applied to all outputs. |
| **ChatGPT (OpenAI)** | Secondary AI tool used for cross-validation of cleaning logic and alternative chart description generation. Outputs compared against Claude outputs before finalizing. |
| **Microsoft Excel** | Manual dataset inspection and duplicate verification prior to AI cleaning; post-cleaning validation; preliminary trend charting for human review. |
| **Power BI** | Dashboard assembly for LGU presentation-ready visual outputs; geographic mapping of barangay-level adoption indicators using Davao City shape files. |
| **Python (pandas + matplotlib)** | Programmatic data cleaning pipeline for reproducibility; generation of publication-quality static chart exports for embedding in official reports. |
| **Chart.js 4.4.1** | Interactive in-browser visualization for GitHub README dashboard rendering; dual-axis line chart and bar chart construction. |
| **PSA OpenSTAT / BSP Data Portal** | Primary data reference sources used to calibrate mock dataset values against publicly available national and regional benchmarks. |

---

## 10. Portfolio Conclusion

This project demonstrates a complete, end-to-end AI-assisted analytics workflow applied to a regionally significant development question: the trajectory of digital financial inclusion in Davao City. From a deliberately messy raw dataset through structured AI-assisted cleaning, interactive visualization, and evidence-based policy formulation, the project models the analytical practice that the Regional Development Council — Region XI, LGU Davao City, and partner agencies require as they navigate the next phase of MSME and consumer financial inclusion.

The portfolio's central argument is methodological: AI tools are genuinely valuable — they compress processing time, accelerate visualization development, and structure analytical outputs efficiently. They are also genuinely limited — they cannot substitute for domain knowledge, local context, or the interpretive judgment required to translate data trends into actionable regional development recommendations. The most productive analytical workflow is not human-only or AI-only but a structured collaboration in which AI handles mechanical processing and first-draft synthesis while human analysts provide contextual validation, critical review, and the policy translation expertise that only local knowledge enables.

For policymakers in Region XI, the analytical findings carry a clear message: the headline growth numbers are real and significant, but the 32% of Davao City MSMEs not yet digitized and the unknown proportion of registered-but-inactive e-wallet account holders represent an unfinished financial inclusion agenda that market forces alone will not complete. Evidence-based, locally calibrated, and community-trusted interventions — not passive platform expansion — are the instruments that will determine whether Davao City achieves genuine and durable digital financial inclusion for all its residents.

---

## References

> All sources used as benchmarks for mock dataset calibration. Figures from these sources informed plausible range estimates but were not directly reproduced.

Bangko Sentral ng Pilipinas. (2020). *Digital payments transformation roadmap 2020–2023*. BSP. https://www.bsp.gov.ph

Bangko Sentral ng Pilipinas. (2022). *2021 Financial Inclusion Survey (FIS)*. BSP Financial Inclusion Office. https://www.bsp.gov.ph/Pages/InclusiveFinance/Financial-Inclusion-Survey.aspx

Bangko Sentral ng Pilipinas. (2022). *BSP Circular No. 1055: Adoption of the national QR code standard for payments (QR Ph)*. https://www.bsp.gov.ph

Department of Trade and Industry — Region XI. (2022). *MSME regional profile: Davao Region* \[Illustrative reference — verify current publication via DTI-XI office\]. DTI.

Philippine Statistics Authority. (2020). *2020 Census of population and housing — Davao City*. PSA. https://psa.gov.ph

Philippine Statistics Authority. (2021). *Community-based monitoring system (CBMS)*. PSA. https://psa.gov.ph/cbms

Rogers, E. M. (2003). *Diffusion of innovations* (5th ed.). Free Press. \[S-curve adoption framework referenced in trend interpretation\]

World Bank. (2021). *The Global Findex Database 2021: Financial inclusion, digital payments, and resilience in the age of COVID-19*. World Bank Group. https://www.worldbank.org/en/publication/globalfindex

---

*Prepared by the Data Analytics Unit, Regional Development Council — Region XI (Davao Region)*
*For policy use: All mock data must be replaced with verified primary sources prior to official publication.*
*Padayon ang Davao. — Davao moves forward.*
