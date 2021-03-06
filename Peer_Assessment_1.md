---
title: "Peer_Asssessment"
output: html_document
---

```{r,}

# Load Libraries

library(knitr)
library(dplyr)
library(ggplot2)
library(knitr)
library(lattice)

```

```r
# Reading the File

activity <- read.csv("D:/RCoursera/Reproducible Research/Week1/Data/activity.csv", colClass=c('integer', 'Date', 'integer'))

# What is mean total number of steps taken per day?

steps.date <- aggregate(steps ~ date, activity, sum)

head(steps.date)

barplot(steps.date$steps, names.arg=steps.date$date, ylim=c(0, 25000), xlab="date",ylab="sum(steps)",col = 'Red')

# Mean 
mean(steps.date$steps)

# Median
median(steps.date$steps)
```

```r
# What is the average daily activity pattern?

steps.interval <- aggregate(steps ~ interval, activity, mean)
plot(steps.interval, type='l', col = 'blue')

# Max Value
Max.value <- steps.interval$interval[which.max(steps.interval$steps)]

```
```r
# Imputing missing values

sum(is.na(activity$steps))

activity.clean <- merge(activity, steps.date, by="date", suffixes=c("", ".mean"))

nas <- is.na(activity.clean$steps)
activity.clean$steps[nas] <- activity.clean$steps.mean[nas]
activity.clean <- activity.clean[, c(1:3)]
head(activity.clean)

steps.date <- aggregate(steps ~ date, activity.clean, sum)

barplot(steps.date$steps, names.arg=steps.date$date, ylim=c(0, 25000), xlab="date", ylab="sum(steps)",col = 'blue')

# Mean 
mean(steps.date$steps)

# Median
median(steps.date$steps)
```

```r
# Are there differences in activity patterns between weekdays and weekends?


day <- function(dates) {
  f <- function(date) {
    if (weekdays(date) %in% c("Saturday", "Sunday")) {
      "weekend"
    }
    else {
      "weekday"
    }
  }
  sapply(dates, f)
}

activity$day <- as.factor(day(activity$date))
str(activity)

steps.interval <- aggregate(steps ~ interval + day, activity, mean)
xyplot(steps ~ interval | day, data=steps.interval, layout=c(1,2), type='l')

```