Climate Change Assigment
========================================================
author: Mehdi Houacine
date: 16/07/2016

1.1.1 Read the data
========================================================

Let's read the dataset climate_change.csv into R and see its structure.




```
'data.frame':	308 obs. of  11 variables:
 $ Year    : int  1983 1983 1983 1983 1983 1983 1983 1983 1984 1984 ...
 $ Month   : int  5 6 7 8 9 10 11 12 1 2 ...
 $ MEI     : num  2.556 2.167 1.741 1.13 0.428 ...
 $ CO2     : num  346 346 344 342 340 ...
 $ CH4     : num  1639 1634 1633 1631 1648 ...
 $ N2O     : num  304 304 304 304 304 ...
 $ CFC.11  : num  191 192 193 194 194 ...
 $ CFC.12  : num  350 352 354 356 357 ...
 $ TSI     : num  1366 1366 1366 1366 1366 ...
 $ Aerosols: num  0.0863 0.0794 0.0731 0.0673 0.0619 0.0569 0.0524 0.0486 0.0451 0.0416 ...
 $ Temp    : num  0.109 0.118 0.137 0.176 0.149 0.093 0.232 0.078 0.089 0.013 ...
```



1.1.2 - Dataset split
========================================================

Let's split the data into a training set, consisting of all the observations up to and including 2006, and a testing set consisting of the remaining years.


```r
Train <- subset(clim, Year <= 2006)
Test <- subset(clim, Year > 2006)
```


```
[1] "The dataset clim contains 308 obs., the train set contains 284 obs and the test set contains 24 obs."
```



1.1.3 - Dataset split
========================================================

Next, we will build a linear regression model to predict the dependent variable Temp, using MEI, CO2, CH4, N2O, CFC.11, CFC.12, TSI, and Aerosols as independent variables (*Year and Month should not be used in the model*). We will then use the training set to build the model.


```r
model1 = lm(Temp ~ MEI + CO2 + CH4 + N2O + CFC.11 + CFC.12 + TSI + Aerosols, data=clim)
```


Here is the model R2 (the "Multiple R-squared" value):


```r
summary(model1)$r.squared
```

```
[1] 0.743994
```


1.2 - Significant variables of model1
========================================================

Let's see which variables are significant in this first model.
We will consider a variable signficant only if the p-value is below 0.05.


```r
significant_pValues <- summary(model1)$coef[,"Pr(>|t|)"] <= 0.05
```


```
            significant_pValues
(Intercept)                TRUE
MEI                        TRUE
CO2                        TRUE
CH4                       FALSE
N2O                        TRUE
CFC.11                     TRUE
CFC.12                     TRUE
TSI                        TRUE
Aerosols                   TRUE
```

2.1 - (Contradiction in the data)
========================================================

...

2.2.1 - Correlation
========================================================

Which variables is N2O highly correlated with (absolute correlation greater than 0.7)?


```r
cor(Train)["N2O"]
```


```
         cor.Train.....N2O.....0.7
Year                          TRUE
Month                        FALSE
MEI                          FALSE
CO2                           TRUE
CH4                           TRUE
N2O                           TRUE
CFC.11                       FALSE
CFC.12                        TRUE
TSI                          FALSE
Aerosols                     FALSE
Temp                          TRUE
```

2.2.2 - Correlation
========================================================

Which of the following independent variables is CFC.11 highly correlated with?


```r
cor(Train)["CFC.11"]
```


```
         cor.Train.....CFC.11.....0.7
Year                            FALSE
Month                           FALSE
MEI                             FALSE
CO2                             FALSE
CH4                              TRUE
N2O                             FALSE
CFC.11                           TRUE
CFC.12                           TRUE
TSI                             FALSE
Aerosols                        FALSE
Temp                            FALSE
```

3.1 - Simplified Model
========================================================

Given that the correlations are so high, let us focus on the N2O variable and build a model with only MEI, TSI, Aerosols and N2O as independent variables.


```r
model2 <- lm(Temp ~ N2O + MEI + TSI + Aerosols, data=Train)
```


```
[1] "In this reduced model, the coefficient of N2O is 0.025320. It was -0.016929 in the previous model1."
```

```
[1] "The R^2 of model2 is 0.726132. It was 0.743994 in model1."
```

3.2 - Simplified Model - Explanation
========================================================


We have observed that, for this problem, when we remove many variables the sign of N2O flips.

The model has not lost a lot of explanatory power (the model R2 is 0.7261 compared to 0.7509 previously) despite removing many variables. 

As discussed in lecture, this type of behavior is typical when building a model where many of the independent variables are highly correlated with each other. 

In this particular problem many of the variables (CO2, CH4, N2O, CFC.11 and CFC.12) are highly correlated, since they are all driven by human industrial development.

4.1.1 - Akaike Information Criterion
========================================================

Let's try the AIC trade-off with the R step() function.


```
Start:  AIC=-1348.81
Temp ~ Year + Month + MEI + CO2 + CH4 + N2O + CFC.11 + CFC.12 + 
    TSI + Aerosols

           Df Sum of Sq    RSS     AIC
- CH4       1   0.00103 2.2765 -1350.7
- Year      1   0.00127 2.2767 -1350.7
- CO2       1   0.00500 2.2804 -1350.2
- N2O       1   0.00650 2.2819 -1350.0
<none>                  2.2754 -1348.8
- Month     1   0.02296 2.2984 -1348.0
- CFC.12    1   0.03254 2.3080 -1346.8
- CFC.11    1   0.06270 2.3381 -1343.1
- TSI       1   0.22111 2.4965 -1324.5
- Aerosols  1   0.41084 2.6863 -1303.7
- MEI       1   0.82417 3.0996 -1263.0

Step:  AIC=-1350.68
Temp ~ Year + Month + MEI + CO2 + N2O + CFC.11 + CFC.12 + TSI + 
    Aerosols

           Df Sum of Sq    RSS     AIC
- Year      1   0.00079 2.2772 -1352.6
- CO2       1   0.00536 2.2818 -1352.0
- N2O       1   0.00550 2.2820 -1352.0
<none>                  2.2765 -1350.7
- Month     1   0.02444 2.3009 -1349.7
- CFC.12    1   0.04187 2.3183 -1347.5
- CFC.11    1   0.06656 2.3430 -1344.5
- TSI       1   0.22217 2.4986 -1326.2
- Aerosols  1   0.42088 2.6973 -1304.5
- MEI       1   0.82352 3.1000 -1265.0

Step:  AIC=-1352.59
Temp ~ Month + MEI + CO2 + N2O + CFC.11 + CFC.12 + TSI + Aerosols

           Df Sum of Sq    RSS     AIC
- N2O       1   0.00964 2.2869 -1353.4
- CO2       1   0.01065 2.2879 -1353.3
<none>                  2.2772 -1352.6
- Month     1   0.03626 2.3135 -1350.1
- CFC.12    1   0.14103 2.4183 -1337.5
- CFC.11    1   0.15006 2.4273 -1336.5
- TSI       1   0.31677 2.5940 -1317.6
- Aerosols  1   0.45378 2.7310 -1303.0
- MEI       1   0.84345 3.1207 -1265.1

Step:  AIC=-1353.39
Temp ~ Month + MEI + CO2 + CFC.11 + CFC.12 + TSI + Aerosols

           Df Sum of Sq    RSS     AIC
- CO2       1   0.00297 2.2899 -1355.0
<none>                  2.2869 -1353.4
- Month     1   0.05794 2.3448 -1348.3
- CFC.12    1   0.17632 2.4632 -1334.3
- CFC.11    1   0.18267 2.4696 -1333.6
- TSI       1   0.30713 2.5940 -1319.6
- Aerosols  1   0.46771 2.7546 -1302.5
- MEI       1   0.84843 3.1353 -1265.8

Step:  AIC=-1355.02
Temp ~ Month + MEI + CFC.11 + CFC.12 + TSI + Aerosols

           Df Sum of Sq    RSS     AIC
<none>                  2.2899 -1355.0
- Month     1   0.09251 2.3824 -1345.8
- TSI       1   0.31006 2.5999 -1321.0
- Aerosols  1   0.48540 2.7753 -1302.4
- MEI       1   0.86442 3.1543 -1266.1
- CFC.11    1   1.11520 3.4051 -1244.3
- CFC.12    1   2.86967 5.1595 -1126.3
```

4.1.2 - Akaike Information Criterion
========================================================


```
[1] "The R^2 of the newly simplified model is 0.753387."
```

The CH4 has been removed now.

5.1 - Test on unseen data
========================================================

Let's try to calculate temperature predictions for the testing data set, using the predict function.


```r
TempPrediction <- predict(simpleModel, newdata=Test)
SSE = sum((TempPrediction - Test$Temp)^2)
SST = sum( (mean(Train$Temp) - Test$Temp)^2)
OutOfSampleR2 <- 1 - SSE/SST
```



```r
sprintf("The R2 on the test set is %f.", OutOfSampleR2)
```

```
[1] "The R2 on the test set is 0.643174."
```

