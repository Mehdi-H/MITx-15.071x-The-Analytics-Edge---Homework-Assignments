Assignment 3 - Detecting Flu
========================================================
author: Mehdi Houacine
date: 17/07/2016

1.1 Reading the data
========================================================



Looking at the time period 2004-2011, which week corresponds to the highest percentage of ILI-related physician visits? 


```r
FluTrain[which.max(FluTrain$ILI),]
```

```
                       Week      ILI Queries
303 2009-10-18 - 2009-10-24 7.618892       1
```

Which week corresponds to the highest percentage of ILI-related query fraction?


```r
FluTrain[which.max(FluTrain$Queries),]
```

```
                       Week      ILI Queries
303 2009-10-18 - 2009-10-24 7.618892       1
```

1.2 - Plotting ILI
==================


```r
hist(FluTrain$ILI)
```

The histogram of ILI presents presents a lot of small values, with a relatively small number of large values, in a skew right fashion.

1.3 - Plot Log(ILI)
========================

When handling a skewed dependent variable, it is often useful to predict the logarithm of the dependent variable instead of the dependent variable itself -- this prevents the small number of unusually large or small observations from having an undue influence on the sum of squared errors of predictive models. 


```r
plot(FluTrain$Queries, log(FluTrain$ILI))
```

Visually, there is a positive, linear relationship between log(ILI) and Queries.

2.1 - Model determinaton
=====================

Based on the plot we just made, it seems that a linear regression model could be a good modeling choice.

Something like log(ILI) = intercept + coefficient x Queries, where the coefficient is positive.

2.2 - First model
=================


```r
FluTrend1 <- lm(log(ILI) ~ Queries, data=FluTrain)
```


```
[1] "The R-Squared value for Trend1 is 0.709020."
```

2.3 R-Squared and Correlation
=============================

For a single variable linear regression model, there is a direct relationship between the R-squared and the correlation between the independent and the dependent variables.

Correlation = cor(FluTrain$Queries, log(FluTrain$ILI))

Correlation^2 = 0.7090201

It appears that Correlation^2 is equal to the R-squared value. It can be proved that this is always the case.

3.1.1 The testing set
=====================


```r
FluTest <- read.csv("FluTest.csv")
```

Normally, we would obtain test-set predictions from the model FluTrend1 using the code

PredTest1 = predict(FluTrend1, newdata=FluTest)

However, the dependent variable in our model is log(ILI), so PredTest1 would contain predictions of the log(ILI) value. We are instead interested in obtaining predictions of the ILI value. The new code, which predicts the ILI value, is


```r
PredTest1 = exp(predict(FluTrend1, newdata=FluTest))
```

3.1.2 The testing set
=====================

What is our estimate for the percentage of ILI-related physician visits for the week of March 11, 2012?


```r
which(FluTest$Week == "2012-03-11 - 2012-03-17")
```

```
[1] 11
```


```
[1] "The percentage of ILI-related physician visits for the week of March 11, 2012 is 2.187378."
```

3.2 - Relative Error
====================


```r
relativeError <- function(observedILI, estimatedILI){
  return((observedILI - estimatedILI) / observedILI)
}
```


```r
observedILI <- FluTest$ILI[11]
estimatedILI <- PredTest1[11]
```


```
[1] "The relative error between our prediction and the observed value for the week of March 11, 2012 is 0.046238."
```

3.3 - RMSE
==========


```r
SSE = sum((PredTest1-FluTest$ILI)^2)
RMSE = sqrt(SSE / nrow(FluTest))
```


```
[1] "The Root Mean Squared Error between our prediction and the actual obs. is 0.749065."
```

4.1.1 - Facing a Time Series Model
==================================

The observations in this dataset are consecutive weekly measurements of the dependent and independent variables. In our models, this means we will predict the ILI variable in the current week using values of the ILI variable from previous weeks.

First, we need to decide the amount of time to lag the observations. Because the ILI variable is reported with a 1- or 2-week lag, a decision maker cannot rely on the previous week's ILI value to predict the current week's value. Instead, the decision maker will only have data available from 2 or more weeks ago. We will build a variable called ILILag2 that contains the ILI value from 2 weeks before the current observation.


4.1.2 - Facing a Time Series Model
==================================


```r
library(zoo)
```


```r
ILILag2 = lag(zoo(FluTrain$ILI), -2, na.pad=TRUE)
FluTrain$ILILag2 = coredata(ILILag2)
```


In these commands, the value of -2 passed to lag means to return 2 observations before the current one; a positive value would have returned future observations. The parameter na.pad=TRUE means to add missing values for the first two weeks of our dataset, where we can't compute the data from 2 weeks earlier.

4.2 - Let's do some plots
=========================


```r
plot(log(FluTrain$ILILag2), log(FluTrain$ILI))
```

There is a strong positive relationship between log(ILILag2) and log(ILI). 

4.3 Model FluTrain2
===================

Let's train a linear regression model on the FluTrain dataset to predict the log of the ILI variable using the Queries variable as well as the log of the ILILag2 variable.


```r
FluTrend2 <- lm(log(ILI) ~ Queries + log(ILILag2), data=FluTrain)

summary(FluTrend2)$coef[,4]
```

```
  (Intercept)       Queries  log(ILILag2) 
 6.471119e-30  1.253513e-44 4.082764e-102 
```


```r
summary(FluTrend2)$r.squared
```

```
[1] 0.9063414
```


5.1 - Evaluating the Time Series Model in the Test Set
=============================================

So far, we have only added the ILILag2 variable to the FluTrain data frame. To make predictions with our FluTrend2 model, we will also need to add ILILag2 to the FluTest data frame.


```r
ILILag2 = lag(zoo(FluTest$ILI), -2, na.pad=TRUE)
FluTest$ILILag2 = coredata(ILILag2)
```

We can notice that there is (still) 2 NA's in this new ILILag2, which is normal due to the time shifting.

5.2.1 - Filling the testing set
=============================

In this problem, the training and testing sets are split sequentially -- the training set contains all observations from 2004-2011 and the testing set contains all observations from 2012. 

There is no time gap between the two datasets, meaning the first observation in FluTest was recorded one week after the last observation in FluTrain. 

From this, we can identify how to fill in the missing values for the ILILag2 variable in FluTest.

5.2.2 - Filling the testing set
===============================

The ILI value of the second-to-last observation in the FluTrain data frame should be used to fill in the ILILag2 variable for the first observation in FluTest.

And the ILI value of the last observation in the FluTrain data frame should be used to fill in the ILILag2 variable for the second observation in FluTest.

The time two weeks before the second week of 2012 is the last week of 2011. This corresponds to the last observation in FluTrain.

5.2.3 - Filling the testing set
===============================

Let's apply this.


```r
FluTest$ILILag2[1] = FluTrain$ILI[416]

FluTest$ILILag2[2] = FluTrain$ILI[417]
```

The new value of the ILILag2 variable in the first row of FluTest is now 1.852736.

And the new value of the ILILag2 variable in the second row of FluTest is 2.12413.


5.4 - FluTrend2 predictions
=====================


```r
PredTest2 = exp(predict(FluTrend2, newdata=FluTest))
```


```r
SSE = sum((PredTest2-FluTest$ILI)^2)
RMSE = sqrt(SSE / nrow(FluTest))
```


```
[1] "The Root Mean Squared Error between our prediction via FluTrend2 and the actual obs. is 0.294203."
```

5.5 - RMSE Model comparison
===========================

The test-set RMSE of FluTrend2 is 0.294, as opposed to the 0.749 value obtained by the FluTrend1 model.

In this problem, we used a simple time series model with a single lag term.

ARIMA models are a more general form of the model we built, which can include multiple lag terms as well as more complicated combinations of previous values of the dependent variable.


```r
?arima
```

