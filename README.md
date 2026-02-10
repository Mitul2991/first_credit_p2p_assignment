# Propensity to Pay Modelling

Propensity to pay modelling for Starter and Non-starter accounts.

---

## 📌 Problem Statement
Snapshot data for charged-off loans in terms of collections and the communications made to them was provided and the aim was to create propensity to pay models for two sections in this data : starter and non-starter accounts. Starter accounts were the ones where a payment was made on or before 31st August, 2025 and similarly, Non-starter accounts were the ones where no payment was made before this period. A propensity to pay meant that at least one payment by the account would be made in the near future and so, the target variable was derived depending on whether at least one payment was made by these accounts in their life cycle or not.

## 🎯 Data available
In terms of features, repayment snapshot data was available for all the accounts. Last payment amount, Principal amount, Current outstanding, total payments, chargeoff date, last payment date, age, etc. which gave an indication of where an account was in terms of repaying at regular intervals and the longetivity of the collection process. In addition to this, data regarding the calls made to them and the interactions had was also provided, i.e, the frequency of calls made, calls connected and what was said on these calls in terms of repayment, etc. This set had around 1.5 lakh loan ids.
Another dataset regarding communications like SMS and Emails sent to some of these loans was also provided. This data was transactional in that emails sent, delivered and read were recorded. Similarly, SMS'es sent and delivered were recorded. This data had around 16k loans which formed a subset of the 1.5 lakh loans.

## Assumptions

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
- Models / algorithms
- Assumptions
- Key steps

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
