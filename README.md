# Getting-and-Cleaning-Data-Project

## 1 Merges the training and the test sets to create one data set.
## read the files
## generate the path
path<-file.path(getwd(),"UCI HAR Dataset")

## read activiies 
activities<-read.table(file.path(path,"activity_labels.txt"))
## rename the colume 
names(activities)<- c("ActivetiesID","ActivetiesName")

## read features 
features<-read.table(file.path(path,"features.txt"))
## rename the colume 
names(features)<- c("featuresID","featuresName")

## read train dataset
trainPath <- file.path(path,"train")
## read subjects and name the column
subjectTrain <- read.table(file.path(trainPath,"subject_train.txt"))
names(subjectTrain)[1] <- "subject"
## read the train data
trainSet <- read.table(file.path(trainPath,"X_train.txt"))
## read activeties lables
trainLables <- read.table(file.path(trainPath,"y_train.txt"))
## name the lables column
names(trainLables) <- c("lablesID")
## combine the subject and activeties lables with the train set
trainSet <- cbind(subjectTrain, trainLables,trainSet)


## read test dataset
testPath <- file.path(path,"test")
## read subjects and name the column
subjectTest <- read.table(file.path(testPath,"subject_test.txt"))
names(subjectTest)[1] <- "subject"
## read the test data
testSet <- read.table(file.path(testPath,"X_test.txt"))
## read activeties lables
testLables <- read.table(file.path(testPath,"y_test.txt"))
## name the lables column
names(testLables) <- c("lablesID")
## combine the lables with the train set
testSet <- cbind(subjectTest,testLables,testSet)



## 2 Merges the training and the test sets to create one data set
## Extracts mean(mean()) and standard deviation(std()) 
meanIDs <- features[grep("mean()",features$featuresName),]
stdIDs <- features[grep("std()",features$featuresName),]
extractsCols <- rbind(meanIDs,stdIDs)

## merge into one data set
mergeSet <- rbind( trainSet, testSet )[c("subject","lablesID", paste("V",sep="",extractsCols$featuresID))]

## 3 Uses descriptive activity names to name the activities in the data set
mergeSet <- merge(mergeSet,activities,by.x="lablesID",by.y = "ActivetiesID")

# 4 Appropriately labels the data set with descriptive variable names.
names(mergeSet)[3:81] <- as.character(extractsCols$featuresName)

## 5 From the data set in step 4, 
## creates a second, independent tidy data set 
## with the average of each variable for each activity and each subject.
tidyData <- aggregate(3:81~lablesID+subject,data=mergeSet,mean)
tidyData <- tidyData[order(tidyData$subject,tidyData$lablesID),1:81]
tidyData <- merge(tidyData,activities,by.x="lablesID",by.y = "ActivetiesID")

## export the tidy data to file "tidydata.txt"
write.table(tidyData, file = "tidydata.txt", row.name=FALSE )


