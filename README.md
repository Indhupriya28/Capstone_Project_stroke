Dataset Details: healthCare_stroke_dataset.csv
Path : [Capstone_Project_stroke/dataset/healthCare_stroke_dataset.csv](https://github.com/Indhupriya28/Capstone_Project_stroke/tree/main/dataset)
Images : https://github.com/Indhupriya28/Capstone_Project_stroke/tree/main/images


**Null Value Analysis and Data type correction:**
Column "BMI" has null values of count 201 which is 4.1%. Null values are replaced with Median Value of BMI column. Mean() is not considered because mean value gets impacted by the Outliers much, so median is used.

Here, BMI is of type "Object" and values are written within  "". This double quote is handled by string replace() and Type is changed to numeric.Then Null values are handled.

Duplicate detection and removal:
Duplicate datas are found in the dataset. 
Analysis: Null count/Percentage is reduced from the actual percentage post dropping the duplicate data. It became 3.9% from 4.1%..

Type :Category
Memory usage before conversion:: 1704116
Memory usage after conversion :: 1469323

Column "Residence_type" is changed to "category"

Descriptive statistics and skewness: 
----------------------------------------
 id : -0.018683629825433572              |
 age : -0.13810378754797709              |
 hypertension : 2.7138353513284765       |
 heart_disease : 3.9415310178638117      |
 avg_glucose_level : 1.5710067514023272  |
 bmi : 1.087883631572357                 |
 stroke : 4.184609671802627              |
-----------------------------------------

   * Among the numeric columns, Stroke, heart_disease, hypertension has highest value. However, these fields has binary values . So here i'm considering next highest numeric column "Avg_glucose_level" it has numeric values. 
    * This column is positively (right)skewed, meaning hew higher values (outliers) are present. In such case, mean is influenced by high values. So median is considered

    * Negative Skewness :  id and age. Ignored column "Id" as it doesn't mean anything, just unique id for the row. I could see slightly left skew in the column "Age".This could be presence of young age people which leads to negative skew


Outlier detection with IQR:
--------------------------------------------------------
Number of outliers for column Avg_glucose_level => 629  |
Number of outliers for column " BMI" => 126             |
--------------------------------------------------------
* The outliers identified in above columns will be retained in Part2. Because, these are actual health data which are needed for further processing.

7. Visualizations:

    * Histogram_Plot : Most skewed data- column "avg_glucose_level" 
        The graph is right-Skewed. Majority of the people's glucose level lies in the range of 70-110. For minimum people glucose_level is beyond 200, which is serious issue where they couldn't bring glucose level under control. that is why this is right skewed(positive)

    * Scatter_Plot : Distribution between Age and GlucoseLevel
        consider 2 combinations:
            1. Age_ BMI : This is not taken for consideration because, Age alone won't influence BMI when compared to other combination. 
            2. Age_Glucose level : As the age increases, glucose level increase. Here, from the graph I infer that less age people has less glucose level.As it goes further , it increases.I could see slight increase in the graph.Hence I consider these two column correlate each other.

    
    * Box Plot : Distribution between WORK_TYPE and BMI
        Here, people in Private, SElf-employeed, Government job has similar Median values. Children has lowest median value. Private plot has higher number outliers compared to others, this could be data distribution (count) which is high for private when compared other category.
        Working category has outliers. However Never working category has less outliers, also if I see the count of this category it is less when compared to others. This could be the reason for less outliers.


**8. Correlation Heat map:**

    Highest Absolute Correlation : Age and BMI (0.32).
    As Age increases, Bmi increases. However, this is not casual realtionship. Only Age cant be a factor for BMI to increase, third factors also there. Like lack of exercise, food habit.. also matters.
    One plausiable explanation : Lack of Physical activity due to medical conditions or some other factors. This could also add as a reson for the BMI to increase.

**9.A Imputation strategy comparison:**
    Most Skewed Data: "AVG_GLUCOSE_LEVEL" : It is right skewed (postive) meaning it has outliers. 
    For "BMI" -> Slightly skewed (right).THere is no huge difference between mean and median here. 
    Choosing mean() ,wont be a right option- outlier has impact on the mean. I prefer median() because it consider the middle value/ avg of the middle values. Hence it wont be impacted by the outliers.

**9.B Spearman rank correlation**
    1st PAIR : age vs avg_glucose_level
        Pearson > spearman . It indicates the Linear rather than monotonic.
    2nd PAIR : hypertension vs avg_glucose_level
        Pearson > Spearman. It indicates the Linear
    3rd PAIR : heart_disease vs avg_glucose_level
        Pearson > Spearman. It indicates the Linear

    In part2 , i rely on Peasrson correlation, because most of the combination indicates Pearson in this dataset. However, I could see Spearman correlation in the combination of age VS bmi ,heart_disease vs bmi , bmi vs stroke. I'll look into that as well.

9.C Grouped aggregation

    Consideried categorical column : "Smoking_Status" and numeric column : "BMI"
    (i) Highest Mean : Formely_smoked (30.61)
        Highest Std : Unknown (7.97)
    (ii) Unknown Status has higher standard deviation, indicates that BMI varies significantly with in this category. this alone is not sufficient to predict the BMI value.. Similarly to predict the target Stroke , "smoking_status" alone is not sufficient. Other features like hypertension, heart_disease, glucose_level.. needs to be considered.
    (iii) Ratio between highest and lowest mean is 1.19. Indicates that smoking_status slightly predictive factory for Bmi (future for Stroke).However, this feature alone wont act as predictive factor, it should be used along with other features to predict the target (stroke)


==============================================================================================================================================================

-----------
**PART-II**
-----------

1. Load dataset:
    Considered dataset has a Target (binary value) column "Stroke". This I consider for the classification
    Among the dataset, numeric continuous column "BMI" this considered as target for the Regression case.
    Data split happens in two way accordingly, 
    a. For Regression : Removed the Target column "Bmi" from the dataset and remaining columns as Features
    b. For Classification : Removed the Target column "Stroke" from the dataset, remaining columns as Features (including BMI here)

2. Encoding
In my dataset, there is no categorical column in natural order. One-Hot encoding is used for all categorical columns.
Label encoder is not used here, because label encoding label the categories in ranked order which will make model to misunderstand the relationship.
One-hot encoding prevents this, it categories each different value as a individual binary column, also first dummy variable is removed to avaoid multi-collinearality problem. That one column value is inferred from the other values.


3. train-test split and scaling:
    Fitting the complete dataset on the scaler leads to data-leakage because model will learn from the complete data_set.When we use test_data the result wont be the proper one. Model memorize the result. So splitting is done before scaling and only training data is scaled.

4. Regression model — Linear Regression:
    A Large positive coefficient  => prodicted BMI increases as the feature value increases, while other features reamin constant. Here a positive coefficient fo 0.80 for "avg_glucose_level" says that higher glucose levels are associated with a highet BMI value.
    A Large Negative coefficient -> prodicted BMI decreases as the feature value increases, while other features remain constant. HEre, coefficient of  -2.80 for  "work_type_children" tells that individuals in the children work category are predicted to have a lower BMI that other work category.

5. Comparsion Ridge Regression:

Coefficient_data :
              Features  co-efficient  abs_coefficient
12  work_type_children     -2.803578         2.803578
8     ever_married_Yes      0.943272         0.943272
4    avg_glucose_level      0.802802         0.802802


Comparison of Linear and Ridge
               Model        MSE        R²
0  Linear Regression  44.693519  0.238088
1   Ridge Regression  44.693557  0.238087


    Here, Linear and Ridge produce almost same MSE and R2 score.THis shows Linear regression model was already stable. Using alpha=1.0 , produce same result as Linear model, indicates minor effect on the model.
    Ridge might produce different result in other cases because it adds penalty for the large coefficient during training. THe alpha value controls the strength of this penalty by shrinking the coefficients.

==================================================================================

5 . CLASSIFICATION MODEL -LOGISTIC REGRESSION

    Here, minority class is only 4% , imbalance dataset. To handle this imbalance I choose class_weight ='balanced'. 
    REASON:
        1. SMOTE create a new synthetic data. I have low percent of minority class, creating synthetic data around that class could be false dataset. Since it is a medical data. I prefer not to go with random data creation.
        2. By using class_weight, model gives more importance to the minority class when it comes to prediction due to penalty

    PRECISION:
                TP / (TP+FP) . TP - True Positive ;  FP - False Positive
    - It tells from the predicted stroke cases that are acutally stroke cases.

    RECALL:
            TP /(TP + FN)
    - It tells proportion of actual cases that model correctly identifies.

    For current case (stroke prediction)- Recall is more important that Precision. Missing a stroke case could delay the treatment, leading to serious health issue.
    A false positive result in additional medical test- Nothing cause major problem.

    The model's AUC is 77%- tells model able to distinguish between stroke and non-stroke patients. However, still we can improve the performance to distinguish betwenn the two classes.

5.B Decision-threshold:

----------------------------------------------
Threshold  Precision    Recall  F1 score     |
0        0.3   0.125581  0.830769  0.218182  |
1        0.4   0.142857  0.723077  0.238579  |
2        0.5   0.160156  0.630769  0.255452  |
3        0.6   0.152709  0.476923  0.231343  |
4        0.7   0.164286  0.353846  0.224390  |
---------------------------------------------

    a) Precision => TP / ( TP + FP)
        Recall => TP / (TP + FN)
    b)  For threshold = 0.5 (default) , F1 score is high.
    c) Recall Metric is important for this Stroke prediction. A false negative means a patient who actually has a stroke is predicted as not having one. Missing stroke can delay medical treatment and have serious consequences.
    d) I would lower the threshold, to detect as many stroke as possible. However, this also increases the number of false positives, which lowers precision which may lead to unnecessary follow-up examinations.


6. REGULARIZATION ON LOGISTIC REGRESSION:

C paramete controls the amount of regularization applied in logistic regression. A larger C, weak the regularization, allowing the model to fit the training data more closely. A smaller C applies stronger regularization which shrinks the model co-efficient and helps reduce overfitting.

---------------------------------------------------------
Comparison                                              |
                  Model  Precision    Recall       AUC  |
0          Base (C=1.0)   0.164286  0.353846  0.775026  |
1  Regularized (C=0.01)   0.160714  0.692308  0.787961  |
---------------------------------------------------------

Reducing C from 1.0 to 0.01 increases the model performance. Precision decreased sightly, the recall increased much- model correctly indentified many stroke cases. AUC also increased slightly.
The regularized model improved the result for this classification task.

7.Bootstrap confidence interval for AUC difference: 

--------------------------------------------------
Mean AUC Difference : -0.01270                    |
95% Confidence Interval : (-0.02512, -0.00039)    |
Confidence interval excludes zero.                |
--------------------------------------------------

The AUC difference is -0.012, indicated the model with c=0.01 perform higher than model with C=1.0.Since the confidence interval excludes zero and the AU curve is negative, the result indicate that the codel with c=0.01 consistenly achieved a slightly higher AUC than c=1.0 across the bootstrap samples.



=================================================================================================

***PART-III***

-------------------------------
Decision Tree performance :
Taining Accuracy : 99.93
Test Accuracy : 91.42
------------------------------

From the training and test accuracy i could see some gaps- this shows slight overfitting. However, by looking at the test accuracy, i can say that model has learned some patterns to predict the unknown cases as expected..

    *Decision Trees are considered as high-variance models because small chanes in the training data can produce different tree strucutre and different predictions. During training, a decision tree build a model by selecting the best split at each node. 
    *Once a split is made, the algo does not modify the earlier decisions. As a result, tree become highly tailored on training data, especially when there is no constraints which increase the risk of overfitting.

2.
** ROLE of max_depth:
    It tells how many levels the decision tree is allowed to grow.A tree with large depth can learn complex patterns, but it also memorize the training data leads to overfitting. restricting the maximum depth, improves models ability to generalize to unseen data.

** ROLE of min_samples_split:
    It specifies the minimum number of samples requried for a node to be split. If a node contains fewer samples, then it became a leaf node no split happens further. As a result, model becomes more stable and less prone to overfitting

* COMPARISON OF Decision Tree and Controlled version:

----------------------------
Controlled: 
Taining Accuracy : 95.35
Test Accuracy : 93.47
---------------------------

In Controlled version, training accuracy is bit reduced, however test accuracy is increased. Rather than memorizing the data, model learned the pattern.Thus making a model more reliable.

With 99% accuracy in unconstrained version- tells model overfit the training data.

**3. GINI VS ENTROPY:**
    Gini=1−∑p^2
    Entropy=−∑pi*log2*(p)

    Gini = 0 => means that the node is completely pure. Every sample in the node belongs to same class.
    Comparison of Gini and ENtropy : 

        Gini Test Accuracy : 93.47
        Entropy Test Accuracy :93.57

        The difference between two model is 0.10%. Both performs almost equally well on this dataset.


**4. RANDOM FOREST CLASSIFIER:**
    Random forest classifier calculates feature importance by measuring how much each feature reduces Gini impurity when data split happens.Each split decrease the impurity of a node. Features that consistently produce large impurity reductions receive higher importance values.

    It differ from the linear Regression coefficient. Linear regression tells the direction, magnitude of feature's effect on predicted value.
Random Forest "
    => measures how useful a feature  for making accurate split

**BAGGING in RANDOMFOREST**

    Random Forest is based on the bagging (Bootstrap Aggregating) technique. Instead of training a single decision tree on the entire training dataset, it builds many decision trees. Each tree is trained on a bootstrap sample, which is created by randomly selecting training samples with replacement. As a result, some observations may appear multiple times in a bootstrap sample.

     In addition, at every split, the algorithm considers only a random subset of features (approximately √number of features for classification) instead of evaluating all features. This increases diversity among the trees.

**4b. Feature ablation study**

    The removed features did contribute some information because removing them caused a minor drop in Auc-score. The performace decreased as 0.0055. So reduced features are not major contributor in stroke prediction. Therefore, reduced model can be considered instead of full model (considering there is not major impact in auc score)
    With fewer features, requires less data for processing, it reduces the memory space,long term maintenance effort in the production (real-world).

**5.Cross-validated comparison:**
    Cross validation gives a more reliable estimate than single train-test model because the model is trained and evaluated multiple times using different subsets of data.
    In 5-fold CV, the dataset is divided into 5 equal parts. During each iteration, 4 folds used for training and one fold is for testing.This process is repeated until every fold has serves as the validation/test set exactly once. The final performance is calculated as the average of the 5 validation scores.
    Using StrtifiedKFold, this ensures that each fold contains same proportion of both classes(stroke and non-stroke).This is importance for this dataset because the targer classes are imbalanced. As a result, CV provides more stable result on how well models are expected to perform on unseen data.


**6.Hyperparameter tuning with GridSearchCV:**
    Model Configuration evaluated :
        hyperparameter configurations : 3 * 3* 2 =18
        Each combination has 5-fold-cross-validation,
            18 * 5 = 90 Random Forest model were trained and evaluated during this grid search.

    Grid search evaluated every possible combination of the specified hyperparameter values.The best combination wil be found here. However, it is expensive and time consuming when there are many hyperparameters.
    RandomForest -> select fixed number fo hyperparameter combinations to evaluate.This makes it much faster and more efficient for large dataset.
    Trade-off doesn't guarantee finding the best combination, but it finds a solution that is very close to the optimum while using less computation.


**7.Manual learning curve**
   Training Fraction  Training AUC  Test AUC
0                0.2      0.980260  0.809081
1                0.4      0.982610  0.818544
2                0.6      0.984624  0.823101
3                0.8      0.986079  0.825641
4                1.0      0.985826  0.827076ve.

    7.a) the training ROC-AUC did not decrease as the training fraction increase.It is high (0.98) across all training set sizes. This indicates that tuned Random forest was able to fit the trainingdata well even as more samples were added. Use of min_samples_leaf =5 control overfitting while still allowing the model to achieve excellent performance on the training data.

    7.b) The test AUC increase  with more training data. The model benefited from having more training examples and that additional data helped imporve its ability to generalize to unseen samples.

    7.c) Conclusion : Test ROC-AUC continued to improve as more training data was used and had not fully plateaud by the time the full trianing dataset was reached. Although improvements became smaller with larger datasets, the upward trend suggests that the model is still somewhat limited by the amount of avialable training data rather its capacity.


**9. Comparison Table:**

Cross-Validation Results :
                 Model  Mean ROC-AUC score   Standard deviation
0  Logistic Regression             0.815782            0.009800
1        Decision Tree             0.794503            0.014910
2        Random Forest             0.827172            0.028313
3    Gradient Boosting             0.837435            0.026170
4  Tuned Random Forest             0.8514

I recommend the Gradient Boosting model for deployment, it has highest ROC-AUC curve indicate it able to distinguish between stroke and non-stroke cases. 
Although the tuned Random Forest achieved the highest cross-validation ROC-AUC (0.8514) during Grid Search, it was not evaluated on the held-out test set in your current workflow.

Without that independent test-set evaluation, the safest recommendation is the Gradient Boosting model, as it demonstrated consistently strong performance on both cross-validation and the test data


======================================================================================================

**PART-IV**

For my Stroke prediction Dataset i'm choosing Tabular REcord Batch Scoring.
    This track used to assess the health risk of a individual patient. Each record is formatted as JSON onject and send to LLM, return risk assessment in json format. The final json is validated aganist the json schema.


**SYSTEM PROMPT :** 

You are a healthcare risk assessment assistant.

Your task is to assess a patient's stroke risk based on the provided health record and return a structured JSON assessment.

Rules:
1. Do not diagnose diseases.
2. Do not prescribe medications.
3. Do not claim that the patient will definitely have a stroke.
4. Assess the patient's overall risk based on age, hypertension, heart disease, average glucose level, BMI, and smoking status.
5. Return ONLY valid JSON.
6. The JSON must contain EXACTLY these five fields:
   - risk_tier
   - flag_for_review
   - primary_signal
   - confidence
   - recommended_action
7. Do not rename any key.
8. Do not include additional fields.

Example Input:
{
    "age": 68,
    "hypertension": 1,
    "heart_disease": 0,
    "avg_glucose_level": 180,
    "bmi": 31,
    "smoking_status": "formerly smoked"
}

Example Output:
{
    "risk_tier": "High",
    "flag_for_review": true,
    "primary_signal": "High glucose level and hypertension",
    "confidence": "High",
    "recommended_action": "Consult a healthcare professional for further evaluation."
}


**USER PROMPT TEMPLATE:**

Assess the following patient record and return ONLY valid JSON.

Patient Profile

Age: <age>
Hypertension: <0 or 1>
Heart Disease: <0 or 1>
Average Glucose Level: <avg_glucose_level>
BMI: <bmi>
Smoking Status: <smoking_status>


**TEMPERATURE = 0** , because this task requires consistent output. At temperature =0, model selects highest probability token at each step, results in deterministic responses. The goal here is structured risk assessment , not creative text generation so low temperature is best choice.


**TEMPERATURE A/B COMPARISON:**

| Input    | Temp = 0                        | Temp = 0.7                            | Key Difference                    |
| -------- | ------------------------------- | ------------------------------------- | --------------------------------- |
| prompt 1 | Low risk, regular check-ups     | Low risk, added BP/glucose monitoring | Slightly different recommendation |
| prompt 2 | Medium risk, consult doctor     | Medium risk, consult doctor           | No difference                     |
| prompt 3 | High risk, immediate evaluation | High risk, personalized assessment    | Recommendation wording changed    |


Temperature controls how LLM select token while generating a response. A temperature with lower value, produce more deterministic and consistent outputs.For the strucutre task, temperature with 0 or sometimes 0.2 is ideal, depends on the usecase. A higher temperature (towards the value of 1), make LLM to produce random values( model hallucinates).


============================================================================================

**3.Structured output handling (all tracks):**

| Input Record                                                                   | LLM Assessment JSON                                                                                                                            | Validation Status |
| ------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- | ----------------- |
| **Record 1:** Age 45, normal glucose, BMI 23, no hypertension, never smoked    | `{"risk_tier":"Low", "flag_for_review":false, "primary_signal":"No significant risk factors", ...}`                                            | **Pass**          |
| **Record 2:** Age 65, hypertension, glucose 180, BMI 31, formerly smoked       | `{"risk_tier":"Medium", "flag_for_review":true, "primary_signal":"High glucose level and hypertension", ...}`                                  | **Pass**          |
| **Record 3:** Age 72, hypertension, heart disease, glucose 210, BMI 34, smoker | `{"risk_tier":"High", "flag_for_review":true, "primary_signal":"High glucose level, hypertension, heart disease, high BMI, and smoking", ...}` | **Pass**          |


================================================================================================
**Demonstrate the feature end-to-end**

| Input                   | LLM Output        | Valid JSON | Pass/Block  |
| ----------------------- | ----------------- | ---------- | ----------- |
| Record 1                | Risk Tier: Low    | Pass       | Pass        |
| Record 2                | Risk Tier: Medium | Pass       | Pass        |
| Record 3                | Risk Tier: High   | Pass       | Pass        |
| Record containing email | Not generated     | N/A        | **Blocked** |

