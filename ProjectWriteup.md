Project Writeup Report (by Yong Hee Park)
==========================================

Introduction
------------------------
According to paper, authors recorded sensor readings and added new values from it to the train and test data.

As described in the paper, I decided to use only meaningful predictors (not empty, NA and has no great value).
Even though roll, pitch and yaw (generally called, attitude) can be calculated from sensor readings (i.e., gyro, accelerator and magneto) including all data improved final training model.

Just like choosing predictors, my choice for training method is "Random Forest" as wrote in the paper. I tried other methods but the accuracy was too low.

Here's the list of predictors selected with dimension :
...
> dim(training)
[1] 6540   49
> names(training)
 [1] "roll_belt"         "pitch_belt"        "yaw_belt"         
 [4] "gyros_belt_x"      "gyros_belt_y"      "gyros_belt_z"     
 [7] "accel_belt_x"      "accel_belt_y"      "accel_belt_z"     
[10] "magnet_belt_x"     "magnet_belt_y"     "magnet_belt_z"    
[13] "roll_arm"          "pitch_arm"         "yaw_arm"          
[16] "gyros_arm_x"       "gyros_arm_y"       "gyros_arm_z"      
[19] "accel_arm_x"       "accel_arm_y"       "accel_arm_z"      
[22] "magnet_arm_x"      "magnet_arm_y"      "magnet_arm_z"     
[25] "roll_dumbbell"     "pitch_dumbbell"    "yaw_dumbbell"     
[28] "gyros_dumbbell_x"  "gyros_dumbbell_y"  "gyros_dumbbell_z" 
[31] "accel_dumbbell_x"  "accel_dumbbell_y"  "accel_dumbbell_z" 
[34] "magnet_dumbbell_x" "magnet_dumbbell_y" "magnet_dumbbell_z"
[37] "roll_forearm"      "pitch_forearm"     "yaw_forearm"      
[40] "gyros_forearm_x"   "gyros_forearm_y"   "gyros_forearm_z"  
[43] "accel_forearm_x"   "accel_forearm_y"   "accel_forearm_z"  
[46] "magnet_forearm_x"  "magnet_forearm_y"  "magnet_forearm_z" 
[49] "classe"           
...

I made code run faster with doSNOW. But removed code from this file. So,it may take long time.
It takes almost 9 minutes and shows 98.3% accuracy.

Also, to speed up the training time and make training result more accurate, I choose resampling with 1/3 of total training data :

...
> model.rfcv
>  Random Forest 
>
> 6540 samples
>   48 predictors
>    5 classes: 'A', 'B', 'C', 'D', 'E' 
> 
> No pre-processing
> Resampling: Cross-Validated (10 fold) 
> 
> Summary of sample sizes: 5886, 5885, 5886, 5885, 5886, 5884, ... 
> 
> Resampling results across tuning parameters:
> 
Resampling results across tuning parameters:

  mtry  |Accuracy  |Kappa  |Accuracy SD  |Kappa SD
  ------|----------|-------|-------------|--------
  2     |0.982     |0.977  |0.005        |0.00633 
  ------|----------|-------|-------------|--------
  25    |0.984     |0.979  |0.00402      |0.00509 
  ------|----------|-------|-------------|--------
  48    |0.973     |0.966  |0.00664      |0.0084  

Accuracy was used to select the optimal model using  the largest value.
The final value used for the model was mtry = 25. 

> pred.rf <- predict(model.rfcv,testing)
> pred.rf

[1] B A B A A E D B A A B C B A E E A B B B

...



Here's the R code and result :
-------------------------------


To run the code, copy the CSV files to PC disk and move to it. I copied files to C:\RData.


```r
setwd("C:/RData")
library(caret)
set.seed(3433)

orgTraining <- read.csv("pml-training.csv",head=TRUE,sep=",")
orgTesting <- read.csv("pml-testing.csv",head=TRUE,sep=",")
columnSelect <- which(grepl("^yaw|^pitch|^roll|^gyros|^accel|^magnet|^classe", colnames(orgTraining), ignore.case = T))
training <- orgTraining[,columnSelect]
testing <- orgTesting[,columnSelect]

trainInds <- sample(nrow(training), nrow(training)/3)
training <- training[trainInds,]

dim(training)
names(training)

Sys.time()
trControl <- trainControl(method = "cv",10)
training$classe <- factor(training$classe)
model.rfcv <-train(classe~., data=training, method="rf", trControl=trControl)
Sys.time()
model.rfcv
plot(model.rfcv)
pred.rf <- predict(model.rfcv,testing)
pred.rf
```



```r
setwd("C:/RData")
library(caret)
```

```
## Loading required package: lattice
## Loading required package: ggplot2
```

```r
set.seed(3433)

orgTraining <- read.csv("pml-training.csv",head=TRUE,sep=",")
orgTesting <- read.csv("pml-testing.csv",head=TRUE,sep=",")
columnSelect <- which(grepl("^yaw|^pitch|^roll|^gyros|^accel|^magnet|^classe", colnames(orgTraining), ignore.case = T))
training <- orgTraining[,columnSelect]
testing <- orgTesting[,columnSelect]

trainInds <- sample(nrow(training), nrow(training)/3)
training <- training[trainInds,]

dim(training)
```

```
## [1] 6540   49
```

```r
names(training)
```

```
##  [1] "roll_belt"         "pitch_belt"        "yaw_belt"         
##  [4] "gyros_belt_x"      "gyros_belt_y"      "gyros_belt_z"     
##  [7] "accel_belt_x"      "accel_belt_y"      "accel_belt_z"     
## [10] "magnet_belt_x"     "magnet_belt_y"     "magnet_belt_z"    
## [13] "roll_arm"          "pitch_arm"         "yaw_arm"          
## [16] "gyros_arm_x"       "gyros_arm_y"       "gyros_arm_z"      
## [19] "accel_arm_x"       "accel_arm_y"       "accel_arm_z"      
## [22] "magnet_arm_x"      "magnet_arm_y"      "magnet_arm_z"     
## [25] "roll_dumbbell"     "pitch_dumbbell"    "yaw_dumbbell"     
## [28] "gyros_dumbbell_x"  "gyros_dumbbell_y"  "gyros_dumbbell_z" 
## [31] "accel_dumbbell_x"  "accel_dumbbell_y"  "accel_dumbbell_z" 
## [34] "magnet_dumbbell_x" "magnet_dumbbell_y" "magnet_dumbbell_z"
## [37] "roll_forearm"      "pitch_forearm"     "yaw_forearm"      
## [40] "gyros_forearm_x"   "gyros_forearm_y"   "gyros_forearm_z"  
## [43] "accel_forearm_x"   "accel_forearm_y"   "accel_forearm_z"  
## [46] "magnet_forearm_x"  "magnet_forearm_y"  "magnet_forearm_z" 
## [49] "classe"
```

```r
Sys.time()
```

```
## [1] "2014-06-22 15:23:33 KST"
```

```r
trControl <- trainControl(method = "cv",10)
training$classe <- factor(training$classe)
model.rfcv <-train(classe~., data=training, method="rf", trControl=trControl)
```

```
## Loading required package: randomForest
## randomForest 4.6-7
## Type rfNews() to see new features/changes/bug fixes.
```

```r
Sys.time()
```

```
## [1] "2014-06-22 15:38:51 KST"
```

```r
model.rfcv
```

```
## Random Forest 
## 
## 6540 samples
##   48 predictors
##    5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold) 
## 
## Summary of sample sizes: 5886, 5885, 5886, 5885, 5886, 5884, ... 
## 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy  Kappa  Accuracy SD  Kappa SD
##   2     1         1      0.005        0.006   
##   20    1         1      0.004        0.005   
##   50    1         1      0.007        0.008   
## 
## Accuracy was used to select the optimal model using  the largest value.
## The final value used for the model was mtry = 25.
```

```r
plot(model.rfcv)
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 

```r
pred.rf <- predict(model.rfcv,testing)
pred.rf
```

```
##  [1] B A B A A E D B A A B C B A E E A B B B
## Levels: A B C D E
```

Notes the this document
------------------------

* Knitr is hard to control. I couldn't find out detailed formatting methods in short time.
* So, the output of the prediction show 1 for accurary and kappa.

Thanks!

Yong Hee Park


