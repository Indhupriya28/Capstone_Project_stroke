# Capstone_Project_stroke_prediction

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

    Among the numeric columns, Stroke, heart_disease, hypertension has highest value. However, these fields has binary values . So here i'm considering next highest numeric column "Avg_glucose_level" it has numeric values. 
    This column is positively (right)skewed, meaning hew higher values (outliers) are present. In such case, mean is influenced by high values. So median is considered

    Negative Skewness :  id and age. Ignored column "Id" as it doesn't mean anything, just unique id for the row. I could see slightly left skew in the column "Age".This could be presence of young age people which leads to negative skew

Outlier detection with IQR:

----------------------------------
Number of outliers Column:Avg_glucose_level => 629
Number of outliers for column " BMI" => 126

