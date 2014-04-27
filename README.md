Tidydataset
===========

This is for peer Assignment wherein the gosl id to create a Tidy data set from smartphone data sources

## Read subject/activity , activity/activitydecription  , testing i/p &o/p   & traini/p &o/p from the working directory

 subtrain<-read.table("./UCI HAR Dataset/train/subject_train.txt")
 xtrain<-read.table("./UCI HAR Dataset/train/X_train.txt")
 ytrain<-read.table("./UCI HAR Dataset/train/y_train.txt")

  subtest<-read.table("./UCI HAR Dataset/test/subject_test.txt")
 xtest<-read.table("./UCI HAR Dataset/test/X_test.txt")
 ytest<-read.table("./UCI HAR Dataset/test/y_test.txt")
 

 activity<-read.table("./UCI HAR Dataset/activity_labels.txt")
 activity$V2<-as.character(activity$V2)

 meancolumns<-read.table("./UCI HAR Dataset/features.txt")

##Extracted only means and SD measurements 


means<-grep("mean()",meancolumns$V2)
stds<-grep("std()",meancolumns$V2)
means<-as.data.frame(means)
stds<-as.data.frame(stds)
colnames(means) <- c("index")
colnames(stds) <- c("index")
 indices<-rbind(means,stds)
 indices$index<-as.numeric(indices$index)
indices$index<-indices[order(indices$index),]
meancolumnsnames<-meancolumns$V2[c(indices$index)]

columnname<-data.frame(indices$index,meancolumnsnames)
columnname$meancolumnsnames<-as.character(columnname$meancolumnsnames)

##combined all the sources as subjects/activity/test with subjects/activity/train
dataset<-rbind(xtrain,xtest)
dataset<-dataset[,c(indices$index)]


                    for (i in   1 : ncol(dataset))
                  {  names (dataset) [i] =  columnname$meancolumnsnames[i] 
                    
                     i = i+1 }


dataset1<-cbind(subtrain,ytrain)
dataset2<-cbind(subtest,ytest)

subtesttrain<-rbind(dataset1,dataset2)
names (subtesttrain) [2] = "Activity"             
           
 
 
                    for (i in   1 : nrow(subtesttrain))
                  {    actcounter<-subtesttrain$Activity[i]
                       actcounter<-as.numeric(actcounter)
                       subtesttrain$Activity[i] =  activity$V2[actcounter]
                                             
                            i = i+1 }

##Datset contains the final data for all subjects /activity/measurements
 
dataset<-cbind(subtesttrain,dataset)



##split the data as per the following
##for each subjects 1 to 30 , find each activity and find average for the means and sd for each of the activity for each of the subject
              
subjectsplit<-split(dataset,dataset$V1)

for( subid in 1:30 )
{    
      actvid<-0   
     subject1<-subjectsplit[subid]
     subject1<-as.data.frame(subject1)
      names (subject1) [1] = "Subject" 
                            names (subject1) [2] = "Activity" 
     subject1split<-split(subject1,subject1$Activity)
     for (actvid in 1:6)
               {         
                    subject1split1<-subject1split[actvid]                                                             
                    subject1split1<-as.data.frame(subject1split1)      
                    subject1split12<-subject1split1[,3:81]
                    subject1split12means<-apply(subject1split12,2,mean)
                    subject1split12means<-as.character( subject1split12means)
                    subject1split12means<-t(subject1split12means)
                    subject1split12sub<-subject1split1[1,1:1]
                    subject1split12act<-subject1split1[1,2:2]
               if ( subid ==1 & actvid ==1 )
                      {    row1<-data.frame(subject1split12means)
                            for (i in   1 : ncol(row1))
                                      {  names (row1) [i] =  columnname$meancolumnsnames[i] 
                                          i = i+1 }  

                            row1<-data.frame(subject1split12sub,subject1split12act,row1)
                            names (row1) [1] = "Subject" 
                            names (row1) [2] = "Activity"
                        }
               else

                        {  row2<-data.frame(subject1split12means)                             
                                   for (i in   1 : ncol(row2))
                                        {  names (row2) [i] =  columnname$meancolumnsnames[i] 
                                         i = i+1
                                         }  
                            row2<-data.frame(subject1split12sub,subject1split12act,row2)
                            names (row2) [1] = "Subject" 
                           names (row2) [2] = "Activity"
                           row1<- rbind(row1,row2) 
                         }
              
             }           
          
  }       

tidydataset<-row1        
## write the final tidy dataset as a text file at the working directory named as tidydataset.txt which has 180 observations and 81 columns .79 columns have the measurements and 2 columns have subject and 
## activity code

write.table(file="./tidydataset.txt",x=tidydataset,sep="\t", row.names=FALSE)
 

                
 