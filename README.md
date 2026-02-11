# Propensity to Pay Modelling

Propensity to pay modelling for Starter and Non-starter accounts.

---

## 📌 Problem Statement
Snapshot data for charged-off loans in terms of collections and the communications made to them was provided and the aim was to create propensity to pay models for two sections in this data : starter and non-starter accounts. Starter accounts were the ones where a payment was made on or before 1st August, 2025 and similarly, Non-starter accounts were the ones where no payment was made before this period. A propensity to pay meant that at least one payment by the account would be made in the near future and so, the target variable was derived depending on whether at least one payment was made by these accounts in their life cycle or not.

## 🎯 Data available
In terms of features, repayment snapshot data was available for all the accounts. Last payment amount, Principal amount, Current outstanding, total payments, chargeoff date, last payment date, age, etc. which gave an indication of where an account was in terms of repaying at regular intervals and the longetivity of the collection process. In addition to this, data regarding the calls made to them and the interactions had was also provided, i.e, the frequency of calls made, calls connected and what was said on these calls in terms of repayment, etc. This set had around 1.5 lakh loan ids.
Another dataset regarding communications like SMS and Emails sent to some of these loans was also provided. This data was transactional in that emails sent, delivered and read were recorded. Similarly, SMS'es sent and delivered were recorded. This data had around 16k loans which formed a subset of the 1.5 lakh loans.

## 🧠 Approach / Methodology
### Feature Creation
In terms of communication, features with a view to explaining the frequency of communication as well as the reach were created. Along with this, a correlation to what was said in response by these individuals was also sought and so a lot of the focus was on creating communication vintages, delivery rates, read rates and response rates. Below are these features :
- was_email_used : was email used for communication or not
- was_sms_used : was sms used for communication or not
- communication_vintage : time intervals for which communications were sent to each loan id
- days_since_last_sms_delivered : no of days from 1st August, 2025 since the last sms was delivered for each loan
- days_since_last_email_read : no of days from 1st August, 2025 since the last email was read
- days_since_last_contact : no of days from 1st August, 2025 since any kind of contact was made last
- email_sent_delivered_ratio : ratio of times email delivered to times email sent
- email_delivered_read_ratio : ratio of times email read to times email delivered
- sms_sent_delivered_ratio : ratio of times sms delivered to times sms sent

Then, based on the interactions had with these individuals or the attempts made to do so through calling, the following features were created to best understand the level of communication that was achieved and also the kind of response recorded :
- connect_rate : % of times a call-connection was made out of the times the number was dialed
- rpc_rate : % of times the call connected to was a right party contact
- ptp_rate : % of times the call connected to promised to pay the outstanding
- drop_rate : % of times call was dropped out of the times a number was dialed
- days_bw_last_notice_last_pmt : no of days between last notice sent and last payment amount

Finally, features that represented historical repayment patterns as well as collection vintages were created. These were created with a view to depict the willingness to pay over time as well as to understand the scenarios for the ones who pay as opposed to the ones who don't. Features like last payment amount were also used to incorporate recent payment patterns. Following are the features :
- days_since_last_payment : no of days between the reference date and the last payment date
- days_since_chargeoff : no of days between chargoff date and reference date
- days_bw_chargeoff_last_pmt : no of days between chargoff date and last payment date
- last_payment_to_balance : last payment amount over current outstanding - compares most recent amount paid back vs yet to be paid
- total_payment_to_original : total paid vs original balance - compares total amount paid back vs principal amount to be paid
- balance_ratio : current outstanding vs original balance - indicates the proportion of amount that is yet to be paid
- principal_reduction : indicates reduction in the principal as compared to the current outstanding
- principal_reduction_ratio : indicates % reduction in the principal as compared to the current outstanding
- no_balance_reduction_flag : flag for ids where payments have been made but principal has not reduced at all

Assuming non-starters have made little to no payment which was also reflected in the data as the most of the features derived through payment amounts had mostly zeroes in them and so these features were excluded from the model along with features made on last payment date as non-starters do not have a last payment date.

### Transformations/Cleaning
Age was clipped from 20 to 65 and ages outside that range were unrealistic.
Communication features had a very low fill-rate but that could have been SMS/Email communications could have been sent only a fraction of the total loan ids. In such a scenario, flag features like was_sms_used and was_email_used could be helpful as they give the model this information that an attempt to communicate was not made.
For other features, null values were filled with 0's since most of them were ratios or percentages.

### Models / algorithms
## Non-starter model :
The Non-starter model had sufficient data but was massively imbalanced in terms of payment. Only 173 had made a payment out of around 1.5 lakhs which accounted for only 0.1%. And thus, all models tried were very high in terms of Train/Test set AUC but it was important to select the right algorithm in terms of the features the model used.
- Logistic regression : Through cross-validation an avergae AUC of 0.91 and a standard deviation of 0.08 was observed. In terms of feature importance, this model heavily relied on balance_ratio, email_delivered_read_ratio as well as call-rates like connect_rate, rpc_rate, ptp_rate, etc. Their directionalities made intuitive sense as well. While call-rates were directly propotional to the odds of paying, balance_ratio and email_delivered_read_ratio were inversly proportional to the same.
- In case of other models like Decision Tree, Random Forest, XGBoost, etc., the scenario was that the AUCs were pretty high but the models were influenced by one feature, namely, balance_ratio. Parameter tuning as well as excluding this feature from the model did not help, as the models then overly relied on other features.
So, keeping all of this into account, the logistic regression model was used for it's extensive use of all the features available as well as their correct use in terms of directionality.

## Final model
The final logistic regression model was created after tuning parameters as well as excluding features with high multi-collinearity with each other. A check was also made for each feature's directionality in the model and since, all the features were in sync in terms of intuitive sense, none of them were removed with regards to this.

The following features had a VIF>5 and they were iteratively removed :

<img width="332" height="191" alt="image" src="https://github.com/user-attachments/assets/85b1de5c-7b52-4baf-b0f2-66962c281849" />


The following features used in the model were found to be highly correlated to each other :

<img width="831" height="316" alt="image" src="https://github.com/user-attachments/assets/7633c218-50c9-4385-b61f-bc32674f36fe" />

Also, to check for feature importance in the model, shap values and Wald-chi were calculated. Below is a table showing these metrics along with the model co-efficients :

<img width="1068" height="660" alt="image" src="https://github.com/user-attachments/assets/f46879d1-0b80-4b5b-8405-f8172351a460" />

Looking at the co-efficients, the shap-values and the Wald-chi for the highly correlated features, days_since_last_email_read and email_sent_delivered_ratio were removed since the magnitude of these co-efficients were lower as compared to their corresponding highly correlated features. And in terms of the VIF, communication_vintage and was_sms_used were iteratively removed.

In terms of parameter tuning, L1, L2 as well as mix of the two techniques were used but that did not really have an impact on the model metrics. And so, below are the final metrics for model performance :

<img width="627" height="142" alt="image" src="https://github.com/user-attachments/assets/59fe7796-4de9-4b8b-90a1-043888a57289" />

Below is the final table for model co-efficients and importance :

<img width="1060" height="486" alt="image" src="https://github.com/user-attachments/assets/f526b147-8f7e-4f00-a10b-70347c6b5155" />

SHAP summary plot :

<img width="1022" height="756" alt="image" src="https://github.com/user-attachments/assets/dccc7c5e-8cc3-4a61-a5ae-6058778840a6" />

Looking at the shap values and model co-efficients, we can see that the balance_ratio, age, connect_rate, rpc_rate, ptp_rate and was_email_used are very influential. In terms of directionality, was_email_used is inversly proportional which might indicate that emails were used for sections that were more unresponsive in terms of payment.

## Starter model :
Only ~500 loans were starter accounts and so, data for training was very less. In such a situation, using simpler models is more straightforward than using more complex models. Models like Logistic Regression, Decision Tree and Random Forest were heavily influenced by only a couple of features, namely, days_since_last_payment, total_payment_to_original, days_bw_last_notice_last_pmt and days_bw_chargeoff_last_pmt. Using a multiple tree model here would have been effective as results are aggregated over those trees and weights are more spread out across the features. This was observed in the Random Forest model but a lot more in the XGBoost model and so given that there was no overfitting between the train and test sets, the XGBoost model was selected.

Manual parameter tuning was done. Since, XGBoost is a complex multiple-trees based model, keeping it as simple as possible in this context was important, otherwise, it would lead to overfitting in such a small set. A max_depth of 2 along with very low proportions of subsample and colsample_bytree was required so that the model generalizes well by training trees on smaller proportions of records as well as features. Below are the final model performance metrics :

<img width="640" height="148" alt="image" src="https://github.com/user-attachments/assets/fbc8fadc-9f5e-49e6-bd60-5f9aea532080" />

Feature Importances :

<img width="322" height="871" alt="image" src="https://github.com/user-attachments/assets/4593e4a7-8259-4751-86be-5dd19b16b9cb" />

### Final Performance and Testing
## Non-starter model :
Metrics like Accuracy, Precision, Recall and Specificity were calculated at every probability cut-off from 0 to 100 for both test and train sets to check performance. Below are the results :
Train :

<img width="1305" height="102" alt="image" src="https://github.com/user-attachments/assets/8e16d7aa-6f62-48aa-ba85-a40e16c9346c" />

Test :

<img width="1305" height="105" alt="image" src="https://github.com/user-attachments/assets/5a09cb09-f316-4b29-bc06-cb485a46ccd7" />

A probability cut-off of 22% is recommended as threshold because at a Precision of around 65%, we were able to capture around 34% of the ones who paid whilst also capturing most of the ones who did not pay. Precision over Recall should be important in this scenario as we cannot False Positives to be higher because that might mean de-prioritization of accounts that are not going to pay which in turn, might lead to more losses because of less repayment pressure.

## Starter model :
Another way of evaluating the starter model was to create a propensity to pay score from 0-1000. The model probabilities were scaled to this range and bucketwise cumulative paid and not-paid populations were calculated to understand bin-wise behaviour as well as overall score wise accumulation of paid and not-paid. This would give us an idea of the threshold at which the KS (separation between the classes) could be the highest. Below is the table for the test-set :

<img width="1182" height="302" alt="image" src="https://github.com/user-attachments/assets/4bd924af-aaa3-4295-8801-ee5015ffc03d" />

Overall-set :

<img width="1215" height="321" alt="image" src="https://github.com/user-attachments/assets/42e9846a-106d-43c1-8d2d-20aa3dc1ba5f" />

The performances between the two-sets is similar and we can a good separation around the 500 mark which is where the KS-value is highest as well. Beyond 500, we can see that around 60% of the paid population lies whereas below 500, around 90% of the unpaid population lies. Below is the KS-curve for a better representation :

<img width="901" height="628" alt="image" src="https://github.com/user-attachments/assets/f2cc273b-6af6-490d-9bcf-1f42703fb320" />


### Things that could have been tried
- Additional Features : compare vintage of loans vs the current outstanding (loan charged off in the last 6 months and % of principal paid back vs loans charged off in the last 1 year vs % of principal paid back)
- Extensive EDA for correlation analysis
- Feature selection techniques like RFE
- Class imbalance techniques like SMOTE
- Grid search hyper-parameter tuning
- Transformations like scaling

## 👤 Author
Mitul Desai
