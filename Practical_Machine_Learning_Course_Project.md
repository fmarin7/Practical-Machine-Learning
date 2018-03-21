---
title: "Practical Machine Learning Course Project"
author: "Fernando Marin"
date: "March 20, 2018"
output: 
        html_document:
                keep_md: true
---



## Summary



## Data

Below are the links through which the data was obtained. Also, the variables **trainingData** and **testingData** were created for the **pml-training.csv** and **pml-testing.csv** files, respectively. 


```r
# url1 = "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
# download.file(url1, destfile = "./Data/pml-training.csv")
# url2 = "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"
# download.file(url2, destfile = "./Data/pml-testing.csv")
trainingData = read.csv("./Data/pml-training.csv")
testingData = read.csv("./Data/pml-testing.csv")
```

## Cleaning the Data



## Splitting the Data



## Model



## Model Accuracy




## Applying model



## Conclusion



