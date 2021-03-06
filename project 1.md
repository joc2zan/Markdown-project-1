---
title: "Project Monitoring Activity"
author: "Jose Maria Chadid"
date: "10/22/2020"
output:
  html_document: default
  pdf_document: default
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = FALSE)
```

## Activity Data Monitoring

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

##Code for reading in the dataset and/or processing the data

###File destination downloading
```{r echo=TRUE}

fileData = download.file(url = "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip",
                         destfile = "./ActivityMornigData", method = "curl")

```
###Unzip the file downloaded
```{r echo=TRUE}
activity_file<-unzip(zipfile = "ActivityMornigData") 

```

###Reading the activity csv file
```{r echo=TRUE}
activity=read.csv(file = activity_file,header = TRUE, sep = ",")

```

###Summary data Steps
```{r echo=TRUE}
summary(activity)

```

##Histogram of the total number of steps taken each day
```{r}
steps <- aggregate(steps ~ date, activity, sum)
hist(steps$steps, main = "Steps Activity", col="blue",xlab="Steps")
png(filename = "./Data scientisc/markdown lessons/lesson 2/Steps_activity.png", width = 600, height = 480, units = "px")

```

## Mean and median number of steps taken each day

```{r echo=FALSE}
#mean per day
steps_mean<-mean(steps$steps)
print("Mean")
steps_mean

#median per day
steps_median<-median(steps$steps)
print("Median")
steps_median
```

##Time series plot of the average number of steps taken
```{r}
# Interval steps
steps_intervals <- aggregate(steps ~ interval, activity, mean)

# ploting intervals
plot(steps_intervals$interval,steps_intervals$steps, type="l",main="Steps per Day",
     xlab="Intervals", ylab="Steps",sub = "Average by intervals")
png(filename = "./Data scientisc/markdown lessons/lesson 2/steps_intervals.png", width = 600, height = 480, units = "px")


```
##The 5-minute interval that, on average, contains the maximum number of steps
```{r}
steps_intervals$interval[steps_intervals$steps==max(steps_intervals$steps)]
```
##Code to describe and show a strategy for imputing missing data
```{r echo=TRUE}
# Missing data
# As in the introduction said, missing values are treated as 'NAs'. 
# Step's column in the dataset is the only one who has NAs values. 
# A good filter of the dataset is the rows with no missing values in any columns, even if Nas values are only in Step's column.
clean_activity<-complete.cases(activity)
sum(!clean_activity)

# Histogram of the total number of steps taken each day after missing values are imputed
# new dataset fill NAs
act_fill<-function(){
  activity_fill<-activity
  for(i in 1:length(activity_fill[,1])){
    if(is.na(activity_fill[i,1])){
      activity_fill[i,1]<- steps_intervals$steps[steps_intervals$interval==activity[i,3]]
     }
    
  }
activity_fill
}

# new variable with steps replaced with mean data intervals
activity_fill<-act_fill()
```

##Histogram of the total number of steps taken each day after missing values are imputed
```{r}
#Compare Histograms with differences
activity_fill_hist <- aggregate(steps ~ date, data = activity_fill, sum, na.rm = TRUE)
hist(activity_fill_hist$steps, main = paste("Steps per Day"), col="blue", xlab="Steps")
hist(steps$steps, col="green", main = paste("Steps per Day"), xlab="Steps",add=T)
legend("topright", c("Imputed", "Non-imputed"), col=c("blue", "green"), lwd=2,cex = 0.6)
```

##Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends

```{r}
# Mean fill Nas data
mean_fill<-median(activity_fill_hist$steps)
mean_fill

# Median fill NAs data
median_fill<-median(activity_fill_hist$steps)
median_fill

# Do these values differ from the estimates from the first part of the assignment
total_mean <- mean_fill - steps_mean
total_mean


total_media <- median_fill - steps_median
total_media

#Are there differences in activity patterns between weekdays and weekends?
# plots compare between weekdays and weekends
activity_fill_hist$date<-weekdays(as.Date(activity_fill_hist$date))
hist_weekdend<- activity_fill_hist[activity_fill_hist$date %in% c("Saturday","Sunday") ,]
hist_weekdays <- activity_fill_hist[activity_fill_hist$date %in% 
                                     c("Monday","Tuesday","Wednesday","Thursday","Friday") ,]

hist_weekdend$date<-as.factor(hist_weekdend$date)
hist_weekdays$date<-as.factor(hist_weekdays$date)

par(mfrow=c(1,2))
plot(hist_weekdend$steps,main = "Weekend steps", xlab = "Intervals",col="green",type="l")
plot(hist_weekdays$steps,main = "Weekdays steps",xlab = "Intervals", col="blue",type="l")
```

##All of the R code needed to reproduce the results (numbers, plots, etc.) in the report
```{r eval=FALSE,include=TRUE,echo=TRUE}
library(rmarkdown)
# File destination downloading
fileData = download.file(url = "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip",
                         destfile = "./ActivityMornigData", method = "curl")
# unzip the file downloaded
activity_file<-unzip(zipfile = "ActivityMornigData") 

# Reading the activity csv file
activity=read.csv(file = activity_file,header = TRUE, sep = ",")

#summary data steps
summary(activity)

steps <- aggregate(steps ~ date, activity, sum)
hist(steps$steps, main = "Steps Activity", col="blue",xlab="Steps")

#mean per day
steps_mean<-mean(steps$steps)
steps_mean

#median per day
steps_median<-median(steps$steps)
steps_median
# Interval steps
steps_intervals <- aggregate(steps ~ interval, activity, mean)

# ploting intervals
plot(steps_intervals$interval,steps_intervals$steps, type="l",main="Steps per Day",
     xlab="Intervals", ylab="Steps",sub = "Average by intervals")

#The 5-minute interval that, on average, contains the maximum number of steps
steps_intervals$interval[steps_intervals$steps==max(steps_intervals$steps)]

# Missing data
# As in the introduction said, missing values are treated as 'NAs'. 
# Step's column in the dataset is the only one who has NAs values. 
# A good filter of the dataset is the rows with no missing values in any columns, even if Nas values are only in Step's column.
clean_activity<-complete.cases(activity)
sum(!clean_activity)

# Histogram of the total number of steps taken each day after missing values are imputed
# new dataset fill NAs
act_fill<-function(){
  activity_fill<-activity
  for(i in 1:length(activity_fill[,1])){
    if(is.na(activity_fill[i,1])){
      activity_fill[i,1]<- steps_intervals$steps[steps_intervals$interval==activity[i,3]]
     }
    
  }
activity_fill
}

# new variable with steps replaced with mean data intervals
activity_fill<-act_fill()

#Compare Histograms with differences
activity_fill_hist <- aggregate(steps ~ date, data = activity_fill, sum, na.rm = TRUE)
hist(activity_fill_hist$steps, main = paste("Steps per Day"), col="blue", xlab="Steps")
#Difference 
hist(steps$steps, col="green", xlab="Steps", legend=c("Imputed", "Non-imputed"))

# Mean fill Nas data
mean_fill<-median(activity_fill_hist$steps)
mean_fill

# Median fill NAs data
median_fill<-median(activity_fill_hist$steps)
median_fill

# Do these values differ from the estimates from the first part of the assignment
total_mean <- mean_fill - steps_mean
total_mean


total_media <- median_fill - steps_median
total_media

#Are there differences in activity patterns between weekdays and weekends?
# plots compare between weekdays and weekends
activity_fill_hist$date<-weekdays(as.Date(activity_fill_hist$date))
hist_weekdend<- activity_fill_hist[activity_fill_hist$date %in% c("Saturday","Sunday") ,]
hist_weekdays <- activity_fill_hist[activity_fill_hist$date %in% 
                                     c("Monday","Tuesday","Wednesday","Thursday","Friday") ,]

hist_weekdend$date<-as.factor(hist_weekdend$date)
hist_weekdays$date<-as.factor(hist_weekdays$date)

par(mfrow=c(1,2))
plot(hist_weekdend$steps,main = "Weekend steps", xlab = "Intervals",col="green",type="l")
plot(hist_weekdays$steps,main = "Weekdays steps",xlab = "Intervals", col="blue",type="l")

dev.off()
```
