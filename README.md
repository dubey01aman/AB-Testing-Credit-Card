A/B Testing Case Study: Optimizing Credit Card Application While Controlling Fraud
---
A worked example of running and analyzing an A/B test for a bank's online credit card application, balancing conversion optimization against a fraud-risk guardrail.

Problem Statement
---
A leading bank observed that many customers were abandoning the online credit card application before completion.
Analysis showed that the existing application consisted of 5 pages, requiring users to enter detailed information at multiple stages.
The Product team proposed simplifying the application to 3 pages to improve customer experience and increase completion rates.
The Risk team was concerned that reducing friction could make it easier for fraudulent applicants to submit applications.

The bank therefore designed an A/B test with two objectives:
Increase the application completion rate.
Ensure fraud does not increase beyond an acceptable threshold.
Business Objective
> Increase completed credit card applications without increasing fraud risk.
Experiment Design
Group	Description
Control (A)	Existing 5-page application
Treatment (B)	Simplified 3-page application
Traffic split: 50% / 50% — each version receives 15,000 users.
Metrics
Primary Metric
Application Completion Rate
```
Completion Rate = Completed Applications / Started Applications
```
Business goal: increase completed applications.
Guardrail Metrics
Metric	Reason
Fraud Rate	Ensure fraud doesn't increase
Approval Rate	Maintain applicant quality
Customer Complaint Rate	Ensure customer experience remains positive
Application Error Rate	Verify technical stability
> This case study evaluates the **Fraud Rate** guardrail in detail.
Data
Primary Metric — Applications
Version	Started Applications	Completed Applications
A (Control)	15,000	1,800
B (Treatment)	15,000	2,040
Guardrail Metric — Fraud (post-investigation)
Version	Fraudulent Applications
A (Control)	18
B (Treatment)	25
Raw data used for the analysis lives in `data/experiment_data.csv`.
Analysis
All calculations below are reproduced programmatically in `scripts/ab_test_analysis.py`.
Completion Rate
Version	Completion Rate
A	12.0%
B	13.6%
Absolute improvement: 1.6 percentage points
Relative lift: 13.33%
Hypothesis Testing
H₀: No difference in completion rate between A and B.
H₁: The new (3-page) application improves completion rate.
Using a two-proportion Z-test:
Pooled proportion: p̂ = (1,800 + 2,040) / (15,000 + 15,000) ≈ 0.128
Standard error computed from the pooled proportion across both groups
Z-score: Z = 4.15
Critical value (95% confidence, one-tailed): 1.645
Since 4.15 > 1.645, we reject H₀ — the increase in completion rate is statistically significant.
Guardrail Metric: Fraud Rate
```
Fraud Rate = Fraudulent Applications / Completed Applications
```
Version	Fraud Rate
A	1.00%
B	1.23%
Absolute increase: 0.23 percentage points
Relative increase: 23%
Business Discussion & Decision
Although more customers completed the application, the fraud rate also increased. The Product Manager, Risk Team, and Business Team must jointly decide whether this trade-off is acceptable.
Bank policy: Fraud Rate must remain below 1.5%.
Since 1.23% < 1.5%, the guardrail remains within the acceptable threshold.
Production Rollout Decision
Metric	Result	Decision
Completion Rate	✅ Improved significantly	Pass
Fraud Rate	✅ Increased slightly but below threshold	Pass
Customer Experience	Improved	Pass
Final Recommendation
Roll out the 3-page application to all users, because:
Application completion improved by 13.3%.
The increase was statistically significant (Z = 4.15).
Fraud increased only from 1.00% to 1.23%, remaining below the bank's acceptable limit of 1.5%.
The projected increase in completed applications outweighs the small increase in fraud, provided fraud continues to be monitored after launch.
Business Impact
Assuming the treatment is rolled out to all traffic:
Metric	Value
Monthly website visitors	1,000,000
Current completion rate	12% → 120,000 completed applications
New completion rate	13.6% → 136,000 completed applications
Additional applications / month	16,000
Assumptions: Approval Rate = 30%, Profit per approved card = ₹8,000
Metric	Value
Additional approved customers	16,000 × 30% = 4,800
Additional monthly profit	4,800 × ₹8,000 = ₹3.84 crore/month
Repository Structure
```
ab-testing-credit-card/
├── README.md                     # This file
├── LICENSE
├── requirements.txt
├── data/
│   └── experiment_data.csv       # Raw experiment counts (A/B, started/completed/fraud)
└── scripts/
    └── ab_test_analysis.py       # Reproduces completion-rate, Z-test, and fraud-rate calcs
```
Reproducing the Analysis
```bash
# 1. Clone the repo
git clone https://github.com/<your-username>/ab-testing-credit-card.git
cd ab-testing-credit-card

# 2. Install dependencies
pip install -r requirements.txt

# 3. Run the analysis
python scripts/ab_test_analysis.py
```
The script prints the completion rate, lift, Z-score/p-value, and fraud-rate comparison, and checks both against the stated business decision rules (95% confidence, 1.5% fraud ceiling).
Key Takeaways
A primary metric (conversion) should almost never be evaluated in isolation — pair it with guardrail metrics that protect against unintended harm (here, fraud).
Statistical significance (Z = 4.15) answers "is the effect real?" — it does not answer "should we ship it?". That second question requires a business threshold (fraud < 1.5%) decided in advance.
Pre-registering guardrail thresholds before running the experiment avoids post-hoc rationalization when a metric moves in the "wrong" direction.
License
This project is released under the MIT License. The scenario and figures are illustrative/synthetic and created for educational purposes.
