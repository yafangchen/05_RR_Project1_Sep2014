---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data
First, I will read the data into R


```r
data <- read.csv("./activity.csv")
```


## What is mean total number of steps taken per day?
Here is a histogram of the total number of steps taken each day:


```r
aggdata <- aggregate(steps~date,data,sum)
hist(aggdata$steps,xlab="Total Steps",main="Total steps taken each day (with missing data)")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 

The **mean** total number of steps taken per day is:


```r
mean(aggdata$steps)
```

```
## [1] 10766
```

The **median** total number of steps taken per day is:


```r
median(aggdata$steps)
```

```
## [1] 10765
```


## What is the average daily activity pattern?
To investigate the average daily activity pattern, a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis) is generated:


```r
aggdata2 <- aggregate(steps~interval,data,sum)
with(aggdata2,plot(interval,steps,type="l",main="Average number of steps taken"))
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5.png) 

The 5-minute interval that contains the maximum number of steps is:


```r
aggdata2[(aggdata2$steps==max(aggdata2$steps)),]
```

```
##     interval steps
## 104      835 10927
```



## Imputing missing values
Note that there are a number of days/intervals that have missing values (coded as NA). The total number of missing values in the dataset is:


```r
missing <- data[(data$steps=="NA"),]
nrow(missing)
```

```
## [1] 2304
```

To fill in the missing values, **the mean for that 5-minute interval** is used. And a new dataset (**mergedData**) with the missing data filled in is created:


```r
aggdata_mean <- aggregate(steps~interval,data,mean)
mergedData = merge(data,aggdata_mean,by="interval",all=TRUE)
mergedData[(is.na(mergedData$steps.x)),2] <- mergedData[(is.na(mergedData$steps.x)),4]
```

Here is a histogram of the total number of steps taken each day:


```r
aggmergedData <- aggregate(steps.x~date,mergedData,sum)
hist(aggdata$steps,xlab="Total Steps",main="Total steps taken each day (no missing data)")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9.png) 

The **mean** total number of steps taken per day is:


```r
mean(aggmergedData$steps.x)
```

```
## [1] 10766
```

The **median** total number of steps taken per day is:


```r
median(aggmergedData$steps.x)
```

```
## [1] 10766
```

Note that these values differ from those calcuated with the original dataset that has missing values. With the missing values filled in, the **mean** total number of steps taken per day is the same as before, but the **median** total number of steps taken per day is different.


## Are there differences in activity patterns between weekdays and weekends?
First, I create a new factor variable (**day**) in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend.


```r
mergedData$date <- as.Date(mergedData$date)
mergedData$day <- ifelse((weekdays(mergedData[,c("date")],abbreviate=TRUE) %in% c("Mon","Tue","Wed","Thu","Fri")),"Weekday","Weekend")
```

Then a panel plot is generated, which contains a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis):


```r
aggmergedData_day <- aggregate(steps.x~interval+day,mergedData,sum)
library(ggplot2)
g <- ggplot(aggmergedData_day,aes(interval,steps.x))
g + geom_line() + facet_grid(day~.) + labs(y="Number of steps")
```

![plot of chunk unnamed-chunk-13](figure/unnamed-chunk-13.png) 