---
title: "Peer Assessment 1"
author: "Kathryn Cordiano"
date: "September 18, 2015"
output: html_document
---

##Introduction

It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These type of devices are part of the "quantified self" movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.


Dataset Used: Activity monitoring data [52K]

#The variables included in this dataset are:

- Steps: Number of steps taking in a 5-minute interval (missing values are coded as NA)

- Date: The date on which the measurement was taken in YYYY-MM-DD format

- Interval: Identifier for the 5-minute interval in which measurement was taken

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

##Assignment

#Loading and Process the Data

First we read in the data


```r
#Read in the data
data <- read.csv("C:\\Users\\matr06580\\Desktop\\activity.csv", header = T)
View(data)
```


#What is mean total number of steps taken per day?

Calculate the total number of steps taken per day, and make a histogram of data


```r
# Calculate total number of steps per day
sum_steps <- aggregate(data$steps~data$date, FUN = sum)
View(sum_steps)

#Make Histogram of this table
hist(sum_steps$`data$steps`, right = FALSE, main = "Frequency of Total Steps Taken per Day", xlab = "Number of Steps")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png) 

Calculate the mean and median of the total number of steps taken per day (sum_steps table)


```r
#Calculate mean and median of sum steps taken per day
sum_steps_mean <- mean(sum_steps$`data$steps`)
sum_steps_median <- median(sum_steps$`data$steps`)
sum_steps_mean
```

```
## [1] 10766.19
```

```r
sum_steps_median
```

```
## [1] 10765
```


#What is the average daily activity pattern?

Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
#Calculate the number of steps taken across each interval 
Average_Interval <- aggregate(data$steps~data$interval, FUN = mean)
View(Average_Interval)

#Create time-series plot of the data 
plot(Average_Interval$`data$interval`, Average_Interval$`data$steps`, type = "l", main = "Average Steps Taken Across Intervals", xlab = "Interval", ylab = "Average Number of Steps")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

Calculate which 5-minute interval has the maximum number of average steps


```r
#Calculate which 5 minute interval contains the maximum amount of steps
Max_Steps <- which.max(Average_Interval$`data$steps`)
Max_Steps
```

```
## [1] 104
```

```r
Average_Interval[104, ][1]
```

```
##     data$interval
## 104           835
```


#Imputing missing values

Count the number of NAs in the original dataset and replace them with a different value, in order to get rid of any bias in the data that results from the missing values. Then make a new dataset that has the NA values replaced with the new value you have replaced it with.


```r
#count the number of NAs in the original dataset
sum(is.na(data$steps))
```

```
## [1] 2304
```

```r
#Replace the NA values with the mean number of steps in the entire dataset
data$steps[is.na(data$steps)] <- mean(data$steps, na.rm = TRUE)

#Show the new dataset that was created with the replaced values
View(data)
```

Make a histogram using the new dataset that shows the total number of steps taken each day, then calculate the mean and median of the total number of steps taken each day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
#Create table that has the total number of steps per day
sum_steps2 <- aggregate(data$steps~data$date, FUN = sum)
View(sum_steps2)

#Create a histogram that shows the frequency of the total number of steps per day
hist(sum_steps2$`data$steps`, right = FALSE, main = "Total Number of Steps Per Day (Replaced NAs)", xlab = "Total Number of Steps")
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png) 

```r
#Calculate the mean and median of the total number of steps per day
mean_sum_steps2 <- (sum_steps2$`data$steps`)
median_sum_steps2 <- (sum_steps2$`data$steps`)
```

For this new data, the overall mean is the same at 10766.19, but the median has changed from 10765 to 10766.19. This shows that replacing the NAs with the value of the average number of steps across the whole dataset made the data more biased towards the average number of steps.


#Are there differences in activity patterns between weekdays and weekends?

Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day


```r
#Since the current data is a factor, have to convert it to a date
data_date <- as.Date(data$date)
class(data_date)
```

```
## [1] "Date"
```

```r
#Check which day of the week is each date in the data set
Day_Of_Week <- weekdays(data_date)

#Create new column in data set that shows the day of the week
data["Day_of_Week"] <- Day_Of_Week
View(data)

#Create second new column in data set that shows whether its a weekend or a weekday
Weekend <- c("Saturday", "Sunday")
Weekday <- c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday")

data["Weekend vs. Weekday"] <- ifelse(data$Day_of_Week==Weekend, "Weekend", "Weekday")
View(data)
```

Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data


```r
#Create table that only has values for days that are weekdays
Weekday_data <- subset(data, data$`Weekend vs. Weekday`=="Weekday")
View(Weekday_data)

#Create table that only has values for days that are weekends
Weekend_data <- subset(data, data$`Weekend vs. Weekday`=="Weekend")
View(Weekend_data)

#Create table that has the average number of steps taken per interval across weekdays
average_interval2 <- aggregate(Weekday_data$steps~Weekday_data$interval, FUN = mean)
View(average_interval2)

#Create table that has the average number of steps taken per interval across weekends
average_interval3 <- aggregate(Weekend_data$steps~Weekend_data$interval, FUN = mean)
View(average_interval3)

#Create a panel plot containing the time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis)
par(mfrow=c(2,1))
plot(average_interval2$`Weekday_data$interval`, average_interval2$`Weekday_data$steps`, type = "l", 
     main = "Average Number of Steps Over Weekdays", xlab = "Interval", ylab = "Average Number of Steps")
plot(average_interval3$`Weekend_data$interval`, average_interval3$`Weekend_data$steps`, type = "l", 
     main = "Average Number of Steps Over Weekends", xlab = "Interval", ylab = "Average Number of Steps")
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png) 


