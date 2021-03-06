---
title: "Reproducible Research Project 1"
author: "Galina Chtcherbakova"
date: "August 20, 2017"
output: html_document 
---

Reproducible Research Project 1
===============================

It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These type of devices are part of the "quantified self" movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

Loading and preprocessing the data
====================================

Downloading and unzipping file 
================================

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
library(ggplot2)
if(!file.exists("./data")){dir.create("./data")}
fileurl1 <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
dest1 <- "/Users/Galka/Downloads/Coursera/Reproducible Research/Week2/data/dataset.zip"
download.file (fileurl1, dest1)
unzip(zipfile="./data/dataset.zip",exdir="./data")
setwd("/Users/Galka/Downloads/Coursera/Reproducible Research/Week2/data")
```

Read .csv file

```{r read}
activity <- read.csv("activity.csv", header = TRUE, sep = ",", dec =".", fill = TRUE, 	    na.strings = "NA")
head(activity)
```

What is mean total number of steps taken per day?
====================================================

Calculating the total number of steps taken per day
=====================================================

```{r totalstepsperday}
StepsPerDay <- aggregate (steps ~ date, activity, sum, na.action=na.omit)
head(StepsPerDay)
```

Creating Histogram of the total number of steps taken each day
===============================================================

```{r histogramtspd}
hist (StepsPerDay$steps, main="Total number of steps taken each day",
      xlab="Number of Steps")
```

Calculating and reporting the mean and median of the total number of steps taken per day
========================================================================================

```{r meanmedianspd}
spdmean <- mean(StepsPerDay$steps)
spdmedian <- median(StepsPerDay$steps)
```

`r spdmean` 
===========
is the mean of the total number of steps per day

`r spdmedian`

is the median of the total number of steps per day

What is the average daily activity pattern?
============================================

Time series plot of the average number of steps taken
=====================================================

```{r averagesteps}
StepsPerInterval <- aggregate( steps ~ interval, activity, mean)
head(StepsPerInterval)
plot(StepsPerInterval$interval,StepsPerInterval$steps, type="l", xlab="Interval", ylab="Number of Steps",main="Average Daily Activity Pattern")
```

Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
======================================================================================
```{r maxinterval}
MaxInt <- StepsPerInterval[which.max(StepsPerInterval$steps),1]
```

Interval

`r MaxInt`
==========

Imputing missing values
================================

There are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

Calculate and report the total number of missing values in the dataset
======================================================================

```{r missingvaluesnumber}
nmv <- length(which(is.na(activity$steps)))
```

There are 

`r nmv` 
=======
values missing

For filling in all of the missing values in the dataset I decided to use the mean for that 5-minute interval. I will create new dataset that is equal to the original dataset but with the missing data filled in:

```{r imputtingintervalmean}
newActivity <- transform(activity, steps = ifelse(is.na(activity$steps), StepsPerInterval$steps[match(activity$interval, StepsPerInterval$interval)], 
activity$steps))
head(newActivity)
```

Now let's create a new histogram of the total number of steps taken each day.
And then let's calculate and report the mean and median total number of steps taken per day. 

Once that is done we will be able to compare how these values differ from the estimates from the first dataset.

```{r newvalues}
NewStepsPerDay <- aggregate (steps ~ date, newActivity, sum)
hist (NewStepsPerDay$steps, main="Total number of steps taken each day",
      xlab="Number of Steps")
newspdmean <- mean(NewStepsPerDay$steps)
newspdmedian <- median(NewStepsPerDay$steps)
diffspdmean <- newspdmean - spdmean
diffspdmedian <- newspdmedian - spdmedian
```
New dataset mean

`r newspdmean`
===============

New dataset median

`r newspdmedian`
=================

Difference between new mean and old mean

`r diffspdmean`
===============
Difference between new median and old median

`r diffspdmedian`
=====================

What is the impact of imputing missing data on the estimates of the total daily number of steps?

As you can see from the numbers above there is not a significant impact on the average values after imputting missing data using this particular strategy.

Differences in activity patterns between weekdays and weekends
==============================================================

Let's create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```{r weekday}
newActivity$dayofweek <- weekdays(as.Date(newActivity$date))
head(newActivity)
```

```{r weekend}
newActivity$weekend <- ifelse (newActivity$dayofweek == "Saturday" | 
                       newActivity$dayofweek == "Sunday", "Weekend", "Weekday")
head(newActivity)
```

Below is  a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).

```{r panelplot}
WeekendWeekdayActivity <- aggregate(newActivity$steps, by=list(newActivity$weekend, newActivity$interval), mean)
colnames(WeekendWeekdayActivity) <- c ("weekend","interval", "steps")
ggplot(WeekendWeekdayActivity, aes(x = interval, y=steps, color=weekend)) +
       geom_line() +  facet_grid(weekend ~ .) +
       labs(title = "Average Number of Steps By Interval", x = "interval", y = "steps")
```
## URL to my report and plots
[URL:] (http://htmlpreview.github.io/?https://github.com/Galina722/RepData_PeerAssessment1/master/RRP1.html)
