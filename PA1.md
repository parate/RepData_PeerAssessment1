# Reproducible Research - Programming Assignment 1
Florian Hochstrasser  
3 July 2016  





## Loading libraries
Some commonly used libaries are loaded first. Also, set some defaults for ggplot.


```r
library(dplyr)
library(lubridate)
library(ggplot2)
theme_set(theme_bw())
```


## Loading and preprocessing the data

Reading in activity data directly from zip file:

```r
activityData <- read.csv(unzip("activity.zip", "activity.csv"), na.strings = NA)
```

Transform the date field from factor to POSIX date, using dplyr and lubridate. Transforming to an actual date format will allow for easier analysis later on.

```r
activityData <- activityData %>% mutate(date = as_date(date))
```

## What is mean total number of steps taken per day?
To find out, dplyr comes in handy. Group by date and summarize will do to get the daily sums.

```r
ad_SumStepsPerDay <- activityData %>%
    group_by(date) %>%
    summarise(steps = sum(steps))
```

A histogram shows the distribution of daily steps. The warning message tells us that for 8 days, no data was recorded.


```r
ggplot(ad_SumStepsPerDay, aes(steps)) +
    geom_histogram(bins = 8)
```

```
## Warning: Removed 8 rows containing non-finite values (stat_bin).
```

![](PA1_files/figure-html/unnamed-chunk-5-1.png)

10000 to 12000 steps were taken on the majority of days, as it appears.

Let's take a closer look and compute mean and median daily steps. This can conveniently be done using the function summary(). However, it does floor/ceil so we don't get the exact numbers. Using the respective functions yields a more precise result. NA's have to be removed to get a result.


```r
summary(ad_SumStepsPerDay$steps)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
##      41    8841   10760   10770   13290   21190       8
```

```r
mean(ad_SumStepsPerDay$steps, na.rm = TRUE)
```

```
## [1] 10766.19
```

```r
median(ad_SumStepsPerDay$steps, na.rm = TRUE)
```

```
## [1] 10765
```

Mean and median lie very close together.

## What is the average daily activity pattern?
Since the data is divided into intervals that repeat every day, we can compute the mean steps grouped by interval to deduce the average activity pattern. NA's have to be explicitly omitted here.


```r
ad_meanStepsPerInterval <- activityData %>%
    na.omit %>%
    group_by(interval) %>%
    summarise(steps = mean(steps))

ggplot(ad_meanStepsPerInterval, aes(interval, steps)) +
    geom_line()
```

![](PA1_files/figure-html/unnamed-chunk-7-1.png)

What was the max?

```r
max(ad_meanStepsPerInterval$steps)
```

```
## [1] 206.1698
```

## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?
