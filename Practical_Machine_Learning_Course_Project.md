---
title: "Practical Machine Learning Course Project"
author: "Fernando Marin"
date: "March 20, 2018"
output: 
        html_document:
                keep_md: true
---



## Summary

6 Participants in an experiment were asked to perform barbell lifts correctly and incorrectly while accelerometers were attached to their belt, forearm, arm, and dumbell to take measurements.The objective of the project is to predict whether they did the exercises correctly or incorrectly based on the provided data which is included in the section below.


## Data

Below are the links through which the data was obtained. Also, the variables **trainingData** and **testingData** were created for the **pml-training.csv** and **pml-testing.csv** files, respectively.


```r
# url1 = "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
# download.file(url1, destfile = "./Data/pml-training.csv")
# url2 = "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"
# download.file(url2, destfile = "./Data/pml-testing.csv")
trainingData = read.csv("./Data/pml-training.csv", na.strings = c("NA", "#DIV/0!",""))
testingData = read.csv("./Data/pml-testing.csv", na.strings = c("NA", "#DIV/0!",""))
dim(trainingData);dim(testingData)
```

```
## [1] 19622   160
```

```
## [1]  20 160
```

We can see that our datasets contain 160 variables. The next step is to clean the data and reduce the number of variables

## Cleaning the Data

The amount of predictors was reduced by taking out columns with missing values, and near zero values:


```r
library(caret)
```

```
## Loading required package: lattice
```

```
## Loading required package: ggplot2
```

```r
trainingData <- trainingData[,(colSums(is.na(trainingData)) == 0)]
dim(trainingData)
```

```
## [1] 19622    60
```

```r
testingData <- testingData[,(colSums(is.na(testingData)) == 0)]
dim(testingData)
```

```
## [1] 20 60
```

```r
numSet <- which(lapply(trainingData, class) %in% "numeric")

ppModel <-preProcess(trainingData[,numSet],method=c('knnImpute', 'center', 'scale'))
trainingDataSet <- predict(ppModel, trainingData[,numSet])
trainingDataSet$classe <- trainingData$classe

testingDataSet <-predict(ppModel,testingData[,numSet])

nzv <- nearZeroVar(trainingDataSet,saveMetrics=TRUE)
trainingDataSet <- trainingDataSet[,nzv$nzv==FALSE]

nzv <- nearZeroVar(testingDataSet,saveMetrics=TRUE)
testingDataSet <- testingDataSet[,nzv$nzv==FALSE]
```

The amount of variables is now 60 and all NAs and NZVs have been taken out.Next we perform crossvalidation.


## Splitting the Data

In order to perform crossvalidation we need to create a training subset and a validation subset of the data as folows:


```r
set.seed(1234567890)
valDataset<- createDataPartition(trainingDataSet$classe, p=0.6, list=FALSE)
training<- trainingDataSet[valDataset, ]
validation <- trainingDataSet[-valDataset, ]
```

Next we establish a a method to be used in the model. In this case the method is random forest.



## Model

We load the **randomForest** package and fit a model:


```r
library(randomForest)
```

```
## randomForest 4.6-12
```

```
## Type rfNews() to see new features/changes/bug fixes.
```

```
## 
## Attaching package: 'randomForest'
```

```
## The following object is masked from 'package:ggplot2':
## 
##     margin
```

```r
fitrf <- train(classe ~., method="rf", data=training, trControl=trainControl(method='cv'), number=5, allowParallel=TRUE, importance=TRUE )
fitrf
```

```
## Random Forest 
## 
## 14718 samples
##    27 predictor
##     5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold) 
## Summary of sample sizes: 13247, 13247, 13245, 13247, 13248, 13244, ... 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa    
##    2    0.9930697  0.9912334
##   14    0.9927987  0.9908908
##   27    0.9905562  0.9880547
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was mtry = 2.
```

Now we apply the model to our training dataset:



## Model Accuracy


```r
predValidRF <- predict(fitrf, validation)
cmatrix <- confusionMatrix(validation$classe, predValidRF)
cmatrix$table
```

```
##           Reference
## Prediction    A    B    C    D    E
##          A 2231    0    0    0    1
##          B    0 1517    1    0    0
##          C    0    2 1362    4    0
##          D    0    0    4 1281    1
##          E    0    0    0    1 1441
```

As we can see, only a handful of observations fall outside our predictions, which indicate that our model is fairly accurate. The next step is to calculate the accuracy of the model:


```r
accuracy <- postResample(validation$classe, predValidRF)
modelAccuracy <- accuracy[[1]]
modelAccuracy
```

```
## [1] 0.9982157
```

Based on our alculations, the accuracy of the model is approximately 99.8%, so the out-of-sample error is about 0.2%. The last step is to test our model against the 20 test cases provided for the course project.


## Applying model


```r
modelPrediction <- predict(fitrf, testingDataSet)
modelPrediction
```

```
##  [1] B A B A A E D B A A B C B A E E A B B B
## Levels: A B C D E
```
Based on the predictions given by our model, the answers were submitted on the final course quiz and obtained 20 out of 20 questions correct




