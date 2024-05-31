## Project Objectives
Provider Fraud is one of the biggest problems facing Medicare. Based on the government statistics, the total Medicare spending increased exponentially due to frauds in Medicare claims. Healthcare fraud is an organized crime which involves peers of providers, physicians, beneficiaries acting together to make fraud claims.

Rigorous analysis of Medicare data has yielded many physicians who indulge in fraud. They adopt ways in which an ambiguous diagnosis code is used to adopt costliest procedures and drugs. Insurance companies are the most vulnerable institutions impacted due to these bad practices. Due to this reason, insurance companies increased their insurance premiums and as result healthcare is becoming costly matter day by day.

Healthcare fraud and abuse take many forms. Some of the most common types of frauds by providers are:

a) Billing for services that were not provided.

b) Duplicate submission of a claim for the same service.

c) Misrepresenting the service provided.

d) Charging for a more complex or expensive service than was actually provided.

e) Billing for a covered service when the service actually provided was not covered.

## Problem Statement
The goal of this project is to "predict the potentially fraudulent providers" based on their profile features, including the number of claims they have filed, their operating areas, their specialty - the most common diagnosis code, procedure code, the total reimbursement amount, and the like. Along with this, we will also discover important variables helpful in detecting the potentially fraud providers. 

## Dataset
For the purpose of this project, we are considering Inpatient claims, Outpatient claims and Beneficiary details of each provider.

A) Inpatient Data

This data provides insights about the claims filed for those patients who are admitted in the hospitals. It also provides additional details like their admission and discharge dates and admit diagnosis code.

B) Outpatient Data

This data provides details about the claims filed for those patients who visit hospitals and not admitted in it.

C) Beneficiary Details Data

This data contains beneficiary KYC details like health conditions,region they belong to etc.

D) Provider Data

It contains two columns - provider ID and the label PotentialFraud(Yes/No).

## Feature Engineering
Since we don’t have the labels for each claim, we cannot simply join the datasets by provider_id and assign the provider label to each claim. What we will do instead is create features that can portray the provider and then attach them to the provider data.

### From inpatient data
* Average inpatient claims per patient 
* Average inpatient reimbursement amount per claim 
* Average inpatient deductible amount per claim 
* Average inpatient claim length
* Average hospital stay per claim
* Average number of physicians who attend a claim
* Average number of types of physicians per claim
* The most frequent ClmAdmitDiagnosisCode
* The most frequent DiagnosisGroupCode
* The most frequent ClmDiagnosisCode
* The most frequent ClmProcedureCode

### From outpatient data
* Average outpatient claims per patient 
* Average outpatient reimbursement amount per claim 
* Average outpatient deductible amount per claim 
* Average outpatient claim length
* Average number of physicians who attend a claim
* Average number of types of physicians per claim
* The most frequent ClmAdmitDiagnosisCode
* The most frequent ClmDiagnosisCode

### From beneficiary data
* Mortality rate
* Average patients' age
* Distinct count of state
* Distinct count of county
* Average Charlson Comorbidity Index(CCI) of patients for each provider

## EDA Analysis
### Comparison between lable 1 and 0 on numerical columns
![image](https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/0e7ce3ca-8c99-4aad-b00a-834c40b82800)


### Comparison between lable 1 and 0 on categorical columns
![image](https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/0302ba56-3e04-4144-895b-0accb9d7269d)
![image](https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/a0f34e0d-b5a7-492b-9fa4-3e32a84fc51f)

## Data Preprocessing
* Use log transformation with shifting on right screwed columns
* Label encoding categorical features
* Check the correlation among features
* Split data into train, validation, test dataset
* Fit StandardScaler on the training data and then apply parameters on the validation and test datasets

## Modeling
### Using all features
#### Models: SVM, Decision Tree, Random Forest, Xg_Boost
* Use GridSearchCV to find the optimal parameters
* Train the model with the best parameters
* Get the score on the validation dataset
#### Compare the performance of models
<img width="1038" alt="Screenshot 2024-05-17 at 8 41 00 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/9b87fee0-4779-40e5-95ca-867944c85769">

### Using important features
#### Models: SVM, Decision Tree, Random Forest, Xg_Boost
* Use GridSearchCV to find the optimal parameters
* Train the model with the best parameters
* Get the score on the validation dataset
#### Compare the performance of models
<img width="1038" alt="Screenshot 2024-05-17 at 8 41 50 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/27ad2a5b-82ef-413e-a53b-4bd9a7c76a8b">

### Comparison using all features VS Imp features
<img width="1039" alt="Screenshot 2024-05-17 at 8 42 19 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/3bebbfad-55d2-49d9-9237-ef83a8c7de4f">
<img width="1037" alt="Screenshot 2024-05-17 at 8 42 41 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/d0cd97d0-5ed5-495b-8c9a-c8db43130233">
<img width="1040" alt="Screenshot 2024-05-17 at 8 39 51 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/56b20b16-7073-469d-8d9e-59255468ec69">
<img width="1043" alt="Screenshot 2024-05-17 at 8 40 10 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/bea91e83-d337-44a2-b569-08cd06a96660">


## Conclusions: 
* There is not a significant difference in model performance between using all features and using selected features. However, we do observe slightly better performance in some models when using all features.
* From XGBoost, important features are listed as below to classify the claims.
![image](https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/b5c97d8d-f076-413b-b4dd-71ee69bea5f7)
* The number of states, number of counties, outpatient reimbursement, and outpatient deductible amount exhibit distinct trends between labels 0 and 1. Fraudulent providers are typically billed with high outpatient reimbursement claims, and the deductible amount is also usually higher. Additionally, they cover a wider range of areas compared to non-fraudulent providers and are more likely to include multiple physicians and physician types in a claim.

## Limitations: 
* Dataset：
  * The label is assigned to providers, not individual claims. Therefore, attaching the label to each claim by merging with the provider table using "provider_id" is not ideal.
  * Using private information features such as age, gender, and race is not recommended.
* Models:
  * Logistic Regression and Random Forest did not perform well. This poor performance is likely due to incorrect labeling resulting from improper merging, which prevents the models from learning correct patterns.

## Future Work: 
* Reframe the problem and create new features based on providers.
* For the imblanced dataset, consider using class_weight during modeling to address class imbalance.
