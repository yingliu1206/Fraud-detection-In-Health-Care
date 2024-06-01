## Project Objectives
Provider fraud is one of the most significant challenges facing Medicare today. According to government statistics, Medicare spending has increased dramatically due to fraudulent claims. Healthcare fraud often involves organized crime, with networks of providers, physicians, and beneficiaries collaborating to submit fraudulent claims.

Through rigorous analysis of Medicare data, numerous physicians engaging in fraudulent activities have been identified. They frequently use ambiguous diagnosis codes to justify the most expensive procedures and drugs. Insurance companies are particularly vulnerable to these practices. Consequently, they have raised insurance premiums, making healthcare increasingly expensive.

Healthcare fraud and abuse take many forms. Common types of provider fraud include:

a) Billing for services that were not provided.

b) Submitting duplicate claims for the same service.

c) Misrepresenting the services provided.

d) Charging for more complex or expensive services than were actually delivered.

e) Billing for a covered service when a non-covered service was provided.

## Problem Statement
The goal of this project is to predict potentially fraudulent providers based on their profile features. These features include the number of claims filed, operating areas, specialty, most common diagnosis codes, procedure codes, total reimbursement amounts, and other relevant data. Additionally, we aim to identify key variables that are instrumental in detecting potentially fraudulent providers.

## Dataset
For this project, we are considering the following datasets: Inpatient claims, Outpatient claims, Beneficiary details, and Provider information.

A) Inpatient Data

This dataset provides insights into claims filed for patients admitted to hospitals. It includes details such as admission and discharge dates, and admission diagnosis codes.

B) Outpatient Data

This dataset contains information about claims filed for patients who visit hospitals but are not admitted.

C) Beneficiary Details Data

This dataset includes beneficiary Know Your Customer (KYC) details, such as health conditions and the region they belong to.

D) Provider Data

This dataset consists of two columns: Provider ID and the label PotentialFraud (Yes/No).

## Feature Engineering
Since we don’t have labels for each claim, we cannot directly join the datasets by provider_id and assign the provider label to each claim. Instead, we will create features that represent the provider and then attach them to the provider data.

### From inpatient data
* Average inpatient claims per patient 
* Average inpatient reimbursement amount per claim 
* Average inpatient deductible amount per claim 
* Average inpatient claim length
* Average hospital stay per claim
* Average number of physicians attending a claim
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
* Average number of physicians attending a claim
* Average number of types of physicians per claim
* The most frequent ClmAdmitDiagnosisCode
* The most frequent ClmDiagnosisCode

### From beneficiary data
* Mortality rate
* Average patient age
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
* Log Transformation with Shifting on Right-Skewed Columns:
  * Apply log transformation to right-skewed columns to normalize the distribution. Add a constant shift to handle zero or negative values.

* Label Encoding Categorical Features:
  * Convert categorical features into numerical values using label encoding.

* Check Correlation Among Features:
  * Analyze the correlation matrix to identify highly correlated features and reduce multicollinearity.

* Split Data into Train, Validation, and Test Datasets:
  * Divide the data into training, validation, and test sets to evaluate the model's performance.

* Standardize Features:
  * Fit a StandardScaler on the training data to normalize feature scales and then apply the same parameters to the validation and test datasets.

## Modeling
### Using all features
#### Models: SVM, Decision Tree, Random Forest, XGBoost
* Use GridSearchCV to Find the Optimal Parameters
  * Perform hyperparameter tuning for each model using GridSearchCV to identify the best parameters.

* Train the Model with the Best Parameters
  * Train each model using the optimal parameters obtained from GridSearchCV.

* Evaluate the Model on the Validation Dataset
  * Assess the performance of each model on the validation dataset and record the scores.

#### Compare the performance of models
<img width="1005" alt="image" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/8f728312-a866-48c8-a8fb-9453ede392df">

### Using important features
#### Models: SVM, Decision Tree, Random Forest, XGBoost
* Select Important Features Based on Random Forest Output
  * Utilize the feature importances derived from the Random Forest model to select the most relevant features.

* Use GridSearchCV to Find the Optimal Parameters:
  * Perform hyperparameter tuning for each model using GridSearchCV to identify the best parameters.

* Train the Model with the Best Parameters:
  * Train each model using the optimal parameters obtained from GridSearchCV.

* Evaluate the Model on the Validation Dataset:
  * Assess the performance of each model on the validation dataset and record the scores.

#### Compare the performance of models
<img width="1003" alt="image" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/2c77f93b-8f06-4a12-a6af-c58994fd7f05">

## Conclusions: 
* There is not a significant difference in model performance between using all features and using selected features. However, we observe slightly better performance, such as recall in the random forest model, when using all features.
* The number of states, number of counties, outpatient reimbursement, and outpatient deductible amount show distinct trends between labels 0 and 1. Fraudulent providers typically bill higher outpatient reimbursement claims, and the deductible amounts are also usually higher. Additionally, they cover a wider range of areas compared to non-fraudulent providers and are more likely to include multiple physicians and physician types in a claim.
* From the random forest model, important features for classifying claims are listed as below:
<img width="896" alt="image" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/8e792dc7-bccc-4bd4-a805-652e93f1ddb3">

## Limitations: 
The label is assigned to providers, not individual claims. Therefore, merging the label with each claim by using the "provider_id" is not ideal. As a result, we reframed the problem to focus on identifying features of fraudulent providers rather than fraudulent claims. However, in reality, even providers with a high likelihood of being fraudulent may have both legitimate and fraudulent claims. Directing attention to all their claims may result in resource wastage. Thus, it would be more efficient to label each claim and train a model to predict the probability of fraudulent claims. This approach could yield more actionable insights.

## Future Work: 
Exploring better data resources for claims would be valuable for future work. Identifying and incorporating additional data sources could enhance the accuracy and effectiveness of fraud detection models.
