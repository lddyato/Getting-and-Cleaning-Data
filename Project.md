# Instructions

The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set.  

**Review criteria**
The submitted data set is tidy.
The Github repo contains the required scripts.
GitHub contains a code book that modifies and updates the available codebooks with the data to indicate all the variables   
and summaries calculated, along with units, and any other relevant information.
The README that explains the analysis files is clear and understandable.
The work submitted for this project is the work of the student who submitted it.

**Getting and Cleaning Data Course Project**

The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set.   
The goal is to prepare tidy data that can be used for later analysis.   
You will be graded by your peers on a series of yes/no questions related to the project.   
You will be required to submit:  
1) a tidy data set as described below,     
2) a link to a Github repository with your script for performing the analysis, and   
3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md.    
You should also include a README.md in the repo with your scripts.   
This repo explains how all of the scripts work and how they are connected.

One of the most exciting areas in all of data science right now is wearable computing - see for example this article .   
Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

<http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones>

Here are the data for the project:

<https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip>

You should create one R script called run_analysis.R that does the following.

1. Merges the training and the test sets to create one data set.
2. Extracts only the measurements on the mean and standard deviation for each measurement.
3. Uses descriptive activity names to name the activities in the data set
4. Appropriately labels the data set with descriptive variable names.
5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity   
and each subject.

**run_analysis.R**

```r
#download the file and load it into R
if(!file.exists("./data3")) dir.create("./data3")
fileurl <-"https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileurl, destfile="./data3/projectdata.zip")
zipdata <- unzip("./data3/projectdata.zip",exdir="./data3")
trainX <- read.table("./data3/UCI HAR Dataset/train/X_train.txt")
trainY <- read.table("./data3/UCI HAR Dataset/train/y_train.txt")
trainSubject <- read.table("./data3/UCI HAR Dataset/train/subject_train.txt")
testX <- read.table("./data3/UCI HAR Dataset/test/X_test.txt")
testY <- read.table("./data3/UCI HAR Dataset/test/y_test.txt")
testSubject <- read.table("./data3/UCI HAR Dataset/test/subject_test.txt")
features <- read.table("./data3/UCI HAR Dataset/features.txt", stringsAsFactor = FALSE)
activity <- read.table("./data3/UCI HAR Dataset/activity_labels.txt")

#1.Merges the training and the test sets tocreate one data set.
trainData <- cbind(trainX, trainSubject, trainY)
testData <- cbind(testX, testSubject, testY)
data <- rbind(trainData, testData)
dataLables <- rbind(rbind(features,c(562, "Subject")), c(563, "Activityid"))[,2]
names(data) <- dataLables

#2.Extracts only the measurements on the mean and standard deviation for each measurement.
extractdata <- data[, grep("mean|std|Subject|Activityid", names(data))]

#3.Uses descriptive activity names to name the activities in the data set
colnames(activity) <- c("Activityid", "Activity")
extractdata <- merge(extractdata, activity, by = "Activityid")
extractdata <- extractdata[, -1]

# 4. Appropriately labels the data set with descriptive variable names.

names(extractdata) <- gsub("\\()", "", names(extractdata))
names(extractdata) <- gsub("^t", "time", names(extractdata))
names(extractdata) <- gsub("^f", "frequence", names(extractdata))
names(extractdata) <- gsub("-mean", "Mean", names(extractdata))
names(extractdata) <- gsub("-std", "Std", names(extractdata))
names(extractdata) <- gsub("Acc", "Acceleration", names(extractdata))
names(extractdata) <- gsub("Mag", "Magnitude", names(extractdata))
names(extractdata) <- gsub("Gyro", "AngularVelocity", names(extractdata))

# 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
library(dplyr)
sum(is.na(extractdata))
tidyDataMean <- extractdata %>% 
                group_by( Subject, Activity) %>%
                summarise_each(funs(mean))
write.table(tidyDataMean, "./data3/tidyDataMean.txt", row.name=FALSE)
```
