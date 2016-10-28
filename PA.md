---
title: "Reproducible Research Project 1"
author: "Magnolia"
date: "27 October 2016"
output: html_document
---



##Loading the necessary packages

```r
library(knitr)
library(dplyr)
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
library(lubridate)
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following object is masked from 'package:base':
## 
##     date
```

```r
library(ggplot2)
```


##Loading and preprocessing the data

###Reading the data


```r
data <- read.csv("activity.csv", colClass=c('integer', 'Date', 'integer'))
```

```
## Warning in file(file, "rt"): cannot open file 'activity.csv': No such file
## or directory
```

```
## Error in file(file, "rt"): cannot open the connection
```

###Tyding the data

Changing the date into dateformat using lubridate:

```r
data$date <- ymd(data$date)
```

```
## Error in data$date: object of type 'closure' is not subsettable
```

##What is mean total number of steps taken per day?

For this part of the assignment the missing values can be ignored.
1. Calculate the total number of steps taken per day.
2. Make a histogram of the total number of steps taken each day.
3. Calculate and report the mean and median of the total number of steps taken per day.


###Methodology and Result

1.Calculate the total number of steps per day using dplyr and group by date:

```r
steps <- data %>%
  filter(!is.na(steps)) %>%
  group_by(date) %>%
  summarize(steps = sum(steps)) %>%
  print
```

```
## Error in UseMethod("filter_"): no applicable method for 'filter_' applied to an object of class "function"
```

2.Use ggplot for making the histogram:

```r
ggplot(steps, aes(x = steps)) +
  geom_histogram(fill = "purple", binwidth = 1000) +
  labs(title = "Histogram of Steps per day", x = "Steps per day", y = "Frequency")
```

```
## Error in ggplot(steps, aes(x = steps)): object 'steps' not found
```

3.Calculate the mean and median of the total number of steps taken per day:

```r
mean_steps <- mean(steps$steps, na.rm = TRUE)
```

```
## Error in mean(steps$steps, na.rm = TRUE): object 'steps' not found
```

```r
median_steps <- median(steps$steps, na.rm = TRUE)
```

```
## Error in median(steps$steps, na.rm = TRUE): object 'steps' not found
```

```r
mean_steps
```

```
## Error in eval(expr, envir, enclos): object 'mean_steps' not found
```

```r
median_steps
```

```
## Error in eval(expr, envir, enclos): object 'median_steps' not found
```

Mean steps are 10766 and median steps are 10765.


##What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis).
2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?.

###Methodology and Result

1. Calculate the average number of steps taken in each 5-minute interval per day using dplyr and group by interval:


```r
interval <- data %>%
  filter(!is.na(steps)) %>%
  group_by(interval) %>%
  summarize(steps = mean(steps))
```

```
## Error in UseMethod("filter_"): no applicable method for 'filter_' applied to an object of class "function"
```

Use ggplot for making the time series of the 5-minute interval and average steps taken:


```r
ggplot(interval, aes(x=interval, y=steps)) +
  geom_line(color = "purple")
```

```
## Error in if (is.waive(data) || empty(data)) return(cbind(data, PANEL = integer(0))): missing value where TRUE/FALSE needed
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png)

2. Use which.max() to find out the maximum steps, on average, across all the days:


```r
interval[which.max(interval$steps),]
```

```
## Error in interval$steps: object of type 'closure' is not subsettable
```

The interval 835 has, on average, the highest count of steps, with 206 steps.


##Imputing missing values

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs).
2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
3. Create a new dataset that is equal to the original dataset but with the missing data filled in.
4. Make a histogram of the total number of steps taken each day and calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

###Methodology and Result

Summarize all the missing values:


```r
sum(is.na(data$steps))
```

```
## Error in data$steps: object of type 'closure' is not subsettable
```

Missing values are 2304.

1. Taking the approach to fill in a missing NA with the average number of steps in the same 5-min interval.
2. Creating a new dataset as the original and fill in the missing values with the average number of steps per 5-minute interval:


```r
data_full <- data
nas <- is.na(data_full$steps)
```

```
## Error in data_full$steps: object of type 'closure' is not subsettable
```

```r
avg_interval <- tapply(data_full$steps, data_full$interval, mean, na.rm=TRUE, simplify=TRUE)
```

```
## Error in data_full$interval: object of type 'closure' is not subsettable
```

```r
data_full$steps[nas] <- avg_interval[as.character(data_full$interval[nas])]
```

```
## Error in eval(expr, envir, enclos): object 'avg_interval' not found
```

Check that there are no missing values:


```r
sum(is.na(data_full$steps))
```

```
## Error in data_full$steps: object of type 'closure' is not subsettable
```

No more missing values.

4. Calculating the number of steps taken in each 5-minute interval per day using dplyr and group by interval. Using ggplot for making the histogram:


```r
steps_full <- data_full %>%
  filter(!is.na(steps)) %>%
  group_by(date) %>%
  summarize(steps = sum(steps)) %>%
  print
```

```
## Error in UseMethod("filter_"): no applicable method for 'filter_' applied to an object of class "function"
```


```r
ggplot(steps_full, aes(x = steps)) +
  geom_histogram(fill = "purple", binwidth = 1000) +
  labs(title = "Histogram of Steps per day, including missing values", x = "Steps per day", y = "Frequency")
```

```
## Error in ggplot(steps_full, aes(x = steps)): object 'steps_full' not found
```


Calculating the mean and median steps with the filled in values:


```r
mean_steps_full <- mean(steps_full$steps, na.rm = TRUE)
```

```
## Error in mean(steps_full$steps, na.rm = TRUE): object 'steps_full' not found
```

```r
median_steps_full <- median(steps_full$steps, na.rm = TRUE)
```

```
## Error in median(steps_full$steps, na.rm = TRUE): object 'steps_full' not found
```


```r
mean_steps_full
```

```
## Error in eval(expr, envir, enclos): object 'mean_steps_full' not found
```


```r
median_steps_full
```

```
## Error in eval(expr, envir, enclos): object 'median_steps_full' not found
```

The impact of imputing missing data with the average number of steps in the same 5-min interval is that both the mean and the median are equal to the same value: 10766.

##Are there differences in activity patterns between weekdays and weekends?

1. Creating a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.
2. Making a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

###Methodology and Result

Using dplyr and mutate to create a new column, weektype, and apply whether the day is weekend or weekday:


```r
data_full <- mutate(data_full, weektype = ifelse(weekdays(data_full$date) == "Saturday" | weekdays(data_full$date) == "Sunday", "weekend", "weekday"))
```

```
## Error in UseMethod("mutate_"): no applicable method for 'mutate_' applied to an object of class "function"
```

```r
data_full$weektype <- as.factor(data_full$weektype)
```

```
## Error in data_full$weektype: object of type 'closure' is not subsettable
```

```r
head(data_full)
```

```
##                                                                      
## 1 function (..., list = character(), package = NULL, lib.loc = NULL, 
## 2     verbose = getOption("verbose"), envir = .GlobalEnv)            
## 3 {                                                                  
## 4     fileExt <- function(x) {                                       
## 5         db <- grepl("\\\\.[^.]+\\\\.(gz|bz2|xz)$", x)              
## 6         ans <- sub(".*\\\\.", "", x)
```

2. Calculate the average steps in the 5-minute interval and use ggplot for making the time series of the 5-minute interval for weekday and weekend, and compare the average steps:


```r
interval_full <- data_full %>%
  group_by(interval, weektype) %>%
  summarise(steps = mean(steps))
```

```
## Error in UseMethod("group_by_"): no applicable method for 'group_by_' applied to an object of class "function"
```

```r
s <- ggplot(interval_full, aes(x=interval, y=steps, color = weektype)) +
  geom_line() +
  facet_wrap(~weektype, ncol = 1, nrow=2)
```

```
## Error in ggplot(interval_full, aes(x = interval, y = steps, color = weektype)): object 'interval_full' not found
```

```r
print(s)
```

```
## Error in print(s): object 's' not found
```

From the two plots it seems that the test object is more active earlier in the day during weekdays compared to weekends, but more active throughout the weekends compared with weekdays (probably because the oject is working during the weekdays, hence moving less during the day).
