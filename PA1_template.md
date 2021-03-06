Reproducible Research: Course Project 1
=======================================

# Loading and preprocessing the data

## 1. Load the data

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.


```r
# Data Loading
unzip("activity.csv")
```

```
## Warning in unzip("activity.csv"): error 1 in extracting from zip file
```

```r
activity <- read.csv("activity.csv")
```

## 2. Process/transform the data

There are a total of 17,568 observations in this dataset. The variables included are:
* steps: Number of steps taking in a 5-minute interval (missing values are coded as \color{red}{\verb|NA|}NA)
* date: The date on which the measurement was taken in YYYY-MM-DD format
* interval: Identifier for the 5-minute interval in which measurement was taken


```r
# Data Dimension (Number of observations, Number of variables)
dim(activity)
```

```
## [1] 17568     3
```

```r
# Data Head
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
# Data Summary
summary(activity)
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

# What is mean total number of steps taken per day?

## 1. Calculate the total number of steps taken per day


```r
# Total number of steps per day
totalperday <- aggregate(activity$steps, by=list(activity$date), sum)
names(totalperday)[1] ="date"
names(totalperday)[2] ="totalsteps"

# Total steps per day
head(totalperday)
```

```
##         date totalsteps
## 1 2012-10-01         NA
## 2 2012-10-02        126
## 3 2012-10-03      11352
## 4 2012-10-04      12116
## 5 2012-10-05      13294
## 6 2012-10-06      15420
```

## 2. Make a histogram of the total number of steps taken each day


```r
# Plot of total number of steps per day
library(ggplot2)
ggplot(totalperday, aes(x = totalsteps)) +
  geom_histogram(binwidth=1000) +
  labs(title = "Total steps per day", x = "Steps", y = "Date")
```

```
## Warning: Removed 8 rows containing non-finite values (stat_bin).
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png)

## 3. Calculate and report the mean and median of the total number of steps taken per day


```r
# Mean total number of steps per day
mean(totalperday$totalsteps,na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
# Median total number of steps per day
median(totalperday$totalsteps,na.rm=TRUE)
```

```
## [1] 10765
```

# What is the average daily activity pattern?

## 1. Make a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
# Average number of steps
activity_noNA <- activity[which(!is.na(activity$steps)),]
meanperminute <- aggregate(activity_noNA$steps, by=list(activity_noNA$interval), mean)
names(meanperminute)[1] ="interval"
names(meanperminute)[2] ="steps"

# Plot of average number of steps
ggplot(meanperminute, aes(x = interval, y=steps)) +
  labs(title = "Average number of steps", x = "interval", y = "steps")+
  geom_line() 
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png)

## 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
# Maximum average number of steps
meanperminute[which.max(meanperminute$steps),]
```

```
##     interval    steps
## 104      835 206.1698
```

# Imputing missing values

## 1. Calculate and report the total number of missing values in the dataset.


```r
# Number of missing values
sapply(X = activity, FUN = function(x) sum(is.na(x)))
```

```
##    steps     date interval 
##     2304        0        0
```

## 2. Devise a strategy for filling in all of the missing values in the dataset.


```r
library(dplyr)
impute <- function(num) replace(num, is.na(num), mean(num, na.rm = TRUE))
nulltomean <- (activity %>% group_by(interval) %>% mutate(steps = impute(steps)))
head(nulltomean)
```

```
## Error in library.dynam(lib, package, package.lib): DLL 'utf8' not found: maybe not installed for this architecture?
```

## 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
activity_new <- as.data.frame(nulltomean)
head(activity_new)
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

## 4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

The means have no difference since mean was used in imputing missing data. However, the medians are slightly different.


```r
# Total number of steps per day
totalperday_new <- aggregate(activity_new$steps, by=list(activity_new$date), sum)
names(totalperday_new)[1] ="date"
names(totalperday_new)[2] ="totalsteps"

# Total steps per day
head(totalperday_new)
```

```
##         date totalsteps
## 1 2012-10-01   10766.19
## 2 2012-10-02     126.00
## 3 2012-10-03   11352.00
## 4 2012-10-04   12116.00
## 5 2012-10-05   13294.00
## 6 2012-10-06   15420.00
```


```r
# Plot of total number of steps per day
library(ggplot2)
ggplot(totalperday_new, aes(x = totalsteps)) +
  geom_histogram(binwidth=1000) +
  labs(title = "Total steps per day", x = "Steps", y = "Date")
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png)


```r
# Mean total number of steps per day
mean(totalperday_new$totalsteps,na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
# Median total number of steps per day
median(totalperday_new$totalsteps,na.rm=TRUE)
```

```
## [1] 10766.19
```

# Are there differences in activity patterns between weekdays and weekends?

## 1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
activity_new$day <- ifelse(weekdays(as.Date(activity_new$date)) %in% c("Saturday", "Sunday"), "Weekend", "Weekday")
```

## 2. Make a panel plot containing a time series plot (i.e. \color{red}{\verb|type = "l"|}type="l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
meanbyweekendorweekday <- aggregate(activity_new$steps, by=list(activity_new$day, activity_new$interval), mean)
names(meanbyweekendorweekday)[1] ="day"
names(meanbyweekendorweekday)[2] ="interval"
names(meanbyweekendorweekday)[3] ="steps"

ggplot(meanbyweekendorweekday, aes(x = interval, y=steps, color=day)) +
  geom_line() +
  facet_grid(day ~ .) +
  labs(title = "Mean number of steps", x = "interval", y = "steps")
```

![plot of chunk unnamed-chunk-14](figure/unnamed-chunk-14-1.png)
