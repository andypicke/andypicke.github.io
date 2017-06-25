---
layout: post
title: Using a SQL database w/ Python and Pandas
tags: python sql
---

When you're working with small enough datasets, you can simply load the data into memory and work with it in python. But sometimes you have so much data that loading it into memory is either impossible or very slow. In that case, storing your data in a [SQL](https://en.wikipedia.org/wiki/SQL) database might be a good option. A SQL database allows you to run queries on large datasets much more efficiently than if the data was stored in csv format.

I’m currently working on a project where the amount of data was too much to work with in python all at once, so I decided to instead store my data in a SQL database. I'm doing my analysis and visualization in python, so I also wanted to learn how to do all this from python. In this post, i'll go over an example of how to add data to a SQL database and query the database in python using Pandas. It’s actually very easy!

First you need to create a database to add to or read from. I’m using [SQLite](https://en.wikipedia.org/wiki/SQLite), because it is simple and easy, but you can work with pretty much any SQL variant in python (for working with types other than SQLite in Pandas, you need the SQLAlchemy package (<https://pandas.pydata.org/pandas-docs/stable/io.html#sql-queries>). I created my empty database in [SQLiteStudio](https://sqlitestudio.pl/index.rvt). 

Next we need to add some tables and data. You could create the tables in SQLiteStudio first and then add data to them, but I will create the tables from python. This is nice because it automatically names all the columns, and you have a script that will reproduce everything. 

First we need to open a connection to the database. My database is called 'sqlite_example.db3'. We import the sqlite3 package and use it to connect to the database.


```python
import sqlite3 
con = sqlite3.connect("/Users/Andy/sqlite_example.db3")
```

Next i'll read in some of the data i'm working with, historical ridership for the [Citibike](https://www.citibikenyc.com/) bike-rental program in NYC. I've already downloaded the data as a csv file.


```python
import pandas as pd
df = pd.read_csv('/Users/Andy/2014-03 - Citi Bike trip data.csv',parse_dates=True)
```



Now I want to store this data in my SQL database. We can create a SQL table w/ the Pandas method **to_sql**.


```python
df.to_sql("rides", con, if_exists='replace', index=False) 
```

This will write the data frame to a table called “rides”. The *if_exists* parameter specifies what to do if a table of the same name already exists. 
- '*replace*' will replace the pre-existing table. 
- '*fail*' will return an error (useful if you want to be sure you don’t overwrite a table).  
- '*append*' will add to the table.

Now that we have populated the database, we want to run a query and extract the results. This is done w/ the pandas **read_sql_query** method. For example, if I want to get all the rows where _tripduration_ was less than 500, I would use:


```python
df = pd.read_sql_query("SELECT * FROM rides WHERE tripduration < 500 ",con)
```


Note you don’t actually have to capitalize the SQL query commands, but it is standard practice, and makes them much easier to read. The nice thing about using this method to query the database is that it returns the results of the query in a Pandas dataframe, which you can then easily manipulate or analyze.

You will probably also want to use variables in your SQL queries. To do this, you can pass an additional argument _params_ to **read_sql_query()**. The format for placing variables in the SQL query varies depending on the type of SQL, but for SQLite you just put a ? where you want the variable(s) to go. For example, say I want to extract rows for all the rides that started at the station with id 439:

```python
id = 439
df = pd.read_sql_query("SELECT * FROM rides WHERE start_station_id=? ", con, params=[id])
```


In this example i’ve only used one month of data, which could could have handled in memory. But in the project i’m working on, i’ll be analyzing years of data, which is much too large to load into memory. One option would be to read it in chunks and perform operations on each chunk. But that is clunky and would require re-reading the data for every new query I want to make. Storing the data in a SQL database allows me to easily and quickly query the entire dataset, and have the results immediately available for analysis in python. 



Some resources I found helpful:
- <https://www.dataquest.io/blog/python-pandas-databases/>
- <http://www.datacarpentry.org/python-ecology-lesson/08-working-with-sql/>
- <https://pandas.pydata.org/pandas-docs/stable/io.html#sql-queries>


```python

```
