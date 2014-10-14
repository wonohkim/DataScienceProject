### Introduction

The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit: 1) a tidy data set as described below, 2) a link to a Github repository with your script for performing the analysis, and 3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md.

One of the most exciting areas in all of data science right now is wearable computing - see for example this article http://www.insideactivitytracking.com/data-science-activity-tracking-and-the-battle-for-the-worlds-top-sports-brand/

Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained: 

### Data Source

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones 

Here are the data for the project: 

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip 


### Tasks

You should create one R script called run_analysis.R that does the following. 
1. Merges the training and the test sets to create one data set.
2. Extracts only the measurements on the mean and standard deviation for each measurement. 
3. Uses descriptive activity names to name the activities in the data set
4. Appropriately labels the data set with descriptive variable names. 
5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

### Code
<!-- -->

install.packages("dplyr", repos='http://cran.us.r-project.org')
library(dplyr)
setwd("~/Desktop/0PGN/2014/Development/R_Course/CleaningData/Project")


subject_test_data=read.table("./subject_test.txt")
subject_train_data=read.table("./subject_train.txt")
subject_data<-rbind(subject_test_data,subject_train_data)
names(subject_data)[1]<-paste("subjectid")

y_test_data=read.table("./y_test.txt")
y_train_data=read.table("./y_train.txt")
y_data<-rbind(y_test_data,y_train_data)
colnames(y_data)[1]<-paste("activityid")

x_test_data=read.table("./X_test.txt")
x_train_data=read.table("./X_train.txt")
x_data<-rbind(x_test_data,x_train_data)
x_data_field_names=read.table("./features.txt")

#There are duplicate field names in the table so combine the field name with a unique number
colnames(x_data)<-paste0(x_data_field_names[,"V2"],x_data_field_names[,"V1"])
whole_data<-cbind(subject_data,y_data,x_data)


#2 Extracts only the measurements on the mean and standard deviation for each measurement. 
#use dplyr and the select function with a regular expression pipe as an OR statement for mean OR std
tdy_x_data<-select(x_data,matches("mean|std"))
tdy_data<-cbind(subject_data,y_data,tdy_x_data)

#3 Uses descriptive activity names to name the activities in the data set

y_data_activity_ids=read.table("./activity_labels.txt")
colnames(y_data_activity_ids)<-c("activityid","activityname")
#adds a new field to y_data, uses y_data_activity_ids as a lookup table for y_data$activityid
y_data<-mutate(y_data, activityname=y_data_activity_ids[y_data$activityid,2])
tdy_data<-cbind(subject_data,y_data,tdy_x_data)

#4 Appropriately labels the data set with descriptive variable names. 
#Names should have no capitals, no spaces, or special characters

colnames(tdy_data)<-tolower(gsub("\\(|\\)|-|,","",colnames(tdy_data)))

#5 From the data set in step 4, creates a second, independent tidy data set 
#with the average of each variable for each activity and each subject.
#use summarise_each function in dplyr to span all the columns for calculating an average instead of naming each

grouped<-group_by(tdy_data,subjectid,activityid)
summary_results<-summarise_each(grouped,funs(mean))



 