---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---

## Loading and preprocessing the data

1. Load the data (i.e. read.csv())


```r
unzip(zipfile = "./activity.zip")
dt <- read.csv("activity.csv")
```

2. Process/transform the data (if necessary) into a format suitable for your analysis


```r
dt_conv <- transform(dt, date = as.Date(date, format = "%Y-%m-%d"))
```

## What is mean total number of steps taken per day?

1. Calculate the total number of steps taken per day


```r
dt_agg_day <- aggregate(steps ~ date, FUN = sum, data = dt_conv)
vt_agg_day <- dt_agg_day$steps
names(vt_agg_day) <- dt_agg_day$date
```

2. Make a histogram of the total number of steps taken each day


```r
hist(dt_agg_day$steps, xlab = "number of steps", main = "Total number of steps taken each day",col = "blue")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

3. Calculate and report the mean and median of the total number of steps taken per day


```r
v_mean <- (mean(dt_agg_day$steps))
v_median <- factor(median(dt_agg_day$steps))
print(v_mean)
```

```
## [1] 10766.19
```

```r
print(v_median)
```

```
## [1] 10765
## Levels: 10765
```

##What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
dt_agg_int <- aggregate(steps ~ interval, FUN = mean, data = dt_conv)
plot(x = dt_agg_int$interval,
     y = dt_agg_int$steps,
     type = "l",
     main = "Average number of steps taken across all days",
     xlab = "Interval",
     ylab = "Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png)<!-- -->
 
2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
dt_agg_order <- dt_agg_int[order(-dt_agg_int$steps),]
print(dt_agg_order[1, ])
```

```
##     interval    steps
## 104      835 206.1698
```

##Imputing missing values

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
v_na_sum <- sum(is.na(dt_conv$steps))
print(v_na_sum)
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.


```r
dt_agg_int_mean <- aggregate(steps ~ interval, FUN = mean, data = dt_conv)
```

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
dt_conv_merge <- merge(x = dt_conv, y = dt_agg_int_mean, by = "interval")
dt_conv_merge$steps <- ifelse(is.na(dt_conv_merge$steps.x), dt_conv_merge$steps.y, dt_conv_merge$steps.x) 
dt_conv_na <- dt_conv_merge[c("steps", "date", "interval")]
```

4. Make a histogram of the total number of steps taken each day-


```r
dt_agg_day_na <- aggregate(steps ~ date, FUN = sum, data = dt_conv_na)
vt_agg_day_na <- dt_agg_day_na$steps
names(vt_agg_day_na) <- dt_agg_day_na$date 
hist(dt_agg_day_na$steps, xlab = "number of steps", main = "Total number of steps taken each day", col = "blue")
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

Calculate and report the mean and median total number of steps taken per day-


```r
v_mean_na <- (mean(dt_agg_day_na$steps))
v_median_na <- factor(median(dt_agg_day_na$steps))
print(v_mean_na)
```

```
## [1] 10766.19
```

```r
print(v_median_na)
```

```
## [1] 10766.1886792453
## Levels: 10766.1886792453
```

Do these values differ from the estimates from the first part of the assignment?

Yes.

What is the impact of imputing missing data on the estimates of the total daily number of steps?

By replacing the missing value with the average of the interval, the median is shifted closer to the mean.

##Are there differences in activity patterns between weekdays and weekends?

1. Create a new factor variable in the dataset with two levels ??? "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
dt_conv_na$weekdays <- as.factor(ifelse(weekdays(dt_conv_na$date) %in% c("Saturday", "Sunday"), "weekend", "weekday"))
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
library(lattice)
dt_agg_weekdays <- aggregate(steps ~ weekdays + interval, FUN = mean, data = dt_conv_na)
xyplot(steps ~ interval | weekdays, dt_agg_weekdays,
       type = "l",
       xlab = "Interval",
       ylab = "Number of steps",
       main = "Average number of steps taken, averaged across all weekday days or weekend days",
       layout = c(1, 2))
```

![](PA1_template_files/figure-html/unnamed-chunk-14-1.png)<!-- -->
