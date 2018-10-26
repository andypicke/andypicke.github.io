---
layout: post
title: 'Citibike Part 1 : Data collection and Preparation'
tags: python
---

This is the first in a series of blog posts i'll be writing on a project to analyze NYC [Citibike](https://www.citibikenyc.com/) data. In this part, i'll discuss getting and storing the data, and my workflow for analyzing a very large dataset. I've written about some of the pieces of this workflow previously, and will include links to those posts where appropriate.

The Citibike data is available [here](https://s3.amazonaws.com/tripdata/index.html), and is stored in [AWS S3](https://aws.amazon.com/s3/). S3 is a cloud storage service offered by Amazon Web Services (AWS). You can click and download individual files; however since I wanted all the files I used the command line to download all the files at once:

```
aws s3 cp s3://tripdata . --recursive --exclude "*JC"
```
You can also interact w/ S3 through python; see my previous post [here](https://andypicke.github.io/aws_s3/).

The dataset i'm working with contains information on rides taken between July 2013 and March 2017. There is a row for each ride, so the dataset is very big (more than 39 million rows!). Since this is way too big to work w/ in memory in python, I decided to store the data in a [SQLlite](https://www.sqlite.org/index.html) database. SQLlite is a simple, light version of SQL that doesn't require setting up a database server, which makes it great for individual analyses like this.

# Populating the database
The first step is to create a database table to store the data in. This could be done programmatically, but I found it easier in this case to just make the table in SQLiteStudio, a GUI for SQLite. I also created a separate tables for the station data, since this data is repeated redundantly in the data.

I used a python [script](https://github.com/andypicke/NYC_citibike/blob/master/read_citibike_monthly_to_sql.py) and *pandas* to read the data files and populate the database (see my [previous post](https://andypicke.github.io/sql_pandas_post/)). This takes a bit of time because there is so much data, but it only needs to be done once.


# Using the SQL!
In my analysis I won't be looking at individual rides, but rather statistics of rides aggregated over longer time periods such as days, weeks, months, and years. SQL is designed to perform such aggregations on very large datasets quickly. As an example, to count the total number of rides each month the SQL code would be:

``` SQL
SELECT COUNT(*),date
FROM table
GROUP BY date
```

You can execute SQL statements in SQLlite itself, but it's better to run the queries from python in order to have reproducible code. There are many python libraries for working w/ databases. Pandas has some built-in functions (using those libraries?) for interacting w/ databases, which makes it easy to integrate into my normal workflow of using pandas and matplotlib for analysis. Python sends a SQL query, which is executed in the SQL database, and then returns the results.

Example python code to run a query calculating the number of rides per year:

``` python
query = "SELECT year, COUNT(*) as Nrides FROM rides GROUP BY year"
df_year = pd.read_sql_query(query,con)
```

Although SQL is much quicker, it still takes a bit of time on such a large dataset (a few minutes on my (old) laptop). My strategy was to use SQL to aggregate and summarize the large dataset, and then pull those smaller result sets into python for analysis and modeling. I wrote a [script](https://github.com/andypicke/NYC_citibike/blob/master/citibike_MakeDataFromSQL.ipynb) to do all of the operations I will want for the analysis, and save the results in csv files that can be quickly loaded instead of having to run the sql queries every time I update the analysis. Some of the operations I performed were counting the number of rides per day month, year, and day.

# Other data

In addition to the Citibike data, I also obtained data on weather and holidays to use as predictors in the model.
- Weather data is for LaGuardia Airport, and was downloaded from http://www.wunderground.com/ with the script [get_weather_data.py](https://github.com/andypicke/NYC_citibike/blob/master/get_weather_data.py). I made a separate SQLite database for the weather data.
- A list of holiday dates was downloaded from https://holidayapi.com/ with [get_holidays.ipynb](https://github.com/andypicke/NYC_citibike/blob/master/get_holidays.ipynb), and stored in a database table.


In the next post, i'll do some exploratory data analysis (EDA) of the data to detect patterns and trends, and inform the modeling stage.


One last Note: if you prefer to use R, everything I've described here can also be done in a very similar way, using dplyr and ggplot instead of pandas and matplotlib. I use both R and python, but chose to use python for this analysis, because I like sci-kit learn better for modeling.
