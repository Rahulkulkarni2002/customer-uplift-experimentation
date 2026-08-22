# Uplift Modeling: Email Campaign Causal Analysis

## Business Question
Did the email marketing campaigns actually cause customers to visit, convert, and spend more — and if so, which specific customers should be targeted (or excluded) in future campaigns to maximize revenue impact?

## Dataset
[Kevin Hillstrom's MineThatData E-Mail Analytics dataset](http://www.minethatdata.com/Kevin_Hillstrom_MineThatData_E-MailAnalytics_DataMiningChallenge_2008.03.20.csv) — 64,000 customers who purchased within the last 12 months, randomly assigned to one of three groups: Mens E-Mail, Womens E-Mail, or No E-Mail (control). Outcomes tracked over the following two weeks: visit, conversion, spend.

## Why This Project Is Different From Standard Prediction
Most ML projects predict an outcome directly ("will this customer convert?"). This project instead estimates a **causal effect** — "did the email *cause* this specific customer's behavior to change, versus what they would have done anyway?" This requires genuine treatment/control experimental data (which this dataset provides) and different modeling and evaluation techniques than standard classification.

## Methodology
1. **Randomization check** — verified customer attributes (recency, purchase history, newbie status) were statistically balanced across treatment and control groups *before* the campaign, confirming any later differences can be attributed to the email itself.
2. **T-learner (two-model approach)** — trained separate models on treated vs. control customers for each outcome (visit, conversion, spend), then calculated each customer's individual uplift score as the difference between their predicted outcomes under each scenario.
3. **Cross-validation** — built a second, independent uplift model (ClassTransformation) to test whether findings held up across methods, rather than trusting a single technique.
4. **Statistical validation** — bootstrapped confidence intervals (1,000 resamples) on the headline revenue estimates, to report a defensible range rather than a single point figure.
5. **Customer segmentation** — classified customers into Persuadable, Sure Thing, Lost Cause, and Sleeping Dog segments using data-driven thresholds (quartiles of the actual score distributions), based on revenue uplift specifically.

## Key Findings

**1. Both campaigns generated real, statistically validated incremental revenue.**
- Mens campaign: $0.77/customer average uplift, $32,748.77 total estimated incremental revenue (95% CI: $32,568.59–$32,921.00)
- Womens campaign: $0.42/customer average uplift, $18,119.77 total estimated incremental revenue (95% CI: $17,961.65–$18,279.65)
- Both results independently matched the real published Hillstrom benchmark figures, confirming the modeling approach produced legitimate, accurate estimates.

**2. A surprising early result held up under scrutiny.** The Mens campaign's T-learner initially showed a suspiciously clean 99.998% of customers with positive uplift. Rather than accepting this at face value, a second independent method (ClassTransformation) was built to test it — both methods agreed closely, turning an initial red flag into a validated finding.

**3. The Womens campaign has a genuine negative-revenue segment; the Mens campaign does not.** After correctly aligning customer segments with the revenue outcome (an earlier version of this analysis mistakenly defined segments using visit uplift and plotted spend uplift — caught and corrected), the Womens campaign's "Sleeping Dog" segment (~10% of customers) shows a clear **negative** average revenue impact (-$0.20/customer). The Mens campaign's weakest segment remains net-positive ($0.37/customer).

## Business Recommendations
- **Mens campaign**: continue targeting broadly — even the lowest-performing segment shows positive revenue impact.
- **Womens campaign**: target the Persuadable segment (~25% of customers) and explicitly **exclude the Sleeping Dog segment (~10%)** — sending this group the campaign is predicted to actively reduce revenue, not just fail to help.
- Avoid targeting "Sure Thing" customers with either campaign where possible — they convert regardless of treatment, making the campaign spend on them unnecessary.

## Files
- `uplift.ipynb` — full analysis: data inspection, T-learner, cross-validation, segmentation, visualizations
- `hillstrom.csv` — raw dataset
- `segment_comparison.png`, `uplift_by_segment.png` — analysis visuals

## Tools & Skills Demonstrated
Python, pandas, scikit-learn (Logistic/Linear Regression), scikit-uplift (ClassTransformation), causal inference and experimental design, bootstrapped statistical inference, matplotlib.
