---
layout: post
title: 'Winter Is Coming (with higher energy bills)'
---


Winter is coming, but for us that means higher energy bills, not [whitewalkers](http://gameofthrones.wikia.com/wiki/White_Walkers) :) . We've had a few unseasonably warm days and not much snow yet, but it's definitely colder and we are now using our heat almost every night. I thought it would be fun to explore our energy usage data in R. 


# Data
[Xcel energy](https://www.xcelenergy.com/) lets you download your usage data as a csv file. However it is not in a format that is easy to read in to R; it's probably possible to write code to read in the sections you want, but it really just wasn't worth it for an analysis I might do once a year. So I just copied the data I needed into a new clean spreadsheet.




```r
library(ggplot2)
theme_set(theme_gray(base_size = 18))
suppressPackageStartupMessages(library(dplyr))
suppressPackageStartupMessages(library(lubridate))
```

## Gas data

```r
# load gas data
gas <- read.csv('data/xcel_gas.csv',stringsAsFactors = FALSE)
gas <-gas %>% rename(Date=Last.Read.Date,therms=Gas.Usage..Therms.,avgTemp=Average.Temperature) %>% 
  select(Date,therms,avgTemp) %>% 
  mutate(Date=mdy(Date)) %>% 
  mutate(avgTemp=as.integer(gsub("[^0-9]", "", avgTemp) ))
head(gas)
```

```
##         Date therms avgTemp
## 1 2017-11-16     58      47
## 2 2017-10-18     37      54
## 3 2017-09-18      7      72
## 4 2017-08-17      5      71
## 5 2017-07-19      6      73
## 6 2017-06-19     17      63
```



## Electric Data

```r
ele <- read.csv('data/xcel_elec.csv',stringsAsFactors = FALSE)

ele <- ele %>% rename(Date=Last.Read.Date,kWh=Electric.Usage..kWh.,avgTemp=Average.Temperature) %>% 
  select(Date,kWh,avgTemp) %>% 
  mutate(Date=mdy(Date)) %>% 
  mutate(avgTemp=as.integer(gsub("[^0-9]", "", avgTemp) ))

head(ele)
```

```
##         Date kWh avgTemp
## 1 2017-11-16 340      47
## 2 2017-10-18 324      54
## 3 2017-09-18 376      72
## 4 2017-08-17 323      71
## 5 2017-07-19 331      73
## 6 2017-06-19 304      63
```



# Analysis

## Timeseries of temperature, gas use, and electricity use

```r
p1 <- gas %>% ggplot(aes(Date,avgTemp))+
    geom_line(size=1)+
  geom_point(size=3)+
  ggtitle("Average monthly temperature")

p2 <-gas %>% ggplot(aes(Date,therms))+
  geom_line(size=1)+
  geom_point(size=3)+
  ggtitle("Natural Gas Use")

p3 <-ele %>% ggplot(aes(Date,kWh))+
  geom_line(size=1)+
  geom_point(size=3)+
  ggtitle("Electrcity Use")

gridExtra::grid.arrange(p1,p2,p3)
```

![](/images/energy_use/unnamed-chunk-5-1.png)<!-- -->


## Gas use vs temperature
* A scatter plot shows there is a very strong correlation between gas use and temperature
* This makes sense because we have gas heat, and the only other gas appliance we have is the water heater.
* You can see there is sort of a 'break' in the relationship for temperatures above 70 deg. We never use our heat if it's hotter than 70 deg, so gas usage shouldn't depend on temperature above 70 deg.



```r
p1 <- gas %>% ggplot(aes(avgTemp,therms))+
  geom_point(size=3)+
  geom_smooth(method='lm')+
  ggtitle("Gas Usage vs Temperature ; All Data")+
   xlab("Monthly Avg. Temperature")

p2 <- gas %>% filter(avgTemp<70) %>% 
  ggplot(aes(avgTemp,therms))+
  geom_point(size=3)+
  geom_smooth(method='lm')+
  ggtitle("Gas Usage vs Temperature ; Only Temp <70")+
   xlab("Monthly Avg. Temperature")


gridExtra::grid.arrange(p1,p2)
```

![](/images/energy_use/unnamed-chunk-6-1.png)<!-- -->

## Fit a linear regression of Gas Usage vs. Avg. Temperature

We can fit a linear regression to quantify the dependence of gas usage on temperature. I fit 2 models; 1 with all the data, and another excluding data where temperature>70.

### Model w/ All Data

```r
model1 <- lm(therms~avgTemp,data=gas)
broom::tidy(model1)
```

```
##          term   estimate std.error statistic      p.value
## 1 (Intercept) 169.453948 8.3931827  20.18947 3.372132e-11
## 2     avgTemp  -2.363515 0.1492451 -15.83647 7.055392e-10
```

### Model w/ Only Temperature<70

```r
model2 <- lm(therms~avgTemp,data=gas %>% filter(avgTemp<70))
broom::tidy(model2)
```

```
##          term   estimate std.error statistic      p.value
## 1 (Intercept) 185.057937 8.5847151  21.55668 1.030139e-09
## 2     avgTemp  -2.705768 0.1665392 -16.24703 1.619307e-08
```

* The model slope is -2.71, which means that for every degree colder it is (below 70 degress), we use 2.71 more therms of natural gas. 
* We are planning to add insulation to our attic, so it will be interesting to see if the slope changes after that (hopefully it does!)



## Plot electricity use vs Temperature

```r
p1 <- ele %>% ggplot(aes(avgTemp,kWh))+
  geom_point(size=3)+
  geom_smooth(method = 'lm')+
  ggtitle("kWh vs. Temperature : All Data")+
   xlab("Monthly Avg. Temperature")


p2 <- ele %>% filter(kWh<450) %>%
  ggplot(aes(avgTemp,kWh))+
  geom_point(size=3)+
  geom_smooth(method = 'lm') +
  ggtitle("kWh vs. Temperature : January 2017 Removed")+
 xlab("Monthly Avg. Temperature")
   
gridExtra::grid.arrange(p1,p2)
```

![](/images/energy_use/unnamed-chunk-9-1.png)<!-- -->


* From the timeseries plot above, the electricity usage looks fairly constant, except for a spike in January 2017. We have gas heat, so why the spike? Well when we bought the house, there was no heat vent in the back room and we used an electric heater there; hence the spike. We had a vent installed in February and didn't need to use the electric heater anymore. The first value is also lower than the rest; probably because we were moving in during this period and our first bill didn't cover a full month.

* The scatter plots above show there isn't really much of a relationship between electricity use and temperature. The 3 higher points above 70 degrees might be due to our window AC unit, but other than that there isn't much seasonality to our electricity use.

* We also just got an electric car that we charge at home every few nights, so it will be interesting to see how that changes our electricity usage.


Try it out with your energy data and let me know what you find!
