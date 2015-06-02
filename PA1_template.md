---
title: "PA1_template"
author: "XXXX"
date: "Tuesday, June 02, 2015"
output: html_document
---
This R Markdown document is to answer four different operation as listed below 

Activity Data File Name : **activity.csv**

**1. What is mean total number of steps taken per day?**

Loading, preprocessing the data,  calculating and plotting steps taken per day in 

```r
temp_data <- read.csv("activity.csv",header=TRUE)
```

```
## Warning in file(file, "rt"): cannot open file 'activity.csv': No such file
## or directory
```

```
## Error in file(file, "rt"): cannot open the connection
```

```r
require(dplyr)
```

```
## Loading required package: dplyr
## 
## Attaching package: 'dplyr'
## 
## The following objects are masked from 'package:lubridate':
## 
##     intersect, setdiff, union
## 
## The following object is masked from 'package:stats':
## 
##     filter
## 
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
require(ggplot2)
```

```
## Loading required package: ggplot2
```

```r
require(lattice)
```

```
## Loading required package: lattice
```

```r
df_dplyr<-tbl_df(temp_data)
```

```
## Error in is.data.frame(data): object 'temp_data' not found
```

```r
rm(temp_data)
```

```
## Warning in rm(temp_data): object 'temp_data' not found
```

```r
total <- summarise(group_by(df_dplyr,date),sum=sum(steps,na.rm=TRUE))
qplot(total$sum, binwidth=1000, xlab="Total number of steps taken each day")
```

![plot of chunk unnamed-chunk-1](figure/unnamed-chunk-1-1.png) 

Mean of the total number of steps taken per day is  

```r
mean(total$sum, na.rm=TRUE)
```

```
## [1] 9354.23
```

Median of the total number of steps taken per day is 

```r
median(total$sum, na.rm=TRUE)
```

```
## [1] 10395
```

**2. What is the average daily activity pattern?**

Time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
averages <- aggregate(x = list(steps = df_dplyr$steps), by = list(interval = df_dplyr$interval),   FUN = mean, na.rm = TRUE)
ggplot(data = averages, aes(x = interval, y = steps)) + geom_line() + xlab("5-minute interval") + ylab("Average number of steps taken")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

5-minute interval which has maximum number of steps, on average across all the days in the dataset

```r
averages[which.max(averages$steps), ]
```

```
##     interval    steps
## 104      835 206.1698
```

**3. Imputing missing values, making histogram and report both mean & median**


```r
temp<-cbind(temp,averages$steps)
colnames(temp)[4]<-"avg"
temp$steps<-ifelse(is.na(temp$steps),temp$avg,temp$steps)
total1 <- summarise(group_by(temp,date),sum=sum(steps))
qplot(total1$sum, binwidth=1000, xlab="Total number of steps taken each day")
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6-1.png) 

Mean of the total number of steps taken per day is  

```r
mean(total1$sum)
```

```
## [1] 10766.19
```

Median of the total number of steps taken per day is 

```r
median(total1$sum)
```

```
## [1] 10766.19
```

**4. Are there differences in activity patterns between weekdays and weekends?**


```r
temp$weekend <-format(as.Date(temp$date), "%u") %in% c(6, 7)
wd_temp <-subset(temp,weekend==FALSE)
we_temp <-subset(temp,weekend==TRUE)
we_averages <- aggregate(x = list(steps = we_temp$steps), by = list(interval = we_temp$interval),   FUN = mean)
wd_averages <- aggregate(x = list(steps = wd_temp$steps), by = list(interval = wd_temp$interval),   FUN = mean)
wd_averages$day <-c("weekday")
we_averages$day <-c("weekend")
averages_all<-rbind(we_averages,wd_averages)
xyplot(steps~interval | day,data=averages_all, main="Steps patterns Weekdays Vs Weekend", xlab="Interval", ylab="Number of steps",layout=c(1,2),type="l")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png) 
