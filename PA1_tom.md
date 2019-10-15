---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


### Loading and preprocessing the data

Show any code that is needed to

1. Load the data (i.e. `read.csv()`)


```r
trackerData <- read.csv("activity.csv")
summary(trackerData)
```

```
##      steps                date          interval     
##  Min.   :  0.00   2012-10-01:  288   Min.   :   0.0  
##  1st Qu.:  0.00   2012-10-02:  288   1st Qu.: 588.8  
##  Median :  0.00   2012-10-03:  288   Median :1177.5  
##  Mean   : 37.38   2012-10-04:  288   Mean   :1177.5  
##  3rd Qu.: 12.00   2012-10-05:  288   3rd Qu.:1766.2  
##  Max.   :806.00   2012-10-06:  288   Max.   :2355.0  
##  NA's   :2304     (Other)   :15840
```
2. Process/transform the data (if necessary) into a format suitable for your analysis


### What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in
the dataset.

1. Make a histogram of the total number of steps taken each day


```r
stepsByDay <- aggregate(steps~date, trackerData, sum, na.rm = TRUE)

hist(stepsByDay$steps, main= "Number of Days",xlab = "Count of steps", ylab="# of Days")
```

![](PA1_tom_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

2. Calculate and report the **mean** and **median** total number of steps taken per day

```r
avgSteps <- mean(stepsByDay$steps)
avgSteps
```

```
## [1] 10766.19
```

```r
medSteps <- median(stepsByDay$steps)
medSteps
```

```
## [1] 10765
```

## What is the average daily activity pattern?

1. Make a time series plot (i.e. `type = "l"`) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
avgPerInterval <- aggregate(steps~interval, data=trackerData, mean, na.rm=TRUE)
plot(steps~interval, data=avgPerInterval, type="l", xlab="Interval", ylab= "Mean Steps", main= "Mean Steps By Interval")
```

![](PA1_tom_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
mx <- max(trackerData$steps,na.rm = TRUE)
mx
```

```
## [1] 806
```

## Imputing missing values
Note that there are a number of days/intervals where there are missing
values (coded as `NA`). The presence of missing days may introduce
bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with `NA`s)


```r
sum(is.na(trackerData$steps))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.


```r
meanSteps <- mean(trackerData$steps, na.rm = TRUE)
meanSteps
```

```
## [1] 37.3826
```

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
#find location where NA exists
locWhereNa<- is.na(trackerData)

#put the data in a new object in case we have to go back

##trackerDataRm <-trackerData

#replace NA with the mean from previous step
trackerData[locWhereNa, 1]<-meanSteps
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the **mean** and **median** total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
avgSteps1 <- mean(trackerData$steps)
avgSteps1
```

```
## [1] 37.3826
```

```r
medSteps1 <- median(trackerData$steps)
medSteps1
```

```
## [1] 0
```

```r
stepsByDay <- aggregate(steps~date, trackerData, sum, na.rm = TRUE)

hist(stepsByDay$steps, main= "Number of Days",xlab = "Count of steps", ylab="# of Days")
```

![](PA1_tom_files/figure-html/unnamed-chunk-9-1.png)<!-- -->


## Are there differences in activity patterns between weekdays and weekends?
For this part the `weekdays()` function may be of some help here. Use
the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
library(dplyr)
```

```
## Warning: package 'dplyr' was built under R version 3.5.3
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
trackerData$date<- as.Date(trackerData$date)

trackerData1<-trackerData%>%
  mutate(dayType=ifelse(weekdays(trackerData$date)=="Saturday" | weekdays(trackerData$date)=="Sunday", "Weekend","Weekday"))
```

1. Make a panel plot containing a time series plot (i.e. `type = "l"`) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was created using **simulated data**:


```r
trackerDataWeekDay<-trackerData1%>%
  filter(dayType == "Weekday")

trackerDataWeekDayOut <- aggregate(steps~interval, data=trackerDataWeekDay, mean)
plot(steps~interval, data=trackerDataWeekDayOut, type="l", xlab="Daily Interval", ylab= "Average Number of Steps", main= "Weekday")
```

![](PA1_tom_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

```r
trackerDataWeekEnd<-trackerData1%>%
  filter(dayType == "Weekend")

trackerDataWeekEndOut <- aggregate(steps~interval, data=trackerDataWeekEnd, mean)
plot(steps~interval, data=trackerDataWeekEndOut, type="l", xlab="Daily Interval", ylab= "Average Number of Steps", main= "WeekEnd")
```

![](PA1_tom_files/figure-html/unnamed-chunk-11-2.png)<!-- -->
