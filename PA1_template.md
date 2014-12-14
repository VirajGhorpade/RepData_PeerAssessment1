Reproducible Research - Peer Assessment 1
======================================




# Loading and Preprocessing the data

#### 1. Load the Data (and required packages)


```r
activity <- read.csv("activity.csv")
```


#### 2. Process/Transform the Data


```r
activity$date <- as.Date(activity$date)
```


# Total Number of Steps Taken per Day

#### 1. Make a histogram of the total number of steps taken each day


```r
totalSteps <- ggplot(aggregate(steps ~ date, data = activity, FUN = sum), aes(x = date, 
    y = steps))
totalSteps + geom_histogram(binwidth = 61, stat = "identity")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 


#### 2. Calculate and report the **mean** and **median** total number of steps taken per day


```r
mean(activity$steps, na.rm = TRUE)
```

```
## [1] 37.38
```

```r
median(activity$steps, na.rm = TRUE)
```

```
## [1] 0
```


# Average Daily Activity Pattern

#### 1. Time series plot of 5-minute interval and average number of steps taken


```r
ac <- activity[, c(1, 3)]
md <- melt(ac, id = "interval", measured = "steps")
ct <- cast(md, interval ~ variable, fun.aggregate = mean, na.rm = TRUE)
graph <- ggplot(ct, aes(interval, steps))
graph + geom_line()
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6.png) 


#### 2. 5-minute Interval with maximum number of steps


```r
ct[order(-ct$steps), ][1, "interval"]
```

```
## [1] 835
```



# Inputting Missing Values

#### 1. Total number of missing values


```r
length(activity$steps[is.na(activity$steps)])
```

```
## [1] 2304
```


#### 2. Strategy for filling in missing values

replace missing ("NA") values with the mean interval value of the dataset

#### 3. Re-create dataset with mising values filled in


```r
for (i in 1:length(activity$interval)) {
    if (is.na(activity$steps[i])) {
        activity$steps[i] <- ct$steps[ct$interval == activity$interval[i]]
    }
}
```


#### 4. Histogram of total number of steps taken each day and the **mean** and **median** of total number of steps


```r
totalSteps <- ggplot(aggregate(steps ~ date, data = activity, FUN = sum), aes(x = date, 
    y = steps))
totalSteps + geom_histogram(binwidth = 61, stat = "identity")
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10.png) 

```r

mean(activity$steps)
```

```
## [1] 37.38
```

```r

median(activity$steps)
```

```
## [1] 0
```


# Differences in Activity Between Weekend and Weekdays

#### 1. create new factor with two levels - "weekday" and "weekend"


```r
activity$date <- as.factor(ifelse(weekdays(activity$date) %in% c("Saturday", 
    "Sunday"), "weekend", "weekday"))
```


#### 2. Plot time series of 5-minute intervals for weekend vs weekday


```r
week <- ggplot(activity, aes(interval, steps)) + geom_line()
week + facet_grid(date ~ .)
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12.png) 

