# Propensity to Pay Modelling

Propensity to pay modelling for Starter and Non-starter accounts.

---

## 📌 Problem Statement
Snapshot data for charged-off loans in terms of collections and the communications made to them was provided and the aim was to create propensity to pay models for two sections in this data : starter and non-starter accounts. Starter accounts were the ones where a payment was made on or before 31st August, 2025 and similarly, Non-starter accounts were the ones where no payment was made before this period. A propensity to pay meant that at least one payment by the account would be made in the near future and so, the target variable was derived depending on whether at least one payment was made by these accounts in their life cycle or not.

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

### Models / algorithms
## Non-starter model :
The Non-starter model had sufficient data but was massively imbalanced in terms of payment. Only 173 had made a payment out of around 1.5 lakhs which accounted for only 0.1%. And thus, all models tried were very high in terms of Train/Test set AUC but it was important to select the right algorithm in terms of the features the model used.
- Logistic regression : 


### 
### Assumptions
### Final Performance and Testing

### Things that could have been tried
- Additional Features
- Grid search hyper-parameter tuning
- Transformations like scaling

## 📂 Project Structure
Explain important files and folders.

## ⚙️ Installation & Setup
How to run this project locally.

## ▶️ Usage
How to execute the code.

## 📊 Results
Metrics, outputs, plots, or conclusions.

## 🧪 Experiments
(Optional) Hyperparameters, ablations, comparisons.

## 🚀 Future Work
Improvements you would make.

## 👤 Author
Your name, course, institution.

## 📄 License
(Optional)
