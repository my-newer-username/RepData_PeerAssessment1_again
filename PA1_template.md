# PA1_template.Rmd
Cong  
August 3, 2016  



## Loading and preprocessing the data

Show any code that is needed to

1. Load the data (i.e. read.csv())
2. Process/transform the data (if necessary) into a format suitable for your analysis


```r
FileName <- "activity.csv"
if(!file.exists(FileName))
{
    FileUrl <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
    download.file(FileUrl,"activity.zip")
    unzip("activity.zip")
}
data <- read.csv(FileName)
```

During the above code's running, file will be downloaded, and unzip. File will be read into the variable named data.

## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

1. Calculate the total number of steps taken per day


```r
TotalStepsPerDay <- tapply(data$steps, data$date, FUN=sum, na.rm=TRUE)
```
The total number of steps taken per day is stored in the variable TotalStepsPerDay


2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day


```r
hist(TotalStepsPerDay,25)

library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 3.2.5
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

```r
qplot(TotalStepsPerDay,geom="histogram")
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

![](PA1_template_files/figure-html/unnamed-chunk-3-2.png)<!-- -->

The figure is shown above.

3. Calculate and report the mean and median of the total number of steps taken per day


```r
mean(TotalStepsPerDay, na.rm=TRUE)
```

```
## [1] 9354.23
```

```r
median(TotalStepsPerDay, na.rm=TRUE)
```

```
## [1] 10395
```

## What is the average daily activity pattern?
1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)






```r
ActivityPattern <- tapply(data$steps, data$interval,mean,na.rm=T)


#qplot(names(ActivityPattern),ActivityPattern,type="l",xlab="5-minute interval",ylab="average steps across different days",main="The mean number of steps per 5 minute interval")


ActivityPattern1 = data.frame(Interval=seq_along(ActivityPattern), Mean_Steps=ActivityPattern)
ggplot(ActivityPattern1,aes(Interval,Mean_Steps)) + geom_line()+
        labs(x="5-minute interval",y="average steps across different days",
             title = "The mean number of steps per 5 minute interval")
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->


2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
names(ActivityPattern)[which.max(ActivityPattern)]
```

```
## [1] "835"
```
The above line reports the 5-minute inteval which contains the max number of steps.

## Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
Total_Missing_number <- sum(!complete.cases(data))
Total_Missing_number
```

```
## [1] 2304
```

The total number of missing values in the dataset is 'r Total_Missing_number'

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

```r
FillNa <- mean(data$steps,na.rm=T)
```

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
new_data <- data
new_data$steps[is.na(new_data$steps)] <- FillNa
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
NewTotalStepsPerDay <- tapply(new_data$steps, new_data$date, FUN=sum, na.rm=TRUE)
hist(NewTotalStepsPerDay,25)
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

```r
mean(NewTotalStepsPerDay, na.rm=TRUE)
```

```
## [1] 10766.19
```

```r
median(NewTotalStepsPerDay, na.rm=TRUE)
```

```
## [1] 10766.19
```

## Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.
1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
new_data_weekdays <- weekdays(as.Date(as.character(new_data$date)))
weekdayslist <- c('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday')
new_data$weekday_weekend <- factor( ifelse(new_data_weekdays %in% weekdayslist, "weekday", "weekend") )
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
library(ggplot2)
Steps_interval_WeekDayEnd <- aggregate(steps ~ interval + weekday_weekend, data = new_data, mean)
ggplot(Steps_interval_WeekDayEnd, aes(interval, steps)) + geom_line() + facet_grid(weekday_weekend ~ .) + 
    xlab("5-minute interval") + ylab("Number of steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

