# Criminal Recidivism
Capstone Project for Data Science Immersive, General Assembly

# Introduction
predicting reoffense because there will be criminal records if people have been sentenced before

Recidivism = act of a person repeating an undesirable behavior after they have either experienced negative consequences of that behavior (punishment), or have been trained to extinguish that behavior (rehabilitation). 

In the context of prisons and inmates it’s used to refer to the percentage of former prisoners who have been rearrested.

Recidivism is used as an indicator on whether rehabilitation strategies are reducing offender relapse into criminal behavior - useful in evaluating the efficacy of prison rehabilitation.

# Problem Statement
After 3 years of rehabilitation, can we predict whether an individual offender will go on to reoffend, break the terms of their parole, or never offend again? 

# Methodology
In this project, I aimed to create an accurate, reliable classification model to correctly predict the outcome of rehabilitation.

Main Goal was to correctly classify individuals to one of 3 categories (no, parole, new offence)

Identify most important features (factors that have the greatest correlation to whether an individual is likely to reoffend or break parole)

Evaluate efficacy of rehabilitation in this prison (less effective if parole violations and recidivism is higher)

Investigate link between first crime and the seriousness of the second crime, if one was committed - are those who commit more serious crimes likely to commit them again?

Process; starting with sourcing data, then clean it, carry out modelling and spend some time tuning (but this didn’t exactly go to plan as I’ll explain)

Success metrics I aim to use to evaluate classification algorithms include mean CV score, precision, recall and f1. Also looked at confusion matrices & classification reports, ROC curves and precision-recall curves

# Data Summary
The dataset I used was in the form of a csv found on a US government website, privided by Iowa dept of corrections

26,020 rows with each row referring to 26,020 inmates at an Iowa correctional facility

17 columns (11 useable physical features such as race, sex etc that I thought would be interesting to model)

Some other variables here that were interesting for EDA but would need to be removed for modelling as they’re dependent on the class of the target variable (days to return, new offense classification etc); these were null for those who didn’t return to prison but had a value for those who did

Tracks prisoners who were released from this prison between 2010 and 2015 
Each prisoner tracked for 3 years

A three year time frame was used as previous studies have shown if an offender relapses into criminal behavior it is most likely to happen within three years of being released.

Two potential target variables here - the first one being Return to Prison (simple Yes/No where Yes aggregates parole violations and new offences into one class).

 The other variable was Recidivism Type (multiclass variable where parole violations and new offences are distinct classes). Chose to use recidivism type as I feel this would provide more interesting/valuable insights. 
 
 # EDA & Cleaning
 Slide 1 disparity in class sizes for target variable
Outcomes categorised as (0, 1 and 2) representing no return to prison (indicating no parole violation or reoffence), technical violation of parole and new offense, in that order.

No recidivism 66.63% - highest proportion
Parole Violation Rate 12.16%
New Offence Rate 21.19%
Combined Recidivism Rate  33.36%

There are also disparities in group sizes within feature variables 

Slide 3 out of those who DID return (so excludng class 0), how quickly did they return to prison. 
This is the distribution for days to return - positive skew towards fewer days (more people who did return, returned within the first year?)

Slide 4 - Offense Classicifications
Thought it would be interesting to look at new offenses compared to first offenses and consider their seriousness. There were over 19,000 null values in the new offense classification column so I took only those that had recorded values for second offenses and compared those to the first using a groupby and create this weighted bipartite network to compare the two. The higher the number, the more serious the offence. 
Higher correlation between same classification level uggests that those who do reoffend tend to commit the same seriousness of offence

Decided to drop if over 70% 
Dropped if dependent on target variable (approx 4 columns)

Dummified categorical variables

Some assumptions made (age; given as range but chose to take the average of these ranges)

# Modelling
Baseline: 0.666
19 classification algorithms tried but they all came out with mean CV scores around the baseline - most lower, in fact
4 models achieved a mean CV score higher than baseline (5th from the top), but even this was only by 0.001
Highest of these was Logistic Regression 
None of these significantly performed better than baseline
Some training scores were worse than baseline too and most weren’t very high

NEXT SLIDE

Also found precision & recall scores but these were generally no better than baseline too 

# Troubleshooting
For all of these I did logistic regression and found mean cv (which are the numbers on the slide now)

Training set indexes - compare indexes of train_x to train_y (these were identical)

Chi-square test on features to check how well they will translate to modelling - all were either exactly 0 or very close to 0 (see left) indicating that they could all be used for modelling

Random Over sample (mean cv 0.6678035594917955)
Random OverSampling recall - 0.6654496541122213

MinMax Scaling (mean cv 0.6661222183065547)

Polynomial features (df[['Race_Black', 'Sex_Male']]; 0.6663143181569219, White and Male Mean CV Score 0.6663143181569219)

Individual features (Race; Mean CV Score 0.6663143181569219, Sex: mean CV 0.6663143181569219, District: 0.6663143181569219)

Changing class_weight parameter to ‘balanced'

Tuning by changing penalties improved this marginally but not significantly and the same applied for my other models such as KNN etc

# Results
After trying all of these things with no changes I came to the conclusion that these features are not discriminative enough on their own to give more accurate predictions

These alone provide low predictability despite EDA indicating that modelling should be successful

Different features may be required in order to provide a more accurate model

This is the best model that I achieved through gridsearching logistic regression - this is the best score I managed to get (0.002). This is the ROC curve for this best model
Precision score 0.665641813989239
Recall score 0.665641813989239
F1 score 0.665641813989239
Training score 0.668524479892375
Testing score 0.665641813989239
CV scores [0.67675312 0.66330451 0.66922892 0.66450373 0.66762797]
Mean CV Score 0.6682836514898551

Area under ROC curve (ROC-AUC):
Class 0: 0.67
Class 1: 0.74
Class 2: 0.64


The most important feature 
Dt classifier: Average Release Age	
Random forest: Average Release Age
Ada boost: Average Release Age

# Next Steps
Drop one of the year variables as they are perfectly correlated and my model may improve slightly without one of them

GRidsearch more models

Test other dataset that I have (has similar features) to confirm conclusions

Compare recidivism in different areas (NY dataset has similar features vs Iowa)

Source more data & remodel; would be interesting to look at less physical features such as plea type, any expressions of remorse and good/bad behaviour in prison

Survival Analysis (how long until an event happens)

# Conclusion
All things considered even though my model didn’t work as well as I hoped it would it was a really valuable process especially the troubleshooting I did at the end - getting to pull everything we’ve learned together was really interesting and I’m really happy with how it turned out in the end.
