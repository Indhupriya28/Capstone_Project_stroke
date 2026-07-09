Dataset Details: healthCare_stroke_dataset.csv

Null Value Analysis and Data type correction:
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


8. Correlation Heat map:

    Highest Absolute Correlation : Age and BMI (0.32).
    As Age increases, Bmi increases. However, this is not casual realtionship. Only Age cant be a factor for BMI to increase, third factors also there. Like lack of exercise, food habit.. also matters.
    One plausiable explanation : Lack of Physical activity due to medical conditions or some other factors. This could also add as a reson for the BMI to increase.

9.A Imputation strategy comparison:
    Most Skewed Data: "AVG_GLUCOSE_LEVEL" : It is right skewed (postive) meaning it has outliers. 
    For "BMI" -> Slightly skewed (right).THere is no huge difference between mean and median here. 
    Choosing mean() ,wont be a right option- outlier has impact on the mean. I prefer median() because it consider the middle value/ avg of the middle values. Hence it wont be impacted by the outliers.

9.B Spearman rank correlation
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

PART-II

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
