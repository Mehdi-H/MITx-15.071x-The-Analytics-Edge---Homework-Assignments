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


