---
layout: post
title: 'Tracking My Water Usage'
---

# Introduction
Our water service recently changed to the Consolidated Mutual Water Company [CMWC](http://www.cmwc.net/), which covers a lot of the Lakewood/WheatRidge area west of Denver. Unlike our previous service, they record our water usage at hourly intervals, and give access to the data from their website. I love tracking things and collecting and analyzing data, so I was super excited to find this out. This is a quick first look at that data and patterns of our water usage.


# Data
Data was downloaded from the [website](https://mywater.cmwc.net/) and saved as a csv file. As far as I know, there is not currently any public API that could be used to automate the data download, but I will look into this in the future. The data comes in a very clean, tidy format (unusual for data like this, kudos to CMWC!) so there isn't too much cleaning involved.



### First i'll read in the data and look at it's structure

```r
dat <- read.csv('data/export.csv')
str(dat)
```

```
## 'data.frame':	1994 obs. of  12 variables:
##  $ Account_ID : Factor w/ 2 levels "03318530-01",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ Meter_ID   : int  176358 176358 176358 176358 176358 176358 176358 176358 176358 176358 ...
##  $ Meter_SN   : int  29313580 29313580 29313580 29313580 29313580 29313580 29313580 29313580 29313580 29313580 ...
##  $ Read_Time  : Factor w/ 1994 levels "2018-02-03 00:59",..: 1 2 3 4 5 6 7 8 9 10 ...
##  $ Timezone   : Factor w/ 1 level "US/Mountain": 1 1 1 1 1 1 1 1 1 1 ...
##  $ Read       : num  44.3 44.3 44.3 44.3 44.3 ...
##  $ Read_Unit  : Factor w/ 1 level "KGAL": 1 1 1 1 1 1 1 1 1 1 ...
##  $ Read_Method: Factor w/ 1 level "Network": 1 1 1 1 1 1 1 1 1 1 ...
##  $ Flow_Time  : Factor w/ 1994 levels "2018-02-03 00:59",..: 1 2 3 4 5 6 7 8 9 10 ...
##  $ Flow_Unit  : Factor w/ 1 level "Gallons": 1 1 1 1 1 1 1 1 1 1 ...
##  $ Flow       : num  0 2.2 0.1 0.3 0.1 0 1.4 8.3 4 3.5 ...
##  $ Register   : Factor w/ 1 level "single": 1 1 1 1 1 1 1 1 1 1 ...
```

### The data is very clean, but I want to drop some extra columns and add some additional variables.
* Drop some columns we dont need
* Change dates from factor to datetime type
* Add weekday and hour variables for analysis



```r
dat <- dat %>% select(-c(Account_ID,Meter_ID,Meter_SN,Read_Method,Register)) %>%
  mutate(Read_Time=lubridate::as_datetime(Read_Time)) %>%
  mutate(Flow_Time=lubridate::as_datetime(Flow_Time)) %>%
  mutate(wkday=lubridate::wday(Read_Time,label=TRUE)) %>%
  mutate(hour=lubridate::hour(Read_Time)) %>%
  mutate(isweekend=ifelse(wkday %in% c('Sat','Sun'),1,0)) %>%
  rename(gallons=Flow) %>%
  select(-c(Read_Unit,Read,Flow_Unit))

head(dat)
```

```
##             Read_Time    Timezone           Flow_Time gallons wkday hour
## 1 2018-02-03 00:59:00 US/Mountain 2018-02-03 00:59:00     0.0   Sat    0
## 2 2018-02-03 01:59:00 US/Mountain 2018-02-03 01:59:00     2.2   Sat    1
## 3 2018-02-03 02:59:00 US/Mountain 2018-02-03 02:59:00     0.1   Sat    2
## 4 2018-02-03 03:59:00 US/Mountain 2018-02-03 03:59:00     0.3   Sat    3
## 5 2018-02-03 04:59:00 US/Mountain 2018-02-03 04:59:00     0.1   Sat    4
## 6 2018-02-03 05:59:00 US/Mountain 2018-02-03 05:59:00     0.0   Sat    5
##   isweekend
## 1         1
## 2         1
## 3         1
## 4         1
## 5         1
## 6         1
```

Ok now we're ready to roll!

# Analysis

## Time-series of flow

They just upgraded to recording meters, so the data only goes back to the beginning of February. There's not too much of a pattern in time, but we can see that the values (hourly usage) are typically less than 5, with a smaller number of larger values (dishwasher? washing machine?). This is also shown in a histogram of the data.


```r
dat %>% ggplot(aes(as.Date(Read_Time),gallons))+
  geom_point()+
  xlab("Date")+
  ylab("Gallons")
```

![](/images/water_use/unnamed-chunk-1-1.png)

```r
  ggtitle("Water Usage Timeseries")
```

```
## $title
## [1] "Water Usage Timeseries"
##
## $subtitle
## NULL
##
## attr(,"class")
## [1] "labels"
```

## Distribution of flow values

```r
dat %>% ggplot(aes(gallons))+
  geom_histogram(binwidth = 1)+
  xlab("Gallons")+
  ggtitle("Histogram of Hourly Water Usage")+
  geom_rug()
```

![](/images/water_use/unnamed-chunk-2-1.png)



## Next i'll separate the data by day of week, and look at those distributions.

There doesn't seem to be much of a difference in water usage for different days.


```r
dat %>%  ggplot(aes(wkday,gallons))+
  geom_boxplot()+
  xlab("Day of Week")+
  ggtitle("Water Usage by Day of Week")+
  ylab("Hourly Gallons Used")+
  coord_flip()
```

![](/images/water_use/unnamed-chunk-3-1.png)

There also doesn't seem to be too much of a difference between weekdays and weekends.


```r
dat %>%  ggplot(aes(x=as.factor(isweekend),y=gallons))+
  geom_boxplot()+
  xlab("Is Weekend?")+
  ggtitle("Water Usage For Weekdays and Weekends")+
  ylab("Hourly Usage (Gallons)")
```

![](/images/water_use/unnamed-chunk-4-1.png)

## Now i'll break it down by hour of day

We tend to use more water between 6 and 8 am, and from 4 to 10 pm, which probably is driven mainly by our workday schedule. Next i'll separate this into weekdays and weekends to see if this holds up; if it is due to the workday, we shouldn't see the same pattern on the weekends


```r
dat %>% mutate(hour=lubridate::hour(Read_Time)) %>%
  ggplot(aes(as.factor(hour),gallons))+
  geom_boxplot()+
  xlab("Hour of Day")+
  ylab("Gallons")+
  ggtitle("Water Usage by Hour - All Days")
```

![](/images/water_use/unnamed-chunk-5-1.png)



## Boxplots of flow by hour of day, separate for weekdays and weekends

During the week, there is a distinct pattern of more water usage in the morning and evenings which corresponds w/ the workday. We tend to use more water in the morning, probably because we tend to shower in the mornings. On the weekends, water usage is pretty evenly distributed throughout the hours we are awake.


```r
dat %>% mutate(hour=lubridate::hour(Read_Time)) %>%
  ggplot(aes(as.factor(hour),gallons))+
  geom_boxplot()+
  facet_wrap(~isweekend)+
  xlab("Hour of Day")+
  ylab("Gallons")+
  ggtitle("Water Usage by Hour - All Days")
```

![](/images/water_use/unnamed-chunk-6-1.png)

# To Come

We just started being able to get our data, so we can't look at longer-term trends like monthly or yearly yet. For example, i'd expect an increase during the summer when we are watering plants. As we get more data, we can also start seeing if our efforts to conserve water are working.
