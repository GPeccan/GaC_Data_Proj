# GaC_Data_Proj_1
Project 1 for the Getting and Cleaning Data class.

Only 1 Script was used to pull, format, and export the data. The code can be seen below.


## The Assignment ##
###You should create one R script called run_analysis.R that does the following. 
    ###Merges the training and the test sets to create one data set.
    ###Extracts only the measurements on the mean and standard deviation for each measurement. 
    ###Uses descriptive activity names to name the activities in the data set
    ###Appropriately labels the data set with descriptive variable names. 

###From the data set in step 4, creates a second,
###independent tidy data set with the average of each variable for each activity and each subject.
###Good luck!


## Set the working directory
setwd("D:/Coursera/Gac Data/Proj 1")

## Find what is in the files
features <- read.table("UCI HAR Dataset/features.txt")
nrow(features)
features

## From looking at the features table, below are the variables and column numbers to include
* tBodyAcc 1-6
* tGravityAcc 41-46
* tBodyAccJerk 81-86
* tBodyGyro 121-126
* tBodyGyroJerk 161-166

* tBodyAccMag 201-202
* tGravityAccMag 214-215
* tBodyAccJerkMag 227-228
* tBodyGyroMag 240-241
* tBodyGyroJerkMag 253-254

* fBodyAcc 266-271
* fBodyAccJerk 345-350
* fBodyGyro 424-429

* fBodyAccMag 503-504
* fBodyBodyAccJerkMag 516-517
* fBodyBodyGyroMag 529-530
* fBodyBodyGyroJerkMag 542-543

## Create a vector to tell the read.table fuction which columns to include
include_col <- c(rep("numeric", 6), rep("NULL", 34),
                 rep("numeric", 6), rep("NULL", 34),
                 rep("numeric", 6), rep("NULL", 34),
                 rep("numeric", 6), rep("NULL", 34),
                 rep("numeric", 6), rep("NULL", 34),
                 rep("numeric", 2), rep("NULL", 11),
                 rep("numeric", 2), rep("NULL", 11),
                 rep("numeric", 2), rep("NULL", 11),
                 rep("numeric", 2), rep("NULL", 11),
                 rep("numeric", 2), rep("NULL", 11),
                 rep("numeric", 6), rep("NULL", 73),
                 rep("numeric", 6), rep("NULL", 73),
                 rep("numeric", 6), rep("NULL", 73),
                 rep("numeric", 2), rep("NULL", 11),
                 rep("numeric", 2), rep("NULL", 11),
                 rep("numeric", 2), rep("NULL", 11),
                 rep("numeric", 2), rep("NULL", 18))


## Read the raw data
raw_test <- read.table("UCI HAR Dataset/test/X_test.txt", colClasses = include_col)
people_test <- read.table("UCI HAR Dataset/test/subject_test.txt")
activity_test <- read.table("UCI HAR Dataset/test/y_test.txt")

raw_train <- read.table("UCI HAR Dataset/train/X_train.txt", colClasses = include_col)
people_train <- read.table("UCI HAR Dataset/train/subject_train.txt")
activity_train <- read.table("UCI HAR Dataset/train/y_train.txt")


## Add column names
col_names <- features[c(1,2,3,4,5,6,
                        41,42,43,44,45,46,
                        81,82,83,84,85,86,
                        121,122,123,124,125,126,
                        161,162,163,164,165,166,
                        201,202,214,215,227,228,240,241,253,254,
                        266,267,268,269,270,271,
                        345,346,347,348,349,350,
                        424,425,426,427,428,429,
                        503,504,516,517,529,530,542,543),2]
colnames(raw_test) <- col_names
colnames(raw_train) <- col_names


## Add activity name to activity data
activity <- read.table("UCI HAR Dataset/activity_labels.txt")
activity_merge_test <- merge(activity_test,activity)
activity_merge_train <- merge(activity_train,activity)


## Combine raw, activity, and people data
raw_test$activity <- activity_merge_test[,2]
raw_test$person <- people_test[,1]

raw_train$activity <- activity_merge_train[,2]
raw_train$person <- people_train[,1]


## Combine test and train data
rownames(raw_test) <- NULL
rownames(raw_train) <- NULL

my_data <- rbind(raw_train, raw_test)


## Create a dataset with the averages by person and activity
summary_data <- aggregate(. ~ activity + person, data = my_data, mean)


## Export the summary data frames as txt
write.table(summary_data,file="summary_data.txt", row.name=FALSE)

