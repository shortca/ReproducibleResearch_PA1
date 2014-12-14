PA1_template
=========================================================================

Loading and preprocessing the data

Show any code that is needed to

    Load the data (i.e. read.csv())

    Process/transform the data (if necessary) into a format suitable for your analysis



```r
setwd("C:/datasci")
activity <- read.csv("activity.csv")
```

What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

    Make a histogram of the total number of steps taken each day

    Calculate and report the mean and median total number of steps taken per day



```r
complete_data <- na.omit(activity)
hist_data <- aggregate(steps ~ date, complete_data, sum)
hist(hist_data$steps, main="Frequency of Total Number of Steps per Day",xlab="Number of Steps")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png) 

```r
mean(hist_data$steps)
```

[1] 10766.19

```r
median(hist_data$steps)
```

[1] 10765

What is the average daily activity pattern?

    Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

   

```r
#Plot mean number of steps by 5-min interval over time

mean_steps <- aggregate(steps ~ interval, complete_data, mean)

plot(x=mean_steps$interval, y=mean_steps$steps, type="l",main="Average Number of Steps of 5-Minute Intervals",xlab="Interval",ylab="Number of Steps")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
max_steps <- mean_steps$interval[max(mean_steps$steps)]
```


Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

    Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
    

```r
length(which(is.na(activity$steps)))
```

```
## [1] 2304
```

    Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

    Create a new dataset that is equal to the original dataset but with the missing data filled in.


```r
for(i in 1:nrow(activity)){
        if(is.na(activity$steps[i])){
                interval <- activity$interval[i]
                steps_avg_row <- mean_steps[mean_steps$interval==interval,]
                steps_avg <- steps_avg_row$steps
                activity$steps[i] <- steps_avg
        }
}
```

Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 


```r
imputed_hist_data <- aggregate(steps ~ date, activity, sum)
hist(imputed_hist_data$steps, main="Frequency of Steps per Day (Imputed Data Version)",xlab="Number of Steps")
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png) 

```r
mean(imputed_hist_data$steps)
```

```
## [1] 10766.19
```

```r
median(imputed_hist_data$steps)
```

```
## [1] 10766.19
```

Do these values differ from the estimates from the first part of the assignment?
What is the impact of imputing missing data on the estimates of the total daily number of steps?


Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
days <- weekdays(as.Date(activity$date))
weekday <- c("Weekday")
data <- cbind(activity,days,weekday)
data$weekday <- as.character(data$weekday)

for(i in 1:nrow(data)){
        if(data$days[i]=="Saturday" || data$days[i] =="Sunday"){data$weekday[i] <- "Weekend"}
}
data$weekday <- as.factor(data$weekday)
```

Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
library(ggplot2)
qplot(interval, steps, data=data, geom="line") + facet_wrap(~weekday)
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png) 








