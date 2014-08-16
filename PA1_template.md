
```
## Warning: package 'lattice' was built under R version 3.0.3
```


# Reproducible Research: Peer Assessment 1


## Introduction
This file is the R Mark down file for Reproducible Research peer assessment 1. Probelm details regarding this assessment is available in [Read Me]("https://github.com/vinaykk/RepData_PeerAssessment1/blob/master/README.md") 

Following are the sections related to problems asked in this assessment. 

## Loading and preprocessing the data

Load the activity data

```r
DFActivity <- read.csv("activity.csv",colClasses=c("numeric","Date","numeric"))
```
Process/transform data

```r
DFTotalStepsPerDay <- aggregate(steps ~ date, data = DFActivity , FUN="sum")
DFAvgStepsPerInt <- aggregate(steps ~ interval, data = DFActivity , FUN="mean")
```


## What is mean total number of steps taken per day?

Generate histogram of the total number of steps taken each day

```r
hist(DFTotalStepsPerDay$steps,col="Red",freq=TRUE,main="Total number of steps taken each day",xlab="Total Steps")
```

![plot of chunk fig1 plot histogram for mean total number of steps taken per day](figure/fig1 plot histogram for mean total number of steps taken per day.png) 

Calculate and report the mean and median total number of steps taken per day

```r
stepsMean <- mean(DFTotalStepsPerDay$steps)

stepsMedian <- median(DFTotalStepsPerDay$steps)
```

The Mean total number of steps taken per day is 10766    
The Median total number of steps taken per day is 10765

## What is the average daily activity pattern?

Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
plot(DFAvgStepsPerInt$interval,DFAvgStepsPerInt$steps,type="l" )
```

![plot of chunk fig2 plot time series for average daily activity pattern](figure/fig2 plot time series for average daily activity pattern.png) 

Calculate 5- Minute interval that has max avergate across all day. 

```r
max5MinInt <- DFAvgStepsPerInt[ (DFAvgStepsPerInt$steps  == max(DFAvgStepsPerInt$steps)),]$interval
```

830 - 835 is the 5-minute interval, on average across all the days in the dataset, containg the maximum number of steps.

## Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
ncnt <- summary(is.na(DFActivity$steps))[3][1]
```
Total number of missing values in the dataset is 2304

Following is the strategy used to replace missing values. 
1. Make copy of data set
2. Calculate mean steps for each  5-minute interval 
3. Replace NA's with calculated mean


```r
DFActivityImput <- DFActivity
DFActivityImput$step[(is.na(DFActivityImput$steps))] <- round(ave(DFActivityImput$steps, DFActivityImput$interval, FUN = function(x) mean(x, na.rm=T) )[(is.na(DFActivityImput$steps))])
```

Make a histogram of the total number of steps taken each day

```r
DFSumStepsPerDayImput <- aggregate(steps ~ date, data = DFActivityImput , FUN="sum")

hist(DFSumStepsPerDayImput$steps,col="Red",freq=TRUE,main="Total number of steps taken each day",xlab="Total Steps")
```

![plot of chunk fig3 plot histogram mean total number of steps taken per day using imputed data](figure/fig3 plot histogram mean total number of steps taken per day using imputed data.png) 

Calculate and report the mean and median total number of steps taken per day

```r
stepsMean <- mean(DFSumStepsPerDayImput$steps)

stepsMedian <- median(DFSumStepsPerDayImput$steps)
```

The Mean total number of steps taken per day is 10766
The Median total number of steps taken per day is 10765

Mean and Median total number of steps taken does not differ after applying strategy to replace missing steps count with mean steps of 5-minute interval.

Estimates of the total daily number of steps has no impact after applying stategy to replace missing steps count with mean steps of 5-minute interval. 


## Are there differences in activity patterns between weekdays and weekends?

Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
DFActivity$DayType <- as.factor( ifelse( (weekdays(DFActivity$date) == "Sunday" | weekdays(DFActivity$date) == "Saturday"),"weekday","weekend" ))

DFAvgStepsPerInt <- aggregate(steps ~ interval + DayType, data = DFActivity , FUN="mean")
```


```r
xyplot(steps ~ interval | DayType, data = DFAvgStepsPerInt,type="l", layout = c(1,2), ylab="Number of Steps", xlab="Interval" )
```

![plot of chunk fig4 plot pannels for differences in activity patterns between weekdays and weekends](figure/fig4 plot pannels for differences in activity patterns between weekdays and weekends.png) 

