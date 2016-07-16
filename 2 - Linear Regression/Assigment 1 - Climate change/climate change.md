climate change Assigment
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

