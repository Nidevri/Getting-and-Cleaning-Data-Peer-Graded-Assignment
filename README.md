# Getting-and-Cleaning-Data-Peer-Graded-Assignment

## Getting and Cleaning Data - peer assessment project
The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit: 1) a tidy data set as described below, 2) a link to a Github repository with your script for performing the analysis, and 3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md. You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.
One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:
http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones
Here are the data for the project:
https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip
You should create one R script called run_analysis.R that does the following.
1.	Merges the training and the test sets to create one data set.
2.	Extracts only the measurements on the mean and standard deviation for each measurement.
3.	Uses descriptive activity names to name the activities in the data set
4.	Appropriately labels the data set with descriptive variable names.
5.	From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.


## Explanation of the script

The run_analysis.R script can be run to perform the required steps that were described above.
The script starts by loading the required libraries and, if one is not yet available, creating a directory to download and save the zip file containing the data
The zip file is then unzipped and all relevant data sets are read.

Next, the data sets are merged and given column-names based on the 'features' file.

From this merged dataset, the columns containing mean() or std() values are selected.

Activity numbers are substituted by their descriptive label.

Finally, the mean of the data is taken for each variable and each subject and the data is written to the 'tidyData.txt' file

## An overview of the code is given below:

####################################################
#Preparation: Dowloading data and defining variables
####################################################

#Load required libraries
library(data.table)
library(dplyr)

if(!file.exists("./data")){dir.create("./data")}
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl,destfile="./data/Assignmentdata.zip")

#Unzip the downloaded zip file
zipFile<- "./data/Assignmentdata.zip"
outDir<-"./data/Assignmentdata"
unzip(zipFile,exdir=outDir)

#Reading in the files from the dataset
features<-read.table("./data/Assignmentdata/UCI HAR Dataset/features.txt", header=F)
activity_labels<-read.table("./data/Assignmentdata/UCI HAR Dataset/activity_labels.txt", header=F)

X_test<-read.table("./data/Assignmentdata/UCI HAR Dataset/test/X_test.txt", header=F)
y_test<-read.table("./data/Assignmentdata/UCI HAR Dataset/test/y_test.txt",header=F)
subject_test<-read.table("./data/Assignmentdata/UCI HAR Dataset/test/subject_test.txt",header=F)

X_train<-read.table("./data/Assignmentdata/UCI HAR Dataset/train/X_train.txt",header=F)
y_train<-read.table("./data/Assignmentdata/UCI HAR Dataset/train/y_train.txt",header=F)
subject_train<-read.table("./data/Assignmentdata/UCI HAR Dataset/train/subject_train.txt",header=F)

################################################################
#Step 1: Merge the training and test sets to create one data set
################################################################

X_merged<-rbind(X_train,X_test)
y_merged<-rbind(y_train,y_test)
subject_merged<-rbind(subject_train,subject_test)

#Providing descriptive names is part of Step 4, but to make further processing simpler I will already do this at this point

colnames(X_merged) <- features[,2] #the X_merged table contains all the values for each feature
colnames(y_merged) <- "ActivityID" #the y_merged table contains 1 column specifying the activities corresponding to the data in X_merged
colnames(subject_merged) <- "Subject" #the subject_merged table contains 1 column specifying the subjects corresponding to the data in X_merged

#Merge these 3 datasets into 1. I'll make the subject column 1, the activity column 2 and the features the remaining columns

mergedData <- cbind(subject_merged,y_merged,X_merged)

##########################################################################
#Step 2: Extract only the measurements of the mean and standard deviation
##########################################################################

#use grep to determine the indices of the columns of interest
colOfInterest <- grep("-mean\\(\\)|-std\\(\\)", names(mergedData),ignore.case=T)

#use select to only get the subject, activity and previously determined columns of interest from the merged dataset
dataOfInterest <- select(mergedData,"Subject","ActivityID",names(mergedData)[colOfInterest])

########################################
#Step 3: Use descriptive activity names
########################################

#This can be done by merging the table with activity labels and our data of interest
colnames(activity_labels) <- c("ActivityID", "Activity")

labeledData <-merge(dataOfInterest,activity_labels,by = "ActivityID")

#reshuffle and remove the original 'ActivityID' column
labeledData<- select(labeledData,-(ActivityID))
labeledData<-relocate(labeledData,Activity,.after=Subject)

#############################
#Step 4: Label the variables
#############################

#see step 1

###########################################################################################################
#Step 5: Create a second tidy dataset with the average of each variable for each activity and each subject
###########################################################################################################

tidyData <- aggregate(. ~Subject + Activity, labeledData, mean)
write.table(tidyData,"tidyData.txt",row.names=F)
