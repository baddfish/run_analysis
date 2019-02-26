# Getting and Cleaning Data Course Project

For this project we are given files that need to be merged, cleaned, and correctly labeled to be able to be more readable

# Getting Started

Let's read in the dataset

fileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"

if (!file.exists("dataset.zip")) {
  download.file(fileURL, "dataset.zip", method = "curl")
}
if (!file.exists("UCI HAR Dataset"))
  unzip("dataset.zip")


# Prerequisites

We will want to use the tidyr and dplyr packages

library(tidyr)
library(dplyr

# extract and assign training set

trainx <- read.table("UCI HAR Dataset/train/x_train.txt")
trainy <- read.table("UCI HAR Dataset/train/y_train.txt")
train_subjects <- read.table("UCI HAR Dataset/train/subject_train.txt")

# extract and assign test sets
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

colnames(activity_labels) <- c('activityId','activityType')

# Merging all data in one
merge_train <- cbind(trainy, train_subjects, trainx)
merge_test <- cbind(testy, test_subjects, testx)
setAllInOne <- rbind(merge_train, merge_test)

# reading column names
colNames <- colnames(setAllInOne)

# getting mean and std
mean_and_std <- (grepl("activityId" , colNames) | 
                         grepl("subjectId" , colNames) | 
                         grepl("mean.." , colNames) | 
                         grepl("std.." , colNames) 
)


# subset for allinone
setForMeanAndStd <- setAllInOne[ , mean_and_std == TRUE]

# descriptive activity names
setWithActivityNames <- merge(setForMeanAndStd, activity_labels,
                              by='activityId',
                              all.x=TRUE)

# set clean data set
clean_data <- aggregate(. ~subjectId + activityId, setWithActivityNames, mean)
clean_data <- clean_data[order(clean_data$subjectId, clean_data$activityId),]

write.table(clean_data, "clean_data.txt", row.name=FALSE)

# Authors

* **Tony Maddalone** - *Initial work* - (https://github.com/baddfish)

See also the list of [contributors](https://github.com/your/project/contributors) who participated in this project.

# License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details


