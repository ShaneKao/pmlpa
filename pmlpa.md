---
title       : Practical Machine Learning
subtitle    : Prediction Assignment Writeup
author      : Shane Kao
job         : 
logo        : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : 
url:
  lib: ../../librariesNew
  assets: ../../assets
widgets     : [mathjax]            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
---

## Background

The goal of your project is to predict the manner in which they did the exercise. This is the `classe` variable in the training set. You may use any of the other variables to predict with. You should create a report describing how you built your model, how you used cross validation, what you think the expected out of sample error is, and why you made the choices you did. You will also use your prediction model to predict 20 different test cases.

---

## Variables

The following variables to predict with the model.

```
##  [1] "gyros_belt_x"      "gyros_belt_y"      "gyros_belt_z"     
##  [4] "accel_belt_x"      "accel_belt_y"      "accel_belt_z"     
##  [7] "magnet_belt_x"     "magnet_belt_y"     "magnet_belt_z"    
## [10] "gyros_arm_x"       "gyros_arm_y"       "gyros_arm_z"      
## [13] "accel_arm_x"       "accel_arm_y"       "accel_arm_z"      
## [16] "magnet_arm_x"      "magnet_arm_y"      "magnet_arm_z"     
## [19] "gyros_dumbbell_x"  "gyros_dumbbell_y"  "gyros_dumbbell_z" 
## [22] "accel_dumbbell_x"  "accel_dumbbell_y"  "accel_dumbbell_z" 
## [25] "magnet_dumbbell_x" "magnet_dumbbell_y" "magnet_dumbbell_z"
## [28] "gyros_forearm_x"   "gyros_forearm_y"   "gyros_forearm_z"  
## [31] "accel_forearm_x"   "accel_forearm_y"   "accel_forearm_z"  
## [34] "magnet_forearm_x"  "magnet_forearm_y"  "magnet_forearm_z"
```


---

## Machine Learning Algorithm

Training Set: 70%
Test Set:30 %

```r
ind = sample(2, nrow(tidy.training), replace = TRUE, prob = c(0.7, 0.3))
trainData = tidy.training[ind == 1, ]
testData = tidy.training[ind == 2, ]
```

Use `trainData` to fit Conditional Inference Trees 


```r
library(party)
mytree = ctree(classe ~ ., data = trainData)
```


---

## In Sample Error


```r
(is = table(predict(mytree), trainData$classe))
```

```
##    
##        A    B    C    D    E
##   A 3637  163   99   99   38
##   B   92 2273  142   58  121
##   C   60  101 2086  133   80
##   D   79   53   37 1900   50
##   E   20   45   53   57 2219
```

```r
(InSampleError = 1 - sum(diag(is))/sum(is))
```

```
## [1] 0.1154
```


---

## Out of Sample Error


```r
(os = table(predict(mytree, testData), testData$classe))
```

```
##    
##        A    B    C    D    E
##   A 1528   93   53   79   35
##   B   65  893   84   42   67
##   C   27   90  790   68   57
##   D   49   41   34  739   28
##   E   23   45   44   41  912
```

```r
(OutofSampleError = 1 - sum(diag(os))/sum(os))
```

```
## [1] 0.1797
```


---

## Prediction


```r
f = function(x) {
    class(testData[, x])
}
c = apply(cbind(1:37), 1, f)
tidy.testing = testing[, index]
tidy.testing$classe = (tidy.training$classe)[1:20]
write.table(tidy.testing, "tidy.testing.txt", sep = ",", row.names = FALSE)
tidy.testing = read.table("tidy.testing.txt", sep = ",", header = TRUE, colClasses = c)
predict(mytree, tidy.testing)
```

```
##  [1] B A B A A B D B A A D E B A E B A B C B
## Levels: A B C D E
```


