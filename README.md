# Criminal Recidivism
Capstone Project for Data Science Immersive, General Assembly

# Introduction
Recidivism is defined as the act of a person repeating an undesirable behavior after they have either experienced negative consequences of that behaviour (i.e. punished), or have been trained to extinguish that behaviour (i.e. rehabilitated). 
In the context of criminal justice, it is used to refer to the percentage of former prisoners who have been rearrested.
It can be used as an indicator on whether rehabilitation strategies are reducing offender relapse into criminal behavior - this is useful when evaluating the efficacy of prison rehabilitation.

# Problem Statement
After 3 years of rehabilitation, can we predict whether an individual offender will go on to reoffend, break the terms of their parole, or never offend again? 

# Methodology
In this project, I aimed to create an accurate, reliable classification model to correctly predict the outcome of rehabilitation. My main Goal was to correctly classify individuals to one of 3 categories:

1. No recidivism (i.e. no parole violations or reoffence - no return to prison)
2. Technical violation of parole
3. New offence committed

I also wanted to explore and identify the most important features (the factors that have the greatest correlation to whether an individual is likely to reoffend or break parole) and evaluate efficacy of rehabilitation (where it is less effective if parole violations and new offences are high, and more effective if no recidivism is high).

I also thought it would be interesting to investigate the link between first crime and the seriousness of the second crime, if one was committed - are those who commit more serious crimes likely to commit them again?

My process was straightfoward:
1. Sourcing data
2. EDA & cleaning
3. Modelling and tuning

Success metrics I aimed to use to evaluate the classification algorithms I applied include:
- mean CV score
- precision score
- recall score
- f1 score
- confusion matrices & classification reports, ROC curves and precision-recall curves

# Data Summary
The dataset used for this project was in the form of a csv found at https://data.iowa.gov/Correctional-System/3-Year-Recidivism-for-Offenders-Released-from-Pris/mw8r-vqy4. This data was provided by Iowa Department of Corrections.

The dataset contains 26,020 rows with each row referring to one of 26,020 inmates at an Iowa correctional facility. There are 17 columns (11 of which are useable physical features for modelling, such as race, sex, district etc.) 

There are some other variables here which were interesting to explore for EDA but would need to be removed for modelling as they are dependent on the target variable- these include Days to Return, New Offense Classification, New Offense Subtype and New Offense Type. 

This dataset tracks prisoners who were released from this prison between 2010 and 2015. Each prisoner was tracked for 3 years exactly - a three year time frame was used as previous studies have shown if an offender relapses into criminal behavior it is most likely to happen within the first three years of being released.

There are two potential target variables here:
1. Return to Prison (simple Yes/No where Yes aggregates parole violations and new offences into one class).
2. Recidivism Type (multiclass variable where parole violations and new offences are distinct classes)

I chose to use recidivism type as I felt this would provide more interesting/valuable insights. 
 
# EDA & Cleaning
Some findings from EDA and cleaning:
1. Disparity in class sizes for target variable
No recidivism 66.63% - highest proportion
Parole Violation Rate 12.16%
New Offence Rate 21.19%
Combined Recidivism Rate  33.36%

There are also disparities in group sizes within feature variables.

2. Mapped target variable classes for modelling to (0, 1 and 2) representing no return to prison (indicating no parole violation or reoffence), technical violation of parole and new offense, in that order.

3. Plotted histogram showing positive skew towards fewer days (more people who did return, returned within the first year)

4. Weighted bipartite network
I thought it would be interesting to look at new offenses compared to first offenses and consider their seriousness. There were over 19,000 null values in the new offense classification column so I took only those that had recorded values for second offenses and compared those to the first using a groupby.

I created a weighted bipartite network to compare the two. The higher the number, the more serious the offence. The higher correlation between same classification level uggests that those who do reoffend tend to commit the same seriousness of offence

Some assumptions and risks I made in cleaning:
1. Decided to drop columns if over 70% was null
2. Dropped any column that were dependent on target variable (approx 4 columns)
3. Dummified all categorical variables
4. Imputing assumptions (e.g. age; given as range but chose to take the average of these ranges)

Link to Tableau Public for visuals: 

# Modelling
Baseline: 0.666

19 classification algorithms applied; however none perfomed significantly better than the baseline - most were lower, in fact.
4 models achieved a mean CV score higher than baseline, but even this was only by 0.001.
Some training scores were worse than baseline too and most weren’t very high. Precision & recall scores were also investigated, but these were generally no better than baseline. 

The hghest performing of these 19 models was Logistic Regression. 

# Troubleshooting
The fact that none of my models performed very well lended itself to some troubleshooting. For all of these I did logistic regression and found mean cv.

1. Training set indexes - compare indexes of train_x to train_y (these were identical)

2. Chi-square test on features 
This was done to check how well the selected features would translate to modelling - all were either exactly 0 or very close to 0 (see left) indicating that they could all be used for modelling.

3. Random OverSample 
This gave mean cv 0.6678035594917955; no significant improvement. This should have improved recall, however recall performed worse than baseline at 0.6654496541122213 

4. MinMax Scaling rather than Standard Scaling (mean cv 0.6661222183065547)

5. Polynomial features (df[['Race_Black', 'Sex_Male']]; 0.6663143181569219, White and Male Mean CV Score 0.6663143181569219)

6. Modelling on Individual features 
Race: 0.6663143181569219
Sex: 0.6663143181569219
District: 0.666314318156921

7. Changing class_weight parameter to ‘balanced'

8. Tuning 
Tuning by changing penalties improved this marginally but not significantly.The same applied for other models such as KNN.

# Results
After trying all of these things with no changes I came to the conclusion that these features are not discriminative enough on their own to give more accurate predictions. These alone provide low predictability despite EDA indicating that modelling should be successful.

Different features may be required in order to provide a more accurate model.

Best model achieved through gridsearching logistic regression - this is the best score I managed to get (improvement of 0.002). 

Mean CV Score 0.6682836514898551
Precision score 0.665641813989239
Recall score 0.665641813989239
F1 score 0.665641813989239
Training score 0.668524479892375
Testing score 0.665641813989239
CV scores [0.67675312 0.66330451 0.66922892 0.66450373 0.66762797]

Area under ROC curve (ROC-AUC):
Class 0: 0.67
Class 1: 0.74
Class 2: 0.64


The most important feature consistently was identified as Average Release Age
Decision Tree Classifier: Average Release Age	
Random Forest: Average Release Age
AdaBoost: Average Release Age

# Next Steps
1. Drop one of the year variables as they are perfectly correlated and model may improve slightly without one of them
2. Gridsearch more models
3. Test other dataset containing similar features to confirm conclusions
4. Compare recidivism in different areas (NY or London vs Iowa)
5. If possible source more data & remodel
It would be interesting to look at less physical features such as plea type, any expressions of remorse and good/bad behaviour in prison
6. Survival Analysis; how long until a criminal reoffends

# Conclusion
Even though my model didn’t work as well as hoped, the process was incredibly valuable, especially the troubleshooting I did at the end. As a learning process, pull everything I have learned recently together very interesting.
