# Reproducible Research: Peer Assessment 1

Load the data

```r
        activity <- read.csv("activity.csv")   
```
What is mean total number of steps taken per day?

1. Make a histogram of total steps by day
        

```r
               stepsbyday <- aggregate(steps ~ date, data = activity, sum)
                hist(stepsbyday$steps) 
```

![](figure/unnamed-chunk-2-1.png) 

2. Calculate and report the mean and median total steps per day


```r
                meanstepsperday <- round(mean(stepsbyday$steps),3)
```

*The mean total steps per day is  *


```
## [1] 10766.19
```


```r
medianstepsperday <- median(stepsbyday$steps)
```

The median total steps per day is


```
## [1] 10765
```

What is the average daily activity pattern?
               
1. Time series plot of 5 minute interval (x-axis) and average steps taken across all days (y-axis)


```r
                meanstepsbyinterval <- aggregate(steps ~ interval, data = activity, mean) 
                plot(meanstepsbyinterval, type = "l", xaxt = "n", xlab = "Intervals", ylab = "Steps", main = 
                            "Average Steps Taken by 5 Minute Interval")
                axis(1, at = meanstepsbyinterval$interval, labels = meanstepsbyinterval$interval)
```

![](figure/unnamed-chunk-7-1.png) 

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
                maxsteps <- which.max(meanstepsbyinterval$steps)
                maxinterval <- meanstepsbyinterval[maxsteps,]
```

The interval which contains the maximum number of steps is 


```
## [1] 835
```

##Imputing missing values

1. Calculate and report the total number of missing values in the dataset 


```r
                missing <- sum(is.na(activity))
```

The number of missing values in the dataset is


```
## [1] 2304
```
       
2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not               need to be sophisticated. For example, you could use the mean/median for that day, or the mean for                         that 5-minute interval, etc.
        
*NAs will be replaced by the mean for the 5 minute interval*
        
3. Create a new dataset that is equal to the original dataset but with the missing data filled in.



```r
        activity2 <- activity
        activity2$meanbyinterval <- meanstepsbyinterval$steps
        activity2$steps[which(is.na(activity$steps))] <- activity2$meanbyinterval
        activity2$meanbyinterval <- NULL
        head(activity)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```

```r
        head(activity2)
```

```
##       steps       date interval
## 1 1.7169811 2012-10-01        0
## 2 0.3396226 2012-10-01        5
## 3 0.1320755 2012-10-01       10
## 4 0.1509434 2012-10-01       15
## 5 0.0754717 2012-10-01       20
## 6 2.0943396 2012-10-01       25
```


4. Make a histogram of the total number of steps taken each day 
        
*The first histogram below is for comparison with the second one which imputes values for the NAs.*


```r
                stepsbyday2 <- aggregate(steps ~ date, data = activity2, sum)
                par(mfrow=c(1,2))
                hist(stepsbyday$steps, ylim = c(0,40))
                hist(stepsbyday2$steps, ylim = c(0,40))
```

![](figure/unnamed-chunk-15-1.png) 

Calculate and report the mean and median total steps per day


```r
                meanstepsperday2 <- mean(stepsbyday2$steps)
```

The mean steps per day with imputed values for NAs is


```
## [1] 10766.19
```


```r
                medianstepsperday2 <- median(stepsbyday2$steps)
```

The median steps per day with imputed values for NAs is


```
## [1] 10766.19
```

**Do these values differ from the estimates from the first part of the assignment?**
        
*The mean is exactly the same - this seems surprising but technically possible I guess.
The median steps per day is the same as the mean, slightly higher than before imputing
the missing data. This is probably due to many of the NAs occurring in intervals with 
a small mean.*
                
**What is the impact of imputing missing data on the estimates of the total daily number of steps?**
        
*It shifts the estimates slightly higher as can be seen by looking at the two histograms, but the center                 
is at the same place.*
        
Are there differences in activity patterns between weekdays and weekends?

Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating         
whether a given date is a weekday or weekend day.


```r
        dates <- as.POSIXlt(activity2$date) 
        activity2$daynames <- weekdays(dates)
        activity2$daynames[activity2$daynames != "Saturday" & activity2$daynames != "Sunday"] <- "weekday"
        activity2$daynames[activity2$daynames == "Saturday" | activity2$daynames == "Sunday"] <- "weekend"
        activity2$daynames <- factor(activity2$daynames, levels = c("weekday", "weekend"))
```

Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) 
and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). 


```r
                meanstepsbyinterval2 <- aggregate(steps ~ interval, data = activity2, mean) 
                par(mfrow=c(2,1))
                plot(steps ~ interval, data = activity2, subset = daynames == "weekday", type = "l", main =               
                "Weekdays")
                axis(1, at = meanstepsbyinterval$interval, labels = meanstepsbyinterval$interval)
                
                plot(steps ~ interval, data = activity2, subset = daynames == "weekend", type = "l", main = 
                "Weekends")
                axis(1, at = meanstepsbyinterval$interval, labels = meanstepsbyinterval$interval)
```

![](figure/unnamed-chunk-21-1.png) 

*As messy as these plots are due to running out of time, you can still tel that: there are more steps 
taken on weekdays, and that the number of steps taken is higher later in the day on weekends than on                 
weekdays.*
                
               
        
