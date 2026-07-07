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

    
