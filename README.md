Project Title: Classification of Ocean Microbes in "R"
-----------------------------------------------------

This is a project of Practical Predictive Analytics: Models and Methods by University of Washington (Coursera), working with data from the SeaFlow environmental flow cytometry instrument.A flow cytometer delivers a flow of particles through capilliary. By shining lasers of different wavelengths and measuring the absorption and refraction patterns, you can determine how large the particle is and some information about its color and other properties, allowing you to detect it.The technology was developed for medical applciations, where the particles were potential pathogens in, say, serum, and the goal was to give a diagnosis. But the technology was adapted for use in environmental science to understand microbial population profiles.The SeaFlow instrument, developed by the Armbrust Lab at the University of Washington, is unique in that it is deployed on research vessels and takes continuous measurements of population profiles in the open ocean.

Dataset:
-------

The given dataset represents a 21 minute sample from the vessel in a file seaflow_21min.csv. This sample has been pre-processed to remove the calibration “beads” that are passed through the system for monitoring, as well as some other particle types.

The columns of this dataset are as follows:
file_id, time, cell_id, d1, d2, fsc_small, fsc_perp, fsc_big, pe, chl_small, chl_big, pop

Step 1: Read and Summarize the Data 
-----------------------------------

data <- read.csv("seaflow_21min.csv")
summary(data)

   file_id           time          cell_id            d1       
 Min.   :203.0   Min.   : 12.0   Min.   :    0   Min.   : 1328  
 1st Qu.:204.0   1st Qu.:174.0   1st Qu.: 7486   1st Qu.: 7296  
 Median :206.0   Median :362.0   Median :14995   Median :17728  
 Mean   :206.2   Mean   :341.5   Mean   :15008   Mean   :17039  
 3rd Qu.:208.0   3rd Qu.:503.0   3rd Qu.:22401   3rd Qu.:24512  
 Max.   :209.0   Max.   :643.0   Max.   :32081   Max.   :54048  
       d2          fsc_small        fsc_perp        fsc_big     
 Min.   :   32   Min.   :10005   Min.   :    0   Min.   :32384  
 1st Qu.: 9584   1st Qu.:31341   1st Qu.:13496   1st Qu.:32400  
 Median :18512   Median :35483   Median :18069   Median :32400  
 Mean   :17437   Mean   :34919   Mean   :17646   Mean   :32405  
 3rd Qu.:24656   3rd Qu.:39184   3rd Qu.:22243   3rd Qu.:32416  
 Max.   :54688   Max.   :65424   Max.   :63456   Max.   :32464  
       pe          chl_small        chl_big           pop       
 Min.   :    0   Min.   : 3485   Min.   :    0   crypto :  102  
 1st Qu.: 1635   1st Qu.:22525   1st Qu.: 2800   nano   :12698  
 Median : 2421   Median :30512   Median : 7744   pico   :20860  
 Mean   : 5325   Mean   :30164   Mean   : 8328   synecho:18146  
 3rd Qu.: 5854   3rd Qu.:38299   3rd Qu.:12880   ultra  :20537  
 Max.   :58675   Max.   :64832   Max.   :57184                  


table(data$file_id)

  203   204   205   206   207   208   209 
10454  9435  8376  9215 11444 11995 11424

hist(data$time)

plot(data$file_id,data$cell_id)

plot(data$d1,data$d2)

plot(data$fsc_small,data$fsc_perp)

hist(data$fsc_big)

hist(data$pe)

plot(data$chl_small,data$chl_big)
 
> table(data$pop)

 crypto    nano    pico synecho   ultra 
    102   12698   20860   18146   20537 
> 

Step 2: Split the data into test and train
------------------------------------------

library(caret)

## Loading required package: lattice
## Loading required package: ggplot2

ind <- createDataPartition(y=data$pop, p=0.5, list=F)
mytrain <- data[ind,]
mytest  <- data[-ind,]

mean(mytrain$time)
## [1] 341.3721


step 3: Plot the data
----------------------

library(ggplot2)
#qplot(x=chl_small, y= pe, data=data, fill=as.factor(pop))
ggplot(Train, aes(pe, chl_small, color=pop)) + geom_point()

Step 4: Train a decision tree (rpart)
-------------------------------------

f1 <- formula(pop~fsc_small + fsc_perp + fsc_big + pe + chl_big + chl_small)
#model <- train(f1, data)
library(rpart)
model <- rpart(f1, method="class", data=Train)
print(model)

n= 36173 

node), split, n, loss, yval, (yprob)
      * denotes terminal node

 1) root 36173 25788 pico (0.0014 0.18 0.29 0.25 0.29)  
   2) pe< 5006.5 26295 15964 pico (0 0.22 0.39 7.6e-05 0.39)  
     4) chl_small< 32174.5 11371  1984 pico (0 0 0.83 0.00018 0.17) *
     5) chl_small>=32174.5 14924  6743 ultra (0 0.39 0.063 0 0.55)  
      10) chl_small>=41294.5 5189   669 nano (0 0.87 0 0 0.13) *
      11) chl_small< 41294.5 9735  2223 ultra (0 0.13 0.097 0 0.77) *
   3) pe>=5006.5 9878   800 synecho (0.0052 0.055 0.0055 0.92 0.015)  
     6) chl_small>=38284 659   130 nano (0.077 0.8 0 0.061 0.059) *
     7) chl_small< 38284 9219   181 synecho (0 0.002 0.0059 0.98 0.012) *
> 

Step 5: Evaluate the decision tree on the test data
---------------------------------------------------

preds_rpart <- predict(model, newdata=Test, type="class")
corr_pred <- preds_rpart == Test$pop
accuracy <- sum(corr_pred) / length(corr_pred)
accuracy
[1] 0.8570915


Step 6: Build and Evaluate a random forest
----------------------------------------------

Random forests can automatically generate an estimate of variable importance during training by permuting the values in a given variable and measuring the effect on classification. If scrambling the values has little effect on the model's ability to make predictions, then the variable must not be very important.

A random forest can obtain another estimate of variable importance based on the Gini impurity that we discussed in the lecture. The function importance(model) prints the mean decrease in gini importance for each variable. The higher the number, the more the gini impurity score decreases by branching on this variable, indicating that the variable is more important.

library(randomForest)
rf_model <- randomForest(fol, data=Train)
print(rf_model)
Call:
 randomForest(formula = fol, data = Train) 
               Type of random forest: classification
                     Number of trees: 500
No. of variables tried at each split: 2

        OOB estimate of  error rate: 7.99%
Confusion matrix:
        crypto nano  pico synecho ultra  class.error
crypto      50    0     0       1     0 0.0196078431
nano         1 5550     0       4   791 0.1254333438
pico         0    0 10044      10   331 0.0328358209
synecho      1    1     0    9077     1 0.0003303965
ultra        0  334  1411       5  8561 0.1697216565
> 


Step 7: Train a support vector machine model and compare results.
------------------------------------------------------------------

library(e1071)
model <- svm(f1, data=mytrain)
pred <- predict(model, newdata=mytest,type="class")
table(mytest$pop, pred)
        pred
          crypto  nano  pico synecho ultra
  crypto      47     1     0       3     0
  nano         3  5597     0       5   744
  pico         0     0 10031      64   335
  synecho      1     1    22    9046     3
  ultra        0   377  1407       4  8480


mean(mytest$pop==pred)

 [1] 0.91789

preds_rf <- predict(rf_model, newdata=Test, type="class")
corr_pred_rf <- preds_rf == Test$pop
accuracy_rf <- sum(corr_pred_rf) / length(corr_pred_rf)
accuracy_rf
[1] 0.9207907


Step 8: Construct confusion matrices
------------------------------------

Use the table function to generate a confusion matrix for each of your three methods. Generate predictions using the predict function, then call the table functions like this:

confusion_rpart <- table(rpart=preds_rpart, true = Test$pop)
confusion_rf <- table(rf=preds_rf, true = Test$pop)
confusion_svm <- table(svm=preds_svm, true = Test$pop)

confusion_rpart
         true
rpart     crypto nano pico synecho ultra
  crypto       0    0    0       0     0
  nano        51 5015    1      36   701
  pico         0    4 9454       3  1922
  synecho      0    9   38    9027    98
  ultra        0 1324  982       0  7505

confusion_rf
        true
rf        crypto  nano  pico synecho ultra
  crypto      51     1     0       0     0
  nano         0  5539     0       4   326
  pico         0     0 10111       0  1352
  synecho      0     2     7    9061     5
  ultra        0   810   357       1  8543
> 

confusion_svm

         true
svm       crypto  nano  pico synecho ultra
  crypto      45     1     0       0     0
  nano         2  5595     0       2   374
  pico         0     0 10096      27  1326
  synecho      4     6    41    9035     3
  ultra        0   750   338       2  8523
> 


Step 9: Sanity check the data
---------------------------------

As a data scientist, you should never trust the data, especially if you did not collect it yourself. There is no such thing as clean data. You should always be trying to prove your results wrong by finding problems with the data. Richard Feynman calls it “bending over backwards to show how you're maybe wrong.” This is even more critical in data science, because almost by definition you are using someone else's data that was collected for some other purpose rather than the experiment you want to do. So of course it's going to have problems.

The measurements in this dataset are all supposed to be continuous (fsc_small, fsc_perp, fsc_big, pe, chl_small, chl_big), but one is not. Using plots or R code, figure out which field is corrupted.

ggplot(data, aes(x = time, y = chl_big, colour = pop)) + geom_point()

There is more subtle issue with data as well. Plot time vs. chl_big, and you will notice a band of the data looks out of place. This band corresponds to data from a particular file for which the sensor may have been miscalibrated. Remove this data from the dataset by filtering out all data associated with file_id 208, then repeat the experiment for all three methods, making sure to split the dataset into training and test sets after filtering out the bad data.

data_clean <- data[data$file_id != 208,]
trainIndices_clean <- createDataPartition(data_clean$file_id, p=.5, list=FALSE, times=1)
dataTrain_clean <- data_clean[ trainIndices_clean,]
dataTest_clean  <- data_clean[-trainIndices_clean,]

model_svm_clean <- svm(fol, data=dataTrain_clean)
preds_svm_clean <- predict(model_svm_clean, newdata=dataTest_clean, type="class")
ab <- preds_svm_clean == dataTest_clean$pop
accuracy_svm_clean <- sum(ab) / length(ab)
accuracy_svm_clean
[1] 0.9724588

improvement <- accuracy_svm_clean - accuracy_svm
improvement

[1] 0.05197223

