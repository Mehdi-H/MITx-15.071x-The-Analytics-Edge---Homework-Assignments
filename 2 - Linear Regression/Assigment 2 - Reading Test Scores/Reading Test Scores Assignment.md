Reading Test Scores Assignment
========================================================
author: Mehdi Houacine
date: 17/072016

1.1 Dataset Size
========================================================


Let's load the training and testing sets.


```r
pisaTrain <- read.csv("pisa2009train.csv")
pisaTest <- read.csv("pisa2009test.csv")
```


```
[1] "There are 3663 students in the training set."
```

1.2 Summarizing the dataset
========================================================

What is the average reading test score of males ?


```r
tapply(pisaTrain$readingScore, pisaTrain$male, mean)
```

```
       0        1 
512.9406 483.5325 
```


```
[1] "The average reading score for males is 483.532479 and for females is 512.940631."
```

1.3 - Locating missing values
=============================


```r
!is.na(summary(pisaTrain)[7,])
```

We can read which variables have missing values from this command or summary(pisaTrain). Because most variables are collected from study participants via survey, it is expected that most questions will have at least one missing value.

Only 5 of the variables does not have missing data.

1.4 - Removing missing values
=============================


```r
pisaTrain = na.omit(pisaTrain)
pisaTest = na.omit(pisaTest)
```


```
[1] "Now that we have remove observations with missing values, the training set contains 2414 remaining obs. and the testing set contains 990 remaining obs."
```


2.1 - Factor variables
=====================

In our datasets, we have factor variables. Some are unordered like raceeth on 7 levels, some are ordered like grade.

2.2.1 - Unordered factors in regression models
============================================

To include unordered factors in a linear regression model, we define one level as the "reference level" and add a binary variable for each of the remaining levels. In this way, a factor with n levels is replaced by n-1 binary variables. The reference level is typically selected to be the most frequently occurring level in the dataset.

As an example, consider the unordered factor variable "color", with levels "red", "green", and "blue". If "green" were the reference level, then we would add binary variables "colorred" and "colorblue" to a linear regression problem. All red examples would have colorred=1 and colorblue=0. All blue examples would have colorred=0 and colorblue=1. All green examples would have colorred=0 and colorblue=0.

2.2.2 - Unordered factors in regression models
==============================================

Now, consider the variable "raceeth" in our problem, which has levels "American Indian/Alaska Native", "Asian", "Black", "Hispanic", "More than one race", "Native Hawaiian/Other Pacific Islander", and "White". Because it is the most common in our population, we will select White as the reference level.

We should create a binary variable for each level except the reference level, so we would create all these variables except for raceethWhite.

3.1.1 - Building a model
========================

Because the race variable takes on text values, it was loaded as a factor variable when we read in the dataset with read.csv().

However, by default R selects the first level alphabetically ("American Indian/Alaska Native") as the reference level of our factor instead of the most common level ("White").


```r
pisaTrain$raceeth = relevel(pisaTrain$raceeth, "White")
pisaTest$raceeth = relevel(pisaTest$raceeth, "White")
```

Now, the reference level is set to "White".


3.1.2 - Building a model
========================


```r
lmScore <- lm(readingScore ~ ., data=pisaTrain)
```


```
[1] "The multiple R-squared of lmScore of the training set is 0.325143."
```

3.2 - Root-mean squared error
=============================


```r
SSE = sum(lmScore$residuals^2)
RMSE = sqrt(SSE / nrow(pisaTrain))
sprintf("The training-set root-mean squared error (RMSE) of lmScore is %f.", RMSE)
```

```
[1] "The training-set root-mean squared error (RMSE) of lmScore is 73.365551."
```

3.3 - Comparing predictions for similar students
================================================

Consider two students A and B. They have all variable values the same, except that student A is in grade 11 and student B is in grade 9. What is the predicted reading score of student A minus the predicted reading score of student B?


```r
summary(lmScore)$coef[2,1]
```

```
[1] 29.54271
```



The coefficient 29.54 on grade is the difference in reading score between two students who are identical other than having a difference in grade of 1. Because A and B have a difference in grade of 2, the model predicts that student A has a reading score that is 2*29.54 larger.

3.4 - Interpreting model coefficients
=====================================

The variable raceethAsian represents the predicted difference in the reading score between an Asian student and a white student who is otherwise identical.

The only difference between an Asian student and white student with otherwise identical variables is that the former has raceethAsian=1 and the latter has raceethAsian=0. The predicted reading score for these two students will differ by the coefficient on the variable raceethAsian.

3.5 - Identifying variables lacking statistical significance
===============================================

From summary(lmScore), we can see which variables were significant at the 0.05 level. Because several of the binary variables generated from the race factor variable are significant, we should not remove this variable.

4.1 - Predicting on unseen data
===============================


```r
predTest <- predict(lmScore, newdata = pisaTest)
```

From summary(predTest), we see that the maximum predicted reading score is 637.7, and the minimum predicted score is 353.2. Therefore, the range is 284.5.

4.2 - Test set SSE and RMSE
===========================


```r
SSE = sum((predTest-pisaTest$readingScore)^2)
RMSE = sqrt(SSE / nrow(pisaTest))
sprintf("The SSE of lmScore on the testing set is %f.", SSE)
```

```
[1] "The SSE of lmScore on the testing set is 5762082.371144."
```

```r
sprintf("The testing-set root-mean squared error (RMSE) of lmScore is %f.", RMSE)
```

```
[1] "The testing-set root-mean squared error (RMSE) of lmScore is 76.290794."
```

4.3 - Baseline prediction and test-set SSE
==========================================

What is the predicted test score used in the baseline model?

What is the sum of squared errors of the baseline model on the testing set? 


```r
baseline = mean(pisaTrain$readingScore)
SSE = sum((baseline - pisaTest$readingScore)^2)
```


```
[1] "The SSE of the baseline on the testing set is 7802354.077614."
```

```
[1] "The baseline is 517.962887."
```

4.4 - Test-set R-squared
========================


```r
SSE <- sum((predTest-pisaTest$readingScore)^2)
SST <- sum((baseline - pisaTest$readingScore)^2)
R2 <- 1 - SSE/SST
```


```
[1] "The R-squared value of lmScore on the testing set is 0.261494."
```


