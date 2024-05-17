## Project Objectives
Provider Fraud is one of the biggest problems facing Medicare. According to the government, the total Medicare spending increased exponentially due to frauds in Medicare claims. Healthcare fraud is an organized crime which involves peers of providers, physicians, beneficiaries acting together to make fraud claims.

Rigorous analysis of Medicare data has yielded many physicians who indulge in fraud. They adopt ways in which an ambiguous diagnosis code is used to adopt costliest procedures and drugs. Insurance companies are the most vulnerable institutions impacted due to these bad practices. Due to this reason, insurance companies increased their insurance premiums and as result healthcare is becoming costly matter day by day.

Healthcare fraud and abuse take many forms. Some of the most common types of frauds by providers are:

a) Billing for services that were not provided.

b) Duplicate submission of a claim for the same service.

c) Misrepresenting the service provided.

d) Charging for a more complex or expensive service than was actually provided.

e) Billing for a covered service when the service actually provided was not covered.

## Problem Statement
The goal of this project is to "predict the potentially fraudulent providers" based on the claims filed by them. Along with this, we will also discover important variables helpful in detecting the behaviour of potentially fraud providers. Further, we will study fraudulent patterns in the provider's claims to understand the future behaviour of providers.

## Dataset
For the purpose of this project, we are considering Inpatient claims, Outpatient claims and Beneficiary details of each provider.

A) Inpatient Data

This data provides insights about the claims filed for those patients who are admitted in the hospitals. It also provides additional details like their admission and discharge dates and admit d diagnosis code.

B) Outpatient Data

This data provides details about the claims filed for those patients who visit hospitals and not admitted in it.

C) Beneficiary Details Data

This data contains beneficiary KYC details like health conditions,regioregion they belong to etc.

## EDA Before Data Preprocessing
### Inpatient findings
* The top procedure is 4019.0. There are 6% patients undergoing this procedure. Based on ICD-9 Code, 4019 is Hypertension NOS (Unspecified essential hypertension).
* The top diagnosis is also 4019. There are around 4.5% patients diagnosed as Hypertension NOS (Unspecified essential hypertension).
* In the inpatient claims, providers are evenly distributed. There is no specific providers which submitted more claims than others.
* More than 90% of paitients stayed in hospital less than 10 days.
* The distribution of amount reimbursed in the inpatient claim seems like a log normal distribtion. The majority of amount falls between 0 and 20,000.

### Outpatient findings
* The top procedure is 9904.0. There are around 7.3% patients undergoing this procedure. Based on ICD-9 Code, 9904 is Transfusion Of Packed Cells. 4516 is the second popular procedure, which is Esophagogastroduodenoscopy [Egd] With Closed Biopsy.
* The top diagnosis is the same as the inpatient's data - 4019. There are around 4.5% patients diagnosed as Hypertension NOS (Unspecified essential hypertension). The second dx code 25000 is Incision Procedures on the Forearm and Wrist.
* Provider 'PRV51459' has around 1% more claims than other providers.
* The majority of claim length is 1 day.
* The claim reimbursed amount in outpaitent claims shows a rough log normal distribution. However, on the tail, there is a signal of outliers around $3500.

### Beneficiary Details findings
* While there is no significant difference in the age distribution that would allow us to flag potential fraud, we do observe an increasing trend in potential fraud cases among patients aged 65 and older. Additionally, most patients who apply for claims fall within this age range.
* A significant number of fraudulent cases involve patients belonging to a specific race, labeled as 1.

## Data Preprocessing
* Merge OutPatient, Inpatient, and Beneficiary to get the whole dataset
* Replace values with a binary annotation (1,2 to 0,1)
* Check the balance of the label
* Check duplicates
* Replace null values in DeductibleAmtPaid, DiagnosisGroupCode, dx code, and proc dode with 0
* Feature Engineering
   * Feature creation
      * Deceased
      * Age
      * Hospital Stays
      * Claim Length
      * physician_same: check for same physician in all three physician columns
      * N_Types_Physicians: count number of different physicians who appear in a claim
      * total_num_diag: count how many dx a claim has
      * total_num_proce: count how many proc a claim has
      * total_claims_cost: calculate the total claim cost for each 'BeneID'
      * total_claims_num: calculate the total number of claims for each 'BeneID'
      * claims_cost_mean: calculate the mean cost for each 'BeneID'
  * Encoding Categorical Features
      * apply one-hot encoding on all proc code and dx code - cause sparse data issue
      * encode top 5 proc code and dx code
      * encoding types of physicians into numeric values
  * Feature Deletion
      * drop features from which other features were created 
* Split data into train, validation, test dataset
* Use SMOTE to balance the labels, but face overfitting issue. Use ENN+SMOTE to balance the data, but still face overfitting.
* Normalization numerical columns
  *  calculates and stores the parameters necessary for normalization based on the training data
  *  normalize each column in train, validation, and test dataset

## Modeling
### Using all features
#### Models: LR, Decision Tree, Random Forest, Xg_Boost
* Use GridSearchCV to find the optimal parameters
* Train the model with the best parameters
* Get the score on the validation dataset
#### Compare the performance of models
<img width="1038" alt="Screenshot 2024-05-17 at 8 41 00 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/9b87fee0-4779-40e5-95ca-867944c85769">

### Using important features
#### Models: LR, Decision Tree, Random Forest, Xg_Boost
* Use GridSearchCV to find the optimal parameters
* Train the model with the best parameters
* Get the score on the validation dataset
#### Compare the performance of models
<img width="1049" alt="Screenshot 2024-05-17 at 8 38 51 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/9b54983f-9729-46d4-b5d6-321b0a75f601">

### Comparison using all features VS Imp features
<img width="1052" alt="Screenshot 2024-05-17 at 8 39 18 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/51fcfb8c-572b-40e0-b5c0-035752fe1466">
<img width="1045" alt="Screenshot 2024-05-17 at 8 39 36 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/3fadc246-4936-4c1f-a20a-8eff6f3de16b">
<img width="1040" alt="Screenshot 2024-05-17 at 8 39 51 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/56b20b16-7073-469d-8d9e-59255468ec69">
<img width="1043" alt="Screenshot 2024-05-17 at 8 40 10 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/bea91e83-d337-44a2-b569-08cd06a96660">


**Conclusion**: There is not a significant difference in model performance between using all features and using selected features. However, we do observe slightly better performance in some models when using all features.
