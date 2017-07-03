## Overview

Aim of the analysis is to predict quality of exercise using Weight Lifting Exercise Dataset collected using devices such as Jawbone Up, Nike FuelBand, and Fitbit etc.

A group of 6 participants were asked to perform barbell lifts correctly and incorrectly in 5 different ways. Using the data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants, goal is to quantify how well they do it.

Outcome variable **'Classe'** takes value between A to E and it determines if the exercise has been performed correctly or not. Classe 'A' means the exercise has been performed correctly where as Classe 'B' to 'E' mean the exercise has not been performed correctly.

## Data Cleaning

### Step 1 - Download and load the data in R in two separate data frames training and testing 

```{r}

download.file("https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv", destfile="pml-training.csv")

training<-read.csv("pml-training.csv")

dim(training)

download.file("https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv",destfile="pml-testing.csv")

testing<-read.csv("pml-testing.csv")

dim(testing)
```

### Step 2 - Cleaning the data to keep only variables which are required

There are 160 variables in the training data. Many of these have majority of the row values as NA. Create a clean data set by removing such columns. Also, remove the columns 1 to 7 which are not a predictor of the outcome variable classe.

```{r}
variable<-c("roll_belt","pitch_belt","yaw_belt","total_accel_belt","gyros_belt_x","gyros_belt_y","gyros_belt_z","accel_belt_x","accel_belt_y","accel_belt_z","magnet_belt_x","magnet_belt_y","magnet_belt_z","roll_arm","pitch_arm","yaw_arm","total_accel_arm","gyros_arm_x","gyros_arm_y","gyros_arm_z","accel_arm_x","accel_arm_y","accel_arm_z","magnet_arm_x","magnet_arm_y","magnet_arm_z","roll_dumbbell","pitch_dumbbell","yaw_dumbbell","total_accel_dumbbell","gyros_dumbbell_x","gyros_dumbbell_y","gyros_dumbbell_z","accel_dumbbell_x","accel_dumbbell_y","accel_dumbbell_z","magnet_dumbbell_x","magnet_dumbbell_y","magnet_dumbbell_z","roll_forearm","pitch_forearm","yaw_forearm","total_accel_forearm","gyros_forearm_x","gyros_forearm_y","gyros_forearm_z","accel_forearm_x","accel_forearm_y","accel_forearm_z","magnet_forearm_x","magnet_forearm_y","magnet_forearm_z","classe")

idx <- match(variable, names(training))
training <- training[,idx]

variable<-c("roll_belt","pitch_belt","yaw_belt","total_accel_belt","gyros_belt_x","gyros_belt_y","gyros_belt_z","accel_belt_x","accel_belt_y","accel_belt_z","magnet_belt_x","magnet_belt_y","magnet_belt_z","roll_arm","pitch_arm","yaw_arm","total_accel_arm","gyros_arm_x","gyros_arm_y","gyros_arm_z","accel_arm_x","accel_arm_y","accel_arm_z","magnet_arm_x","magnet_arm_y","magnet_arm_z","roll_dumbbell","pitch_dumbbell","yaw_dumbbell","total_accel_dumbbell","gyros_dumbbell_x","gyros_dumbbell_y","gyros_dumbbell_z","accel_dumbbell_x","accel_dumbbell_y","accel_dumbbell_z","magnet_dumbbell_x","magnet_dumbbell_y","magnet_dumbbell_z","roll_forearm","pitch_forearm","yaw_forearm","total_accel_forearm","gyros_forearm_x","gyros_forearm_y","gyros_forearm_z","accel_forearm_x","accel_forearm_y","accel_forearm_z","magnet_forearm_x","magnet_forearm_y","magnet_forearm_z","problem_id")

idx <- match(variable, names(testing))
testing <- testing[,idx]
```

Using the cleaned data set, fit the model to determine the outcome classe in testing data set.

## Modelling

Divide the training data in to training and validation set.

```{r,warning=FALSE,message=FALSE}
library(caret)
InTrain <- createDataPartition(y=training$classe, p=0.75, list=FALSE)
Training_Final <- training[InTrain, ] 
Validation <- training[-InTrain, ]
```

Fit the random forest model to the above training set.

```{r,warning=FALSE,message=FALSE}
library(randomForest)
model_rf<-randomForest(classe ~ ., data = Training_Final)
prediction<-predict(model_rf,Validation)
confusionMatrix(prediction, Validation$classe)
```

**The expected out of sample error is 1-Accuracy on the cross validation test i.e. 0.43%**

## Prediction on the testing set

Predict the outcome classe for the 20 rows of data in the testing set
```{r}
predict(model_rf,testing)
```

##References

Velloso, E.; Bulling, A.; Gellersen, H.; Ugulino, W.; Fuks, H. Qualitative Activity Recognition of Weight Lifting Exercises. Proceedings of 4th International Conference in Cooperation with SIGCHI (Augmented Human '13) . Stuttgart, Germany: ACM SIGCHI, 2013.

Read more: http://groupware.les.inf.puc-rio.br/har#ixzz4lh7XY8GJ
