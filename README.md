## Project Background
Medicare provider fraud poses a critical threat to the sustainability of the Medicare system. Government reports indicate that fraudulent claims have significantly inflated Medicare spending. Often orchestrated by organized crime networks, these fraudulent activities involve collusion among providers, physicians, and beneficiaries to submit deceptive claims.

Provider fraud manifests in various forms, including:

a) Billing for services that were not provided.

b) Submitting duplicate claims for the same service.

c) Misrepresenting the services provided.

d) Charging for more complex or expensive services than were actually delivered.

e) Billing for a covered service when a non-covered service was provided.

## Problem Statement
The goal of this project is to predict potentially fraudulent healthcare providers based on their profile features. By analyzing various data points, including the number of claims filed, operating areas, specialties, common diagnosis codes, procedure codes, total reimbursement amounts, and other relevant information, we aim to develop a robust predictive model. Additionally, we seek to identify key variables that are instrumental in detecting potentially fraudulent providers.

## Project Objectives
#### Develop a Predictive Model
Create a machine learning model that accurately predicts whether a provider is likely to be involved in fraudulent activities.

#### Feature Analysis
Identify the most significant features that contribute to the prediction of fraudulent behavior.

#### Validation and Testing
Ensure the model's reliability and accuracy through rigorous validation and testing.

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

### Features from Inpatient Data (grouped by provider)
* **Average inpatient claims per patient:**
    * Calculate the average number of claims per patient.
* **Average inpatient reimbursement amount per claim:**
    * Calculate the average amount reimbursed for each claim.
* **Average inpatient deductible amount per claim:**
    * Calculate the average deductible amount for each claim.
* **Average inpatient claim length:**
    * Calculate the average length of time from admission to discharge for each claim.
* **Average hospital stay per claim:**
    * Calculate the average number of days a patient stays in the hospital for each claim.
* **Average number of physicians attending a claim:**
    * Calculate the average number of physicians involved in each claim.
* **Average number of types of physicians per claim:**
    * Calculate the average number of different types of physicians involved in each claim.
* **Most frequent ClmAdmitDiagnosisCode:**
    * Identify the most frequently occurring admission diagnosis code.
* **Most frequent DiagnosisGroupCode:**
    * Identify the most frequently occurring diagnosis group code.
* **Most frequent ClmDiagnosisCode:**
    * Identify the most frequently occurring diagnosis code.
* **Most frequent ClmProcedureCode:**
    * Identify the most frequently occurring procedure code.
  
### Features from Outpatient Data (grouped by provider)
* **Average outpatient claims per patient:**
    * Calculate the average number of claims per patient.
* **Average outpatient reimbursement amount per claim:**
    * Calculate the average amount reimbursed for each claim.
* **Average outpatient deductible amount per claim:**
    * Calculate the average deductible amount for each claim.
* **Average outpatient claim length:**
    * Calculate the average length of time for each outpatient claim.
* **Average number of physicians attending a claim:**
    * Calculate the average number of physicians involved in each claim.
* **Average number of types of physicians per claim:**
    * Calculate the average number of different types of physicians involved in each claim.
* **Most frequent ClmAdmitDiagnosisCode:**
    * Identify the most frequently occurring admission diagnosis code.
* **Most frequent ClmDiagnosisCode:**
    * Identify the most frequently occurring diagnosis code.

### From Beneficiary Data (grouped by provider)
* **Mortality rate:**
    * Calculate the proportion of deceased patients among all patients for each provider.
* **Average patient age:**
    * Calculate the average age of patients for each provider.
* **Distinct count of states:**
    * Count the number of unique states from which patients come for each provider.
* **Distinct count of counties:**
    * Count the number of unique counties from which patients come for each provider.
* **Average Charlson Comorbidity Index (CCI) of patients for each provider:**
    * Calculate the average CCI score of patients for each provider.

## EDA Analysis
### Comparison between lable 1 and 0 on numerical columns
![image](https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/0e7ce3ca-8c99-4aad-b00a-834c40b82800)


### Comparison between lable 1 and 0 on categorical columns
<img width="1117" alt="Screenshot 2024-06-11 at 9 23 49 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/fb1d1f30-c2a1-43ab-acdf-291e8aa99688">
<img width="1117" alt="Screenshot 2024-06-11 at 9 24 28 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/5c7f6244-f559-427c-9e60-d5781598a3ad">
<img width="1118" alt="Screenshot 2024-06-11 at 9 24 52 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/6ff6f0c6-4e29-43b2-9417-de7914f77a2b">


## Data Preprocessing
### Process null values
  * Replace numeric null values with 0, as these null values indicate that the provider doesn’t have corresponding inpatient or outpatient records.
  * Replace categorical null values with 'None'.

### Process outliers
  * Use the Interquartile Range (IQR) method to set the lower bound as Q1 - 1.5 * IQR and the upper bound as Q3 + 1.5 * IQR.
  * Cap the top 5% of values to perform winsorization on the upper tail.

### Check Correlation Among Features: 
  * Calculate the Spearman's Rank Correlation Coefficient and visualize the correlation matrix to identify highly correlated features
  * Discover 11 sets of features with correlation coefficients exceeding 0.7.
  * Feature Engineering: 
    * Combine and create new columns:
        * 'avg_ip_cost': (avg_ip_reimbursement_per_claim + avg_ip_deductible_per_claim *'avg_ip_claims_per_pat'
        * 'area_range': num_County + num_State
    * Eliminate features based on their point biserial correlation with the target variable.
    
### Label Encoding Categorical Features:
  * Transform categorical features into numerical values using label encoding.

### Split Data into Train, Validation, and Test Datasets:
  * Divide the dataset into training, validation, and test sets for model evaluation.

### Normalize Features:
  * Fit a MinmaxScaler on the training data to normalize feature scales and then apply the same parameters to the validation and test datasets.

## Modeling
### Models: SVM, Decision Tree, Random Forest, XGBoost
* Use GridSearchCV to find the best parameters, focusing on maximizing recall as the priority metric.
  * Given the critical nature of fraud detection, where missing potential fraudulent cases carries higher risk than falsely flagging non-fraudulent ones, prioritizing recall ensures a focus on accurately identifying fraudulent instances.

* Train the Model with the Best Parameters

* Evaluate the Model on the Validation Dataset

### Compare the performance of models
<img width="1017" alt="Screenshot 2024-06-11 at 9 56 20 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/ef7450b2-9e37-47ac-9aae-8c15d3f55d1f">

### Model Selection and Retraining
* Considering the performance metrics, XGBoost outperforms other models and is selected.
* Merge the training and validation datasets, and retrain the chosen XGBoost model.
* Evaluate the model's performance on the test dataset to obtain validation scores.
* Evaluation Metrics:

| Metric    | XGBoost     |
|-----------|-------------|
| Accuracy  | 79.57%      |
| Recall    | 85.09%      |     
| Precision | 32.23%      |
| AUC       | 88.26%      |       

* Confusion matrix:
<img width="371" alt="Screenshot 2024-06-11 at 10 11 25 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/61c2d5a5-384b-4166-be41-26ba247cd86f">

* ROC Curve:
<img width="688" alt="Screenshot 2024-06-11 at 10 14 21 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/b237893e-3fb2-4f33-99a1-b15b80bd1de7">


## Conclusions: 
* From EDA analysis, fraudulent providers tend to exhibit distinct patterns compared to non-fraudulent ones in terms of the number of states, number of counties, outpatient reimbursement, and outpatient deductible amount. They often submit higher outpatient reimbursement claims and incur higher deductible amounts. Moreover, fraudulent providers typically operate across a wider geographic area and involve multiple physicians and physician types in a claim.
* There is no clear discernible difference in the behavioral patterns of fraudulent providers regarding their diagnosis and procedure codes. This could be influenced by feature selection, as only the most frequent codes were considered. However, in reality, rare codes may serve as more indicative signals of fraud.
* From the XGBoost model, important features for classifying claims are listed as below:
<img width="873" alt="Screenshot 2024-06-11 at 10 18 44 AM" src="https://github.com/yingliu1206/Fraud-detection-In-Health-Care/assets/71619071/4bec3f91-c946-46a3-a258-9f20ea320427">

* Notable features include total inpatient cost, modality rate, and area range. These important features align with the key differences observed during EDA. Additionally, the plot highlights the significance of outpatient claims per patient as an influential factor.

## Limitations: 
The label is assigned to providers, not individual claims. Therefore, merging the label with each claim by using the "provider_id" is not ideal. As a result, we reframed the problem to focus on identifying features of fraudulent providers rather than fraudulent claims. However, in reality, even providers with a high likelihood of being fraudulent may have both legitimate and fraudulent claims. Directing attention to all their claims may result in resource wastage. Thus, it would be more efficient to label each claim and train a model to predict the probability of fraudulent claims. This approach could yield more actionable insights.

## Future Work: 
* Exploring better data resources for claims. 
* Extracting the top 5 diagnosis codes and procedure codes and utilizing Doc2Vec for feature transformation could be explored to enhance model performance.
* Implementing a script in a pipeline format to streamline the workflow.

## Technical Takeaways
#### Outliers Detection:
*	Z-score:
    *	(data - mean)/std
    * If abs(Z-score) > 3, then outliers

*	Interquartile Range (IQR):
    * Q1 = np.percentile(data, 25)
    * Q3 = np.percentile(data, 75)
    * IQR(Interquartile Range ) = Q3 - Q1
    * lower_bound = Q1 - 1.5 * IQR
    * upper_bound = Q3 + 1.5 * IQR
    * Identify outliers, whether they result from human error or extreme values.

eg. calculate 30th percentile
1) Sort the Data: [15, 20, 35, 40, 50]
2) Number of Observations: n=5
3) Calculate the Rank (Index): (n+1) * 30/100 -> 6*0.3 = 1.8
Since 1.8 is not an integer, interpolate between the 1st and 2nd values in the sorted list.
4) Interpolate
The 1st value is 15.
The 2nd value is 20.
The fractional part of R is 0.8.
15 + 0.8*(20-15) = 19

#### Outliers Handling: 
* Simply removing outliers from the dataset, typically useful for addressing human errors.
* Clipping: setting outliers to predefined boundary values, providing a more controlled approach to handling extreme values.
* Winsorization: rather than setting them to the boundary values directly, winsorization replaces extreme values with less extreme values within a specified percentile range.
* Apply robust scaling followed by min-max scaling to address outliers. It’s important to note that while robust scaling doesn’t constrain features to a specific range, we still need to apply another scaler before fitting the data into the model.
    * robust scaling: (x-median)/(percentile(75) - percentile(25))

### Log Transformation with Shifting on Right-Skewed Columns
* Apply log transformation to right-skewed columns to normalize the distribution. Add a constant shift to handle zero or negative values. But it cannot convert all features to normal distribution.

### Eencoding
* When the column values are in list format, one-hot encoding is possible. However, if there are too many distinct values in the column, it can lead to sparse data issues.

### Multicollinearity
#### Correlation Measurement
* Pearson correlation coefficient = Cov(X,Y)/(stdev(X)*stdev(Y))
  * Both variables should be continuous and approximately normally distributed.
  * The relationship between the variables should be linear.
  * Constant variance of the variables
* Spearman's Rank Correlation Coefficient = Cov(X_rank,Y_rank)/(stdev(X_rank)*stdev(Y_rank))
  * Convert the data to ranks and then apply Pearson's correlation formula to the ranks.
  * Can be used with ordinal, interval, or ratio data.
  * Does not require the relationship to be linear, only monotonic (consistently increasing or decreasing).
* VIF: variance inflation factor quantifies how much the variance of a regression coefficient is inflated due to multicollinearity in the model. 
    * Fit the Regression model of Each Predictor on Others:
      Regress X1​ on X2​ and X3​.
      Regress X2​ on X1​ and X3​.
      Regress X3​ on X1​ and X2​.
    * Calculation of R-squared:
      R-squared = 1- (residual sum of squares/ total sum of squares)
      For each regression, compute the R-squared value, which represents how well the predictor can be explained by the other predictors.
    * Compute VIF:
      Use the formula VIF(Xi​)=1−1/Ri-squared​​ to compute VIF for each predictor.
    * Higher R-squared corresponds to higher VIF. This indicates that the predictor is more susceptible to being predicted by other features.

#### Dealing with Highly-Correlated Columns:
* Utilize Domain Knowledge:
    * Consider the significance of each feature within the problem domain.
* Check correlation with the target vairable:
    * use the point-biserial correlation coefficient to evaluate how well each continuous feature correlates with the binary target variable. Features with higher absolute values of the point-biserial correlation are more strongly associated with the target and may be considered more important.
* PCA:
    * While PCA does not require features to adhere to a normal distribution, it assumes linearity. Principal components can be interpreted as combinations of the original features.
* Remove Features with VIF > 10:
    * If multiple features need to be dropped, calculate the variance inflation factor (VIF) dynamically.
* Feature Importance Analysis:
    * For instance, in Random Forest or Gradient Boosting models, train the model with both sets of highly correlated features and assess the importance scores. Retain the feature with the higher importance score.
* Feature Engineering:
    * Combine or create new features to mitigate multicollinearity.

### Modeling:
* Weight of Evidence (WOE) and Information Value (IV) in logistic regression
    * Information value is not an optimal feature selection method when you are building a classification model other than binary logistic regression (for eg. random forest or SVM) as conditional log odds (which we predict in a logistic regression model) is highly related to the calculation of weight of evidence.
* The prerequisites for logistic regression
  * binary target
  * no multicollinearity between the predictor variables 
  * linear relationship between the logit (log-odds) of the outcome and each predictor variable (lr assumption)
  * prefer large sample size
  * problem with extreme outliers
* Models that do not have strict assumptions about multicollinearity are generally those that are non-linear or ensemble-based methods. These models do not require the predictors to be independent of each other and can handle correlated features better than linear models. eg. decision tree, random forests, XGBoost, SVM, KNN, neural networks.

* Hyperparameters tuning
    * Setting class_weight parameters to 'balanced' can help address imbalanced labels.
    * Utilizing a fixed random_state ensures deterministic behavior during model fitting.
    * Both GridSearchCV and RandomizedSearchCV allow the n_jobs parameter to be set to -1, utilizing all available cores for parallel processing.
    * XG_Boost overfitting: 
        * lower max_depth
        * increase min_child_weight: ensure nodes with fewer observations are not split.
        * gamma: increase the minimum loss reduction required to make a split
        * decrease the learning rate(eta) and increase the number of trees to ensure the model learns more slowly and generalizes better.
        * regularization: reg_alpha - L1, reg_lambda - L2




