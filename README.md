# run_analysis

Getting and Cleaning Data Course Projectless 
The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit: 1) a tidy data set as described below, 2) a link to a Github repository with your script for performing the analysis, and 3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md. You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.

One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

Here are the data for the project:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

You should create one R script called run_analysis.R that does the following.

Merges the training and the test sets to create one data set.
Extracts only the measurements on the mean and standard deviation for each measurement.
Uses descriptive activity names to name the activities in the data set
Appropriately labels the data set with descriptive variable names.
From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

## read in dataset

fileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"

if (!file.exists("dataset.zip")) {
  download.file(fileURL, "dataset.zip", method = "curl")
}
if (!file.exists("UCI HAR Dataset"))
  unzip("dataset.zip")

# load librarys

library(dplyr)
library(tidyr)
  
## extract and assign training set

trainx <- read.table("UCI HAR Dataset/train/x_train.txt")
trainy <- read.table("UCI HAR Dataset/train/y_train.txt")
train_subjects <- read.table("UCI HAR Dataset/train/subject_train.txt")

## extract and assign test sets
testx <- read.table("UCI HAR Dataset/test/X_test.txt")
testy <- read.table("UCI HAR Dataset/test/y_test.txt")
test_subjects <- read.table("UCI HAR Dataset/test/subject_test.txt")


# setting features
features <- read.table("UCI HAR Dataset/features.txt", stringsAsFactors = FALSE)

# read in activity labels
activity_labels <- read.table("UCI HAR Dataset/activity_labels.txt")

# assignning column names for training sets
colnames(trainx) <- features[,2] 
colnames(trainy) <-"activityId"
colnames(train_subjects) <- "subjectId"

# column names for testing sets

colnames(testx) <- features[,2] 
colnames(testy) <- "activityId"
colnames(test_subjects) <- "subjectId"

##
colnames(activity_labels) <- c('activityId','activityType')

## Merging all data in one
merge_train <- cbind(trainy, train_subjects, trainx)
merge_test <- cbind(testy, test_subjects, testx)
setAllInOne <- rbind(merge_train, merge_test)

## reading column names
colNames <- colnames(setAllInOne)

## getting mean and std
mean_and_std <- (grepl("activityId" , colNames) | 
                         grepl("subjectId" , colNames) | 
                         grepl("mean.." , colNames) | 
                         grepl("std.." , colNames) 
)


## subset for allinone
setForMeanAndStd <- setAllInOne[ , mean_and_std == TRUE]

# descriptive activity names
setWithActivityNames <- merge(setForMeanAndStd, activity_labels,
                              by='activityId',
                              all.x=TRUE)

## set clean data set
clean_data <- aggregate(. ~subjectId + activityId, setWithActivityNames, mean)
clean_data <- clean_data[order(clean_data$subjectId, clean_data$activityId),]

##
write.table(clean_data, "clean_data.txt", row.name=FALSE)


