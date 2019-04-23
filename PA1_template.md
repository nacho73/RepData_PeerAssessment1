# Reproducible Research: Peer Assessment 1



## Loading and preprocessing the data
##### 1. Load the data (i.e. read.csv())

```r
if(!file.exists('activity.csv')){
    unzip('activity.zip')
}
activityData <- read.csv('activity.csv')
```
##### 2. Process/transform the data (if necessary) into a format suitable for your analysis

```r
#activityData$interval <- strptime(gsub("([0-9]{1,2})([0-9]{2})", "\\1:\\2", activityData$interval), format='%H:%M')
```

-----

## What is mean total number of steps taken per day?

```r
steps_day <- tapply(activityData$steps, activityData$date, sum, na.rm=TRUE)
```

##### 1. Make a histogram of the total number of steps taken each day

```r
qplot(steps_day, xlab='Total steps per day', ylab='Frequency using binwith 500', binwidth=500)
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5.png) 

##### 2. Calculate and report the mean and median total number of steps taken per day

```r
steps_dayMean <- mean(stepsByDay)
steps_dayMedian <- median(stepsByDay)
```
* Mean: 9354.2295
* Median:  10395

-----

## What is the average daily activity pattern?

```r
averageStepsPerTimeBlock <- aggregate(x=list(meanSteps=activityData$steps), by=list(interval=activityData$interval), FUN=mean, na.rm=TRUE)
```

##### 1. Make a time series plot

```r
xyplot(interval~meanSteps,data=average_steps_per_time_block,main="Time Series Plot",xlab="5-minute interval", ylab="average number of steps taken")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8.png) 

##### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
mostSteps <- which.max(averageStepsPerTimeBlock$meanSteps)
timeMostSteps <-  gsub("([0-9]{1,2})([0-9]{2})", "\\1:\\2", averageStepsPerTimeBlock[mostSteps,'interval'])
```

* Most Steps at: 8:35

----

## Imputing missing values
##### 1. Calculate and report the total number of missing values in the dataset 

```r
numMissingValues <- length(which(is.na(activityData$steps)))
```

* Number of missing values: 2304

##### 2. Devise a strategy for filling in all of the missing values in the dataset.
##### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
activity_dataImputed <- activity_data
activity_dataImputed$steps <- impute(activity_data$steps, fun=mean)
```


##### 4. Make a histogram of the total number of steps taken each day 

```r
steps_dayImputed <- tapply(activity_dataImputed$steps, activity_dataImputed$date, sum)
qplot(steps_dayImputed, xlab='Total steps per day (Imputed)', ylab='Frequency using binwith 500', binwidth=500)
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12.png) 

##### ... and Calculate and report the mean and median total number of steps taken per day. 

```r
steps_dayMeanImputed <- mean(steps_dayImputed)
steps_dayMedianImputed <- median(steps_dayImputed)
```
* Mean (Imputed): 1.0766 &times; 10<sup>4</sup>
* Median (Imputed):  1.0766 &times; 10<sup>4</sup>


----

## Are there differences in activity patterns between weekdays and weekends?
##### 1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.


```r
activityDataImputed$dateType <-  ifelse(as.POSIXlt(activityDataImputed$date)$wday %in% c(0,6), 'weekend', 'weekday')
```

##### 2. Make a panel plot containing a time series plot


```r
averagedactivity_dataImputed <- aggregate(steps ~ interval + dateType, data=activity_dataImputed, mean)
xyplot((steps ~ interval| dateType), 
           data = averagedactivity_dataImputed,
           type = "l",
           xlab = "5-minutes interval",
           ylab = "Average number of steps",
           layout=c(1,2)) 
```

![plot of chunk unnamed-chunk-15](figure/unnamed-chunk-15.png) 

