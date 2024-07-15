# Code book for peer graded assignment of the Getting and Cleaning data course
## Author: Nidevri

## Study design
The data used in this project comes from a study where accelerometer data from Samsung Galaxy S smartphones was collected. A full description can be found at this link:
http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

A summary is given here:
The experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz. The experiments have been video-recorded to label the data manually. The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data. 

The sensor signals (accelerometer and gyroscope) were pre-processed by applying noise filters and then sampled in fixed-width sliding windows of 2.56 sec and 50% overlap (128 readings/window). The sensor acceleration signal, which has gravitational and body motion components, was separated using a Butterworth low-pass filter into body acceleration and gravity. The gravitational force is assumed to have only low frequency components, therefore a filter with 0.3 Hz cutoff frequency was used. From each window, a vector of features was obtained by calculating variables from the time and frequency domain. See 'features_info.txt' for more details. 

For each record it is provided:

- Triaxial acceleration from the accelerometer (total acceleration) and the estimated body acceleration.
- Triaxial Angular velocity from the gyroscope. 
- A 561-feature vector with time and frequency domain variables. 
- Its activity label. 
- An identifier of the subject who carried out the experiment.
  

## Code book


The following is a description of the features provided in the original data sets. Features are normalized and bounded within [-1,1].



The features selected for this database come from the accelerometer and gyroscope 3-axial raw signals tAcc-XYZ and tGyro-XYZ. These time domain signals (prefix 't' to denote time) were captured at a constant rate of 50 Hz. Then they were filtered using a median filter and a 3rd order low pass Butterworth filter with a corner frequency of 20 Hz to remove noise. Similarly, the acceleration signal was then separated into body and gravity acceleration signals (tBodyAcc-XYZ and tGravityAcc-XYZ) using another low pass Butterworth filter with a corner frequency of 0.3 Hz. 

Subsequently, the body linear acceleration and angular velocity were derived in time to obtain Jerk signals (tBodyAccJerk-XYZ and tBodyGyroJerk-XYZ). Also the magnitude of these three-dimensional signals were calculated using the Euclidean norm (tBodyAccMag, tGravityAccMag, tBodyAccJerkMag, tBodyGyroMag, tBodyGyroJerkMag). 

Finally a Fast Fourier Transform (FFT) was applied to some of these signals producing fBodyAcc-XYZ, fBodyAccJerk-XYZ, fBodyGyro-XYZ, fBodyAccJerkMag, fBodyGyroMag, fBodyGyroJerkMag. (Note the 'f' to indicate frequency domain signals). 

These signals were used to estimate variables of the feature vector for each pattern:  
'-XYZ' is used to denote 3-axial signals in the X, Y and Z directions.

tBodyAcc-XYZ
tGravityAcc-XYZ
tBodyAccJerk-XYZ
tBodyGyro-XYZ
tBodyGyroJerk-XYZ
tBodyAccMag
tGravityAccMag
tBodyAccJerkMag
tBodyGyroMag
tBodyGyroJerkMag
fBodyAcc-XYZ
fBodyAccJerk-XYZ
fBodyGyro-XYZ
fBodyAccMag
fBodyAccJerkMag
fBodyGyroMag
fBodyGyroJerkMag

The set of variables that were estimated from these signals are: 

mean(): Mean value
std(): Standard deviation
mad(): Median absolute deviation 
max(): Largest value in array
min(): Smallest value in array
sma(): Signal magnitude area
energy(): Energy measure. Sum of the squares divided by the number of values. 
iqr(): Interquartile range 
entropy(): Signal entropy
arCoeff(): Autorregresion coefficients with Burg order equal to 4
correlation(): correlation coefficient between two signals
maxInds(): index of the frequency component with largest magnitude
meanFreq(): Weighted average of the frequency components to obtain a mean frequency
skewness(): skewness of the frequency domain signal 
kurtosis(): kurtosis of the frequency domain signal 
bandsEnergy(): Energy of a frequency interval within the 64 bins of the FFT of each window.
angle(): Angle between to vectors.

Additional vectors obtained by averaging the signals in a signal window sample. These are used on the angle() variable:

gravityMean
tBodyAccMean
tBodyAccJerkMean
tBodyGyroMean
tBodyGyroJerkMean

## Reproducing the Tidy data set

To reproduce the tidy data set, it is enough to simply save and run the run_analysis.R script. Every required step, including downloading and extracting the dataset is included in the code.
The script performs several tidying steps as was specified in the assignment:

1.Merges the training and the test sets to create one data set.

2.Extracts only the measurements on the mean and standard deviation for each measurement. 

3.Uses descriptive activity names to name the activities in the data set

4.Appropriately labels the data set with descriptive variable names. 

5.From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

## R code for run_analysis.R
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
