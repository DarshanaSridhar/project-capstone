# Criminal Recidivism
Capstone Project for Data Science Immersive, General Assembly

## Introduction
Recidivism is defined as the act of a person repeating an undesirable behavior after they have either experienced negative consequences of that behaviour (i.e. punished), or have been trained to extinguish that behaviour (i.e. rehabilitated). 
In the context of criminal justice, it is used to refer to the percentage of former prisoners who have been rearrested.
It can be used as an indicator on whether rehabilitation strategies are reducing offender relapse into criminal behavior - this is useful when evaluating the efficacy of prison rehabilitation.

Link to Tableau Public for visuals: 
https://public.tableau.com/profile/darshana.sridhar#!/

Link to final presentation:
https://docs.google.com/presentation/d/1-u1afJpjBhNJoZYmV_sLXbRQs2PLP7oeGhY7yFA335I/edit?usp=sharing

## Problem Statement
After 3 years of rehabilitation, can we predict whether an individual offender will go on to reoffend, break the terms of their parole, or never offend again? 

## Methodology
The aim of this project was to create an accurate, reliable classification model to correctly predict the outcome of rehabilitation. The main goal was to correctly classify individuals to one of 3 categories:

1. No recidivism (i.e. no parole violations or reoffence - no return to prison)
2. Technical violation of parole
3. New offence committed

Other goals included exploration and identification of the most important features (the factors that have the greatest correlation to whether an individual is likely to reoffend or break parole).

The process was straightfoward:
1. Sourcing data
2. EDA & Cleaning
3. Modelling and Tuning
4. Troubleshooting

Success metrics used to evaluate the classification algorithms applied include:
- mean CV score
- precision score
- recall score
- f1 score
- confusion matrices & classification reports
- ROC curves 
- precision-recall curves

## Data Summary
The dataset used for this project was in the form of a csv found at https://data.iowa.gov/Correctional-System/3-Year-Recidivism-for-Offenders-Released-from-Pris/mw8r-vqy4. This data was provided by Iowa Department of Corrections.

The dataset contains 26,020 rows with each row referring to one of 26,020 inmates at an Iowa correctional facility. There are 17 columns (11 of which are useable physical features for modelling, such as race, sex, district etc.) 

This dataset tracks prisoners who were released from this prison between 2010 and 2015. Each prisoner was tracked for 3 years exactly; a three year time frame was used as previous studies have shown if an offender relapses into criminal behavior it is most likely to happen within the first three years of being released.

There are two potential target variables here:
1. Return to Prison (simple Yes/No where Yes aggregates parole violations and new offenses into one class).
2. Recidivism Type (multiclass variable where parole violations and new offenses are distinct classes)

Recidivism Type was chosen as the final target variable as the multiclass aspect would provide more interesting and potentially more valuable insights. 
 
## EDA & Cleaning
Some findings from EDA and cleaning:

1. Disparity in class sizes for target variable
 - No recidivism:        66.63% (highest proportion) 
 - Parole Violation:     12.16%
 - New Offense:          21.19%
 - Combined Recidivism:  33.36%

There are also disparities in group sizes within feature variables. Mapped target variable classes for modelling to (0, 1 and 2) representing no return to prison (indicating no parole violation or reoffense), technical violation of parole and new offense, in that order.

2. Histogram plotted for Days to Return which showed positive skew towards fewer days (more people who did return, returned within the first year)

3. Weighted bipartite network comparing first offense to second offense
   Here, the higher the number, the longer the sentence and hence the more serious the offense. The higher correlation between   same classification level suggests that those who do reoffend tend to commit the same seriousness of offense.

Some assumptions and risks made in cleaning:
1. Dropped columns if over 70% was null
2. Dropped any column that were dependent on target variable (approx 4 columns)
3. Dummified all categorical variables and dropped redundant columns
4. Imputing assumptions (e.g. age; originally given as ranges, but average of these ranges was taken for modelling)

## Modelling
Baseline: 0.666

19 classification algorithms applied; however none perfomed significantly better than the baseline - most were lower, in fact.
4 models achieved a mean CV score higher than baseline, but even this was only by 0.001.
Some training scores were worse than baseline too and most weren’t very high. Precision & recall scores were also investigated, but these were generally no better than baseline. 

The highest performing of these 19 models was Logistic Regression. 

## Troubleshooting
For all of these cases, Logistic Regression was applied first and the model's success evaluated on Mean CV score, in order to compare the initial Logistic Regression model.

1. X_train and y_train indexes compared; identical

2. Chi-square Test on each feature; all p-values close or exactly 0

3. Random OverSampling
   No significant improvement to Mean CV or Recall scores

4. MinMax Scaling
   No significant improvement to Mean CV score

5. Polynomial features 
   'Race_Black', 'Sex_Male' and 'Race_White', 'Sex_Male' tested
    No significant improvement to Mean CV score

6. Modelling on Individual features such as Race, Sex and District
   No significant improvement to Mean CV Score

7. Changing class_weight parameter to ‘balanced'
   No significant improvement to Mean CV score

8. Tuning 
   Tuning by changing penalties improved mean CV score marginally but not significantly.

## Results
The best model achieved through gridsearching Logistic Regression is shown below:

__LogisticRegression(C=0.044984326689694466, class_weight=None, dual=False, fit_intercept=True, intercept_scaling=1, l1_ratio=0.0, max_iter=1000, multi_class='warn', n_jobs=None, penalty='elasticnet', random_state=None, solver='saga', tol=0.0001, verbose=0, warm_start=False)__

This produced an improvement of 0.002 from the baseline.

The most important feature was identified consistently as Average Release Age.

## Next Steps
1. Drop one of the year variables as they are perfectly correlated and model may improve slightly without one of them
2. Gridsearch more models
3. Test other dataset containing similar features to confirm conclusions
4. Compare recidivism in different areas (NY or London vs Iowa)
5. If possible, source more data & remodel using less physical features such as plea type, any expressions of remorse and good/bad behaviour in prison
6. Survival Analysis; how long until a criminal reoffends

## Conclusion
These features are not discriminative enough on their own to give more accurate predictions. These alone provide low predictability despite EDA indicating that modelling should be successful.

Different features may be required in order to provide a more accurate model.
