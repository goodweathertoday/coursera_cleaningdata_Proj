This is a project to clean up data obtained from
UCI Machine Learning Repository. Specifically, 
the data was obtained from https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip .

You can read the original description of the data here. http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones 


The raw data consist of several files,
where 30 subjects were monitored with a
samusung device. The data were arbitrarily separted into test data and subject data. 

The RunAnalysis.R attempts to select all
variables containg mean and std and present them in a tidy format. 

First, test data and train data are read in, and then combined into a single data set in R. Then the names of the relevant columns are extracted from features_info.txt. This vector of column names are then added to the combined dataset obtained previously and is used as their activity names.

From this newly created table, another tidy data set is created by calculating the the average for each variable of activity and subject. 
