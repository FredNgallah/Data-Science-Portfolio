# 🏦 Case Study: Conservative Credit Scoring for Agri-SMEs
**Objective:** Minimize capital risk in Kenyan agricultural lending through predictive modeling.

## 🔍 The Business Problem
Standard lending models often prioritise volume, resulting in high default rates. Our "Training" data initially showed 2 false negatives—cases in which the model predicted "Pay" but the borrower defaulted. For a conservative bank, even a 5% error rate on defaults is unacceptable.

## 🛠️ The Technical Solution
Using **Nominal Logistic Regression** in JMP, I analyzed five key variables to determine the probability of default.

### Key Performance Drivers:
* **DTI_Ratio:** Risk remains low until 0.25 (25%), then spikes vertically.
* **Txn_Consistency:** Variance in revenue above 0.15 is a primary trigger for default.
* **Payment History:** "No Lates" is the strongest anchor for a safe profile.

## 🛡️ The "Conservative" Optimization
By setting the **Desirability** for Default (1) to zero and Repayment (0) to one, I identified the "Safe Haven" profile:
* **Optimal DTI:** ~0.15
* **Optimal Revenue Variance:** < 0.04
* **Resulting Default Probability:** **2e-21** (Virtually Zero).

## 🚀 Deployment & Operationalization
I exported the JMP prediction formula into an automated **Google Sheets Credit Engine**.
* **Strict Threshold:** Loans are only approved if the predicted probability of default is **< 10%**.
* **Result:** This stricter threshold successfully filters out the "False Negatives" identified during model training.
---
## 📊 Full Model Analysis (JMP 18)
The following report shows the logistic regression fit, where we achieved an **RSquare(U) of 0.884** and identified the critical risk triggers for the portfolio.

![Full Model Results](./model_results_full.png)

---
## 🛠️ Live Deployment: Automated Underwriting Tool
To demonstrate the operational value of this model, I translated the JMP Logistic Regression formula into a functional **Google Sheets Credit Scorecard**. 

* **How it works:** When a new SME's data (DTI, Payment History, etc.) is entered, the sheet automatically calculates the probability of default using the model's coefficients.
* **The Decision Engine:** The system applies the **10% Conservative Threshold** to instantly flag applicants as "APPROVE" or "REJECT."

🔗 **[View the Live Credit Scorecard here](https://docs.google.com/spreadsheets/d/1I13uLEvRGlLYLWJVpH0Jy8CS_J3HZ0PZuJTS-6somhQ/edit?gid=1421198885#gid=1421198885&range=A1:L121)**

Technical Notes:
**1. Parameter Stability** The 'Unstable' markers in the parameter estimates are a result of Perfect Separation in the training data (i.e., all borrowers with 3+ Lates defaulted). While this can inflate standard errors, it confirms the high predictive power of these categorical variables in identifying absolute risk zones.
**2. Factors Calculations**
* **DTI Ratio (Debt-to-Income):** * *Formula:* `(Existing Monthly Debt Obligations / Average Monthly Gross Revenue)`
  * *Logic:* I used a **Random Normal Distribution** centered at **0.25** for "Safe" sectors (Grains) and **0.40** for "Volatile" sectors (Fruit), showcasing the higher credit dependency in perishables.
* **Transaction Consistency:** * Calculated as the **Coefficient of Variation (CV)** of the last 6 months of digital payments. 
  * *Logic:* High CV (>0.15) signals seasonal instability, a primary driver in our Logistic model.
* **Payment History:** * Assigned based on a weighted probability—SMEs with a **Credit Age > 5 years** were 70% less likely to have "3+ Lates" in the simulation.
