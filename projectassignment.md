# Project Assignment Report for Coursera Practical Machine Learning
#R version 3.0.2 (2013-09-25) -- "Frisbee Sailing"
#Platform: x86_64-w64-mingw32/x64 (64-bit)

# Main Assignment
#The goal of this project is to predict "how well" six young men performed barbell lifts  
# The variable "classe" shows classifications of how these exercises were done to the specifications of classe A:
#Class B : throwing the elbows to the front 
#Class C: lifting the dumbbell only halfway 
#Class D lowering the dumbbell only halfway 
#Class E throwing the hips to the front 
#==================================================================#
#Class A corresponds to the specified execution of the exercise, 
#while the other 4 classes correspond to common mistakes.

#Question 1: You should create a report describing how you built your model


#============ Upload libraries needed to build models======================#

> library(AppliedPredictiveModeling)
> library(caret)
> library(rattle)
> library(rpart.plot)
> library(randomForest)

#============ Download datasets into my local drive and upload datasets unto R==========#
> training.as<-read.csv("pml-training.csv",as.is=TRUE)
> testing.as<- read.csv("pml-testing.csv",as.is=TRUE)
> training.as$classe<-factor(training.as$classe)
> training.as$classe<-factor(training.as$classe)
> des(training.as)
# No. of observations =  19622 
                                    
> des(testing.as)
#No. of observations =  20 


#===================== Clean datasets for unwanted variables=========================#

#============Eliminate NA colums  in training and test datasets======================#
                   
> colnames_train <- colnames(training.as)
> without.NAs <- function(x) {
+ as.vector(apply(x, 2, function(x) length(which(!is.na(x)))))
+ }


# Build vector of missing data or NA columns to drop.
> col.to.drop <- without.NAs(training.as)
> drops <- c()
> for (i in 1:length(col.to.drop)) {
+ if (col.to.drop[i] < nrow(training.as)) {
+ drops <- c(drops, colnames_train[i])
+ }
+ }
> training.as <- training.as[,!(names(training.as) %in% drops)]
> testing.as <- testing.as[,!(names(testing.as) %in% drops)]
> des(training.as) #No. of observations =  19622 # variable 93
                 
> des(testing.as) # observation 20, variable 93
 

#========= re-assign data===============#                 
> newdata.training<-training.as
> newdata.test<-testing.as

#crosscheck missing values in training dataset ==#
> any(is.na(newdata.training))
[1] FALSE


#==remove first 7 columns as they are irrelevant===#

> newdata.training.1<-newdata.training[,-c(1:7)]
> colnames(newdata.training.1)# check output
             
> newdata.test.1<-newdata.test[,-c(1:7)] 
> colnames(newdata.test.1) # check output
           
> des(newdata.training.1) # observation 20, variable 86
 

#==consider removing other irrelevant data such as those with variance, kurtosis, skweness and average values===========#                   
> newdata.training.1<-newdata.training.1[,-c(5:13,36:41,45:53,67:75)]
> newdata.test.1<-newdata.test.1[,-c(5:13,36:41,45:53,67:75)]

> des(newdata.training.1) #  No. of observations =  19622 , variable 53
                  
> des(newdata.test.1) #  No. of observations =  20 , variable 53
 


#==Question 2: How you used cross validation

#===========================Algoriths for processing data============================#
#The dataset is very huge and could make working difficult. Many coursera students expressed worry over this.
#Take subsets of data in order to reduce runtime as recommended on cousera discussion==============#


# First. Take 30% of training set                 
> set.seed(200)
> dataset.1 <- createDataPartition(y=newdata.training.1$classe, p=0.30, list=FALSE)
> df_set.1 <- newdata.training.1[dataset.1,] # 30% available
> df_rest.1 <- newdata.training.1[-dataset.1,] #70% available  in remainder

# Dataset 2. Take 40% of training dataset
> set.seed(200)
> dataset.2 <- createDataPartition(y=df_rest.1$classe, p=.40, list=FALSE) #split the rest of data into  40% vrs 60%
> df_set.2 <- df_rest.1[dataset.2,]   # 40%
> df_rest.2 <-df_rest.1[-dataset.2,]  #60%


# Dataset 3 take 50% of training set
> set.seed(200)
> dataset.3 <- createDataPartition(y=df_rest.2$classe, p=0.50, list=FALSE) #split the rest into  # 50% vrs 50%
> df_set.3 <-df_rest.2[dataset.3,]  # 50% of data  into 3


# Dataset 4: Use remainder of training dataset as fourth dataset
> df_set.4 <-df_rest.2[-dataset.3,] # 50% of data into 4
> set.seed(200)
> inTrain <- createDataPartition(y=df_set.1$classe, p=0.6, list=FALSE)     # train first 30% of data into training 1 and test 1
> df_set.1.training <- df_set.1[inTrain,]
> df_set.1.testing <- df_set.1[-inTrain,]
> des(df_set.1.training) # observation 3535
 


# Model evaluation and predictions on data sets using cross validations
#Divide each of the dataset into training (60%) and testing (40%)
#============= Data set 1========================================#
                  
> df_set.1.training$classe<-factor(df_set.1.training$classe) # convert classe to factor variable
> set.seed(200)
> modFit.1 <- train(df_set.1.training$classe ~ ., method="rf", trControl=trainControl(method = "cv",
+ number = 4), data=df_set.1.training)

> print(modFit.1, digits=3)
# output ...........summarised

Summary of sample sizes: 2651, 2652, 2649, 2653 

Resampling results across tuning parameters:

  mtry  Accuracy  Kappa  Accuracy SD  Kappa SD
  2     0.951     0.938  0.00889      0.0113  
  27    0.96      0.95   0.0114       0.0144  
  52    0.954     0.942  0.0127       0.0161  

Accuracy was used to select the optimal model using  the largest value.
The final value used for the model was mtry = 27. 


# make prediction on test data in first sample and assess accuracy

> prediction <- predict(modFit.1, newdata=df_set.1.testing)

# Print and take note of the accuracy value and out of sample error

> print(confusionMatrix(prediction, df_set.1.testing$classe))
Confusion Matrix and Statistics

          Reference
Prediction   A   B   C   D   E
         A 666  14   0   0   0
         B   2 433   9   3   1
         C   0   6 398  11   3
         D   0   1   3 372   3
         E   1   2   0   0 426

Overall Statistics
                                          
               Accuracy : 0.9749          
                 95% CI : (0.9678, 0.9809)
    No Information Rate : 0.2842          
    P-Value [Acc > NIR] : < 2.2e-16       
                                          
                  Kappa : 0.9683          
 Mcnemar's Test P-Value : NA              

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.9955   0.9496   0.9707   0.9637   0.9838
Specificity            0.9917   0.9921   0.9897   0.9964   0.9984
Pos Pred Value         0.9794   0.9665   0.9522   0.9815   0.9930
Neg Pred Value         0.9982   0.9879   0.9938   0.9929   0.9964
Prevalence             0.2842   0.1937   0.1742   0.1640   0.1839
Detection Rate         0.2829   0.1839   0.1691   0.1580   0.1810
Detection Prevalence   0.2889   0.1903   0.1776   0.1610   0.1822
Balanced Accuracy      0.9936   0.9708   0.9802   0.9801   0.9911



#Run against 20 testing data provided in assignment and take note of prediction output

> print(predict(modFit.1, newdata=newdata.test.1))
 [1] B A B A A E D D A A B C B A E E A B B B
Levels: A B C D E



#=================== Dataset 2======================================================================================#

> set.seed(200)
> inTrain <- createDataPartition(y=df_set.2$classe, p=0.6, list=FALSE)  # train second 40% of data
> df_set.2.training<- df_set.2[inTrain,]
> df_set.2.testing <- df_set.2[-inTrain,]
> des(df_set.2.training) # observation 3298
 

# make prediction on test data in second sample and assess accuracy
                 
> df_set.2.training$classe<-factor(df_set.2.training$classe)
> modFit.2 <- train(df_set.2.training$classe ~ ., method="rf", trControl=trainControl(method = "cv",
+ number = 4), data=df_set.2.training)
> print(modFit.2, digits=3)
#............................

  mtry  Accuracy  Kappa  Accuracy SD  Kappa SD
  2     0.952     0.939  0.00208      0.00261 
  27    0.956     0.944  0.00287      0.00364 
  52    0.952     0.939  0.00618      0.00782 

Accuracy was used to select the optimal model using  the largest value.
The final value used for the model was mtry = 27. 
> prediction <- predict(modFit.2, newdata=df_set.2.testing)
> print(confusionMatrix(prediction, df_set.2.testing$classe))
Confusion Matrix and Statistics

          Reference
Prediction   A   B   C   D   E
         A 616  13   0   0   0
         B   7 401  17   1   5
         C   2   8 358   9   1
         D   0   1   5 350   3
         E   0   2   3   0 395

Overall Statistics
                                          
               Accuracy : 0.965           
                 95% CI : (0.9564, 0.9722)
    No Information Rate : 0.2845          
    P-Value [Acc > NIR] : < 2.2e-16       
                                          
                  Kappa : 0.9557          
 Mcnemar's Test P-Value : NA              

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.9856   0.9435   0.9347   0.9722   0.9777
Specificity            0.9917   0.9831   0.9890   0.9951   0.9972
Pos Pred Value         0.9793   0.9304   0.9471   0.9749   0.9875
Neg Pred Value         0.9943   0.9864   0.9863   0.9946   0.9950
Prevalence             0.2845   0.1934   0.1743   0.1639   0.1839
Detection Rate         0.2804   0.1825   0.1629   0.1593   0.1798
Detection Prevalence   0.2863   0.1962   0.1721   0.1634   0.1821
Balanced Accuracy      0.9887   0.9633   0.9619   0.9837   0.9875

# Run against 20 testing provided in assignment

> print(predict(modFit.2, newdata=newdata.test.1))
 [1] B A B A A E D B A A B C B A E E A B B B
Levels: A B C D E

#===================== dataset 3===============================================#
# Train third  50% of data into training 3 and test 3 datasets

> set.seed(200)
> inTrain <- createDataPartition(y=df_set.3$classe, p=0.6, list=FALSE) # train third  50% of data into training 3 and test 3
> df_set.3.training <- df_set.3[inTrain,]
> df_set.3.testing <- df_set.3[-inTrain,]
> df_set.3.training$classe<-factor(df_set.3.training$classe)
> modFit.3 <- train(df_set.2.training$classe ~ ., method="rf", trControl=trainControl(method = "cv",
+ number = 4), data=df_set.2.training)
> print(modFit.3, digits=3)
Random Forest 

3298 samples
  52 predictors
   5 classes: 'A', 'B', 'C', 'D', 'E' 

No pre-processing
Resampling: Cross-Validated (4 fold) 

Summary of sample sizes: 2475, 2473, 2473, 2473 

Resampling results across tuning parameters:

  mtry  Accuracy  Kappa  Accuracy SD  Kappa SD
  2     0.949     0.936  0.0042       0.00532 
  27    0.957     0.946  0.00303      0.00386 
  52    0.954     0.942  0.00299      0.00375 

Accuracy was used to select the optimal model using  the largest value.


#==============make prediction on test data 3 and assess accuracy=================#

The final value used for the model was mtry = 27. 
> prediction <- predict(modFit.3, newdata=df_set.3.testing)
> print(confusionMatrix(prediction, df_set.3.testing$classe))
Confusion Matrix and Statistics

          Reference
Prediction   A   B   C   D   E
         A 463   7   0   0   0
         B   4 303  12   1   0
         C   0   5 268   7   1
         D   1   3   7 262   3
         E   0   0   0   0 298

Overall Statistics
                                          
               Accuracy : 0.969           
                 95% CI : (0.9594, 0.9768)
    No Information Rate : 0.2845          
    P-Value [Acc > NIR] : < 2.2e-16       
                                          
                  Kappa : 0.9608          
 Mcnemar's Test P-Value : NA              

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.9893   0.9528   0.9338   0.9704   0.9868
Specificity            0.9941   0.9872   0.9904   0.9898   1.0000
Pos Pred Value         0.9851   0.9469   0.9537   0.9493   1.0000
Neg Pred Value         0.9957   0.9887   0.9861   0.9942   0.9970
Prevalence             0.2845   0.1933   0.1745   0.1641   0.1836
Detection Rate         0.2815   0.1842   0.1629   0.1593   0.1812
Detection Prevalence   0.2857   0.1945   0.1708   0.1678   0.1812
Balanced Accuracy      0.9917   0.9700   0.9621   0.9801   0.9934


# Run against 20 testing provided in assignment

> print(predict(modFit.3, newdata=newdata.test.1))
 [1] B A B A A E D B A A B C B A E E A B B B
Levels: A B C D E

#=================== Dataset 4=========================================================#
Train 50% of data into training 4 and test 4

> set.seed(200)
> inTrain <- createDataPartition(y=df_set.4$classe, p=0.6, list=FALSE) # train 50% of data into training 4 and test 4
> df_set.4.training <- df_set.4[inTrain,]
> df_set.4.testing <- df_set.4[-inTrain,]
> df_set.4.training$classe<-factor(df_set.4.training$classe)

#Perform random forest on dataset centre and scale to get a best variance in the model

> modFit.4 <- train(df_set.4.training$classe ~ ., method="rf", trControl=trainControl(method = "cv",
+ number = 4), data=df_set.4.training)
> print(modFit.4)
Random Forest 

2473 samples
  52 predictors
   5 classes: 'A', 'B', 'C', 'D', 'E' 

No pre-processing
Resampling: Cross-Validated (4 fold) 

Summary of sample sizes: 1855, 1855, 1853, 1856 

Resampling results across tuning parameters:

  mtry  Accuracy  Kappa  Accuracy SD  Kappa SD
  2     0.94      0.924  0.00661      0.00839 
  27    0.945     0.931  0.0119       0.0151  
  52    0.936     0.919  0.00547      0.00695 

Accuracy was used to select the optimal model using  the largest value.
The final value used for the model was mtry = 27. 


#make prediction on test data

> predictions <- predict(modFit.4, newdata=df_set.4.testing)
> print(confusionMatrix(predictions, df_set.4.testing$classe))
Confusion Matrix and Statistics

          Reference
Prediction   A   B   C   D   E
         A 467  12   0   1   0
         B   1 298   7   2   4
         C   0   7 274  11   5
         D   0   1   6 255   1
         E   0   0   0   1 292

Overall Statistics
                                         
               Accuracy : 0.9641         
                 95% CI : (0.954, 0.9726)
    No Information Rate : 0.2845         
    P-Value [Acc > NIR] : < 2.2e-16      
                                         
                  Kappa : 0.9546         
 Mcnemar's Test P-Value : NA             

Statistics by Class:

                     Class: A Class: B Class: C Class: D Class: E
Sensitivity            0.9979   0.9371   0.9547   0.9444   0.9669
Specificity            0.9890   0.9894   0.9831   0.9942   0.9993
Pos Pred Value         0.9729   0.9551   0.9226   0.9696   0.9966
Neg Pred Value         0.9991   0.9850   0.9904   0.9891   0.9926
Prevalence             0.2845   0.1933   0.1745   0.1641   0.1836
Detection Rate         0.2839   0.1812   0.1666   0.1550   0.1775
Detection Prevalence   0.2918   0.1897   0.1805   0.1599   0.1781
Balanced Accuracy      0.9934   0.9633   0.9689   0.9693   0.9831


# Run against 20 testing provided in assignment
> print(predict(modFit.4, newdata=newdata.test.1))
 [1] B A B A A E D D A A B C B A E E A B B B
Levels: A B C D E

# Question 3: What you think the expected out of sample error
#In week 1 of coursera course. An out of sample error was define the error one gets on a new data
# This will be 1 - (accuracy values for each training prediction on the  testing dataset created)

# Testing set 1 predictions =  1 - 0.9749  =  0.0251
# Testing set 2 predictions =   1 - 0.9663 = 0.0337
#Testing set 3 predictions  =  1- 0.969    = 0.031
#Testing set 4 predictions  =  1 - 0.9623  = 0.031

#Average out of sample error  =  0.0302

#Final Predictions on new dataset with 20 observations is the same for all predictions
# Prediction   B A B A A E D D A A B C B A E E A B B B



