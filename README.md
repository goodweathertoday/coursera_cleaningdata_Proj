This is a project to clean up data obtained from
UCI Machine Learning Repository. Specifically, 
the data was obtained from https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip .

You can read the original description of the data here. http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones 


The raw data consist of several files,
where 30 subjects were monitored with a
samusung device. The data were arbitrarily separted into test data and subject data. 

The RunAnalysis.R attempts to select all
variables containg mean and std and present them in a tidy format. 

First, test data and train data are read in, and then combined into a single data set in R. Then the names of the relevant columns are extracted from features_info.txt in a vector. This vector of column names are then added to the combined dataset obtained previously and is used as their activity names. From this newly created table, another tidy data set is created by calculating the the average for each variable of activity and subject.

description of the code and line by line analysis given below:

##Load training data set
X_train <- read.table("./train/X_train.txt")
subjecttrain <- read.table("./train/subject_train.txt")
y_train <- read.table("./train/y_train.txt")

##Combine training data set into a single data frame
trainingdata <- cbind(subjecttrain, X_train, y_train)

##load testing data set
subjecttest <- read.table("./test/subject_test.txt")
X_test <- read.table("./test/X_test.txt")
y_test <-read.table("./test/y_test.txt")

##Combine testing data set into a single data frame
testdata <- cbind(subjecttest, X_test, y_test)

##combine all training and testing data. This completes Task #1.
alldata <- rbind(trainingdata, testdata)

##the following code obtains the column numbers of means, stds
features <- read.table("features.txt")
cols <- features[grepl("mean\\(\\)", features$V2) | grepl("std\\(\\)", features$V2), "V1"] + 1

##extracts the data containing the mmeans and stds only. This completes
## task 2
extracteddata <- alldata[, c(1, cols, 563)]

##these are the rows of the features table that contain the means and stds
mean_std_rows <- features[grepl("mean\\(\\)", features$V2) | grepl("std\\(\\)", features$V2), "V1"]

##the following assigns the activity names to the proper columns. 
##This completes task 3 and task 4
featurenames <- features[mean_std_rows, "V2"]
names(extracteddata) <- c("Subject", as.character(featurenames), "activity")

temp <- extracteddata$activity
temp2 <- factor(temp, labels = c("Walking", "Walking_Upstars", "Walking_Downstairs", "Sitting", "Standing", "Laying"))
extracteddata$activity <- temp2
extracteddata$activity2 <- alldata[, 563]

##groups extracteddata by subject and activity and then calculates the namnes

temp <- split(extracteddata[, 2], list(extracteddata$Subject, extracteddata$activity))
tidycols <- names(temp)

for(i in 2:67)
{
  groups <- split(extracteddata[, i], list(extracteddata$Subject, extracteddata$activity))
  tempcol <- sapply(groups, mean)
  tidycols <- cbind(tidycols, tempcol)
}

##making the tidy_data frame
subjectcol <- rep(1:30, 6)
tidy_data <- data.frame(tidycols)
names(tidy_data) <- c("Subject", as.character(featurenames))

##clean up the data by separating activity and subject columns
tidy_data2 <- tidy_data
subjectcol <- rep(1:30, 6)
activitycol <- c(rep("Walking", 30), rep("Walking_Upstars", 30), rep("Walking_downstars", 30), rep("Sitting", 30), rep("Standing", 30), rep("Laying", 30)) 
tidy_data3 <- cbind(subjectcol, activitycol, tidy_data2)
tidy_data3$Subject <- NULL
row.names(tidy_data3)<- NULL


##final tidy data form
tidy_data3

##finally writing the table!!
write.table(tidy_data3, "tidy_data.txt")


 
