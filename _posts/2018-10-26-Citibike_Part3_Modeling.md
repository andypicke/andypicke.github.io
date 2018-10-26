---
layout: post
title: 'Citibke Part 3 : Modeling'
tags: python
---

# Introduction

This is the thrid in a series of blog posts i'll be writing on a project to analyze NYC citibike data. The [first post]() focused on collecting and preparing the data, and a strategy for dealing with a very large data set. In the second part, I
did some EDA (exploratory data analysis) of the data. In this final post, i'll focus on modeling with the aim of predicting the number of Citibike rides taken on a particular day.

From the EDA, I identified several variables that are correlated with the number of rides and should probably be included in my model:

* Day of week
* the number of stations
* temperature
* precipitation
* wind gust


# Modeling citibike data
## In this notebook, I create some models to predict the total number of daily Citibike rides based on a set of features identified during EDA.

https://github.com/andypicke/NYC_citibike/blob/master/citibike_regression_new.ipynb

### Import libraries and set some defaults for nice plots.


```python
# %load /Users/Andy/jupyter_imports.py
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns

# make plots look nice
plt.rcParams['font.size'] = 14
plt.rcParams['axes.labelsize'] = 'large'
plt.rcParams['xtick.labelsize'] = 'large'
plt.rcParams['ytick.labelsize'] = 'large'
plt.rcParams['lines.linewidth'] = 3

```

### Load the full combined dataset, which was made in the notebook *citibike_regression_make_data*



```python
df_comb = pd.read_csv('data/data_comb.csv')
df_comb.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>yday</th>
      <th>Nrides</th>
      <th>date</th>
      <th>Tmean</th>
      <th>precip_In</th>
      <th>max_gust_mph</th>
      <th>cloud_cover</th>
      <th>N_stations</th>
      <th>wkday_1</th>
      <th>wkday_2</th>
      <th>wkday_3</th>
      <th>wkday_4</th>
      <th>wkday_5</th>
      <th>wkday_6</th>
      <th>public</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>182</td>
      <td>16650</td>
      <td>2013-07-01</td>
      <td>76</td>
      <td>0.73</td>
      <td>26.0</td>
      <td>8</td>
      <td>326</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>183</td>
      <td>22745</td>
      <td>2013-07-02</td>
      <td>78</td>
      <td>0.06</td>
      <td>23.0</td>
      <td>7</td>
      <td>327</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>184</td>
      <td>21864</td>
      <td>2013-07-03</td>
      <td>80</td>
      <td>0.96</td>
      <td>23.0</td>
      <td>7</td>
      <td>326</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>185</td>
      <td>22326</td>
      <td>2013-07-04</td>
      <td>84</td>
      <td>0.00</td>
      <td>24.0</td>
      <td>4</td>
      <td>324</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>186</td>
      <td>21842</td>
      <td>2013-07-05</td>
      <td>85</td>
      <td>0.00</td>
      <td>23.0</td>
      <td>1</td>
      <td>325</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_comb.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1176 entries, 0 to 1175
    Data columns (total 15 columns):
    yday            1176 non-null int64
    Nrides          1176 non-null int64
    date            1176 non-null object
    Tmean           1176 non-null int64
    precip_In       1176 non-null float64
    max_gust_mph    1173 non-null float64
    cloud_cover     1176 non-null int64
    N_stations      1176 non-null int64
    wkday_1         1176 non-null int64
    wkday_2         1176 non-null int64
    wkday_3         1176 non-null int64
    wkday_4         1176 non-null int64
    wkday_5         1176 non-null int64
    wkday_6         1176 non-null int64
    public          1176 non-null float64
    dtypes: float64(3), int64(11), object(1)
    memory usage: 137.9+ KB


## Split the data into predictor (X) and target (y) arrays in prep for modelling
I drop 'Nrides', which is the target variable, and 'yday and 'date' which I will not be using as a predictor.


```python
X = df_comb.drop(columns=['date','Nrides','yday'], axis=1)
X.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Tmean</th>
      <th>precip_In</th>
      <th>max_gust_mph</th>
      <th>cloud_cover</th>
      <th>N_stations</th>
      <th>wkday_1</th>
      <th>wkday_2</th>
      <th>wkday_3</th>
      <th>wkday_4</th>
      <th>wkday_5</th>
      <th>wkday_6</th>
      <th>public</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>76</td>
      <td>0.73</td>
      <td>26.0</td>
      <td>8</td>
      <td>326</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>78</td>
      <td>0.06</td>
      <td>23.0</td>
      <td>7</td>
      <td>327</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>80</td>
      <td>0.96</td>
      <td>23.0</td>
      <td>7</td>
      <td>326</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>84</td>
      <td>0.00</td>
      <td>24.0</td>
      <td>4</td>
      <td>324</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>85</td>
      <td>0.00</td>
      <td>23.0</td>
      <td>1</td>
      <td>325</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
y = df_comb['Nrides']
y.head()
```




    0    16650
    1    22745
    2    21864
    3    22326
    4    21842
    Name: Nrides, dtype: int64



## Next I split the data into training and test sets for modeling.


```python
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size= 0.3, random_state=39)
```

## Model 1: Simple Linear Regression
- The first baseline model I will try is the simplest: a multiple linear regression.
- First I need to impute some missing values in the dataset. I will fill in missing values with the mean, using Imputer from sklearn.


```python
from sklearn.preprocessing import Imputer

imp = Imputer(strategy='mean')
imp.fit(X_train)
X_train = imp.transform(X_train)
X_test  = imp.transform(X_test)
X_all   = imp.transform(X)

```

### Now time to fit the model


```python
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import cross_val_score

reg = LinearRegression()  # instantiate model object
reg.fit(X_train,y_train)  # fit model
reg.score(X_train, y_train) # score on training data
```




    0.8285467376003368



Using *cross_val_score* gives a better estimate of how the model will perform on unseen data.


```python
cv_score_linreg = np.mean(cross_val_score(reg, X_train, y_train))
print(cv_score_linreg)
```




    0.8180402624574454



We can check the score on the test set also.


```python
# score on test set
lin_reg_test_score=reg.score(X_test,y_test)
lin_reg_test_score
```




    0.8135628727158369



And compute RMSE for comparison w/ other models as well.


```python
from sklearn.metrics import mean_squared_error
RMSE_linreg = mean_squared_error(y_test, reg.predict(X_test))**0.5
RMSE_linreg
```




    5924.665844564357



## Scatterplot actual vs predicted values from test set


```python
# plot predictions vs actual values
preds = reg.predict(X_test) # make predictions
plt.figure(figsize=(12,8))
sns.regplot(y_test,preds)
plt.xlabel('Actual Number of Rides')
plt.ylabel('Predicted Number of Rides')
plt.title('Linear Regression')
plt.grid();

plt.savefig('/Users/Andy/andypicke.github.io/images/Citibike/linreg_vs_actual.png')

```

![](/images/Citibike/linreg_vs_actual.png)


### Plot the timeseries of actual and predicted values from linear regression


```python
#
plt.figure(figsize=(14,8))
plt.plot(y,label='Actual')
plt.plot(reg.predict(X_all),'d',label='Model')
plt.legend();
plt.xlabel('Yearday')
plt.ylabel('Number Daily Rides')
plt.title('Linear Regression')
plt.grid();

plt.savefig('/Users/Andy/andypicke.github.io/images/Citibike/linreg_vs_actual_timeseries.png')
```


![](/images/Citibike/linreg_vs_actual_timeseries.png)

### Model 2: Next i'll try a Random forest regression


```python
from sklearn.ensemble import RandomForestRegressor

rf = RandomForestRegressor()
rf.fit(X_train,y_train)
rf.score(X_train, y_train)
```





    0.9775367157884189




```python
np.mean(cross_val_score(rf,X_train,y_train))
```




    0.8492788229709785




```python
rf.score(X_test,y_test)
```




    0.8831218780786286




```python
RMSE_rf = mean_squared_error(y_test, rf.predict(X_test))**0.5
RMSE_rf
```




    4690.987458246692



### Model 3: Random Forest optimized w/ gridsearchCV
- Let's see if we can improve the performance of the random forest model by doing some hyperparameter tuning.


```python
# optimize RF
from sklearn.model_selection import GridSearchCV

# grid of params to tune over
params = {'n_estimators':[10,50,100,150],
          'min_samples_split':[2,5,10] }

rf2 = RandomForestRegressor()
cv = GridSearchCV(rf2, params)
cv.fit(X_train, y_train)
rf2 = cv.best_estimator_
```

Cross-validated score estimate on training set:
```python
np.mean( cross_val_score(rf2, X_train, y_train) )
```



    0.8679546096300257



Score on test set:
```python
rf2.score(X_test, y_test)
```




    0.8885318835967604



RMSE:
```python
RMSE_rf2= mean_squared_error(y_test, rf2.predict(X_test))**0.5
RMSE_rf2
```




    4581.133953398355



```python
plt.figure(figsize=(14,8))
sns.regplot(rf2.predict(X_all), y)
plt.grid()

plt.savefig('/Users/Andy/andypicke.github.io/images/Citibike/rf_vs_actual.png')

```


![](/images/Citibike/rf_vs_actual.png)


### Compare RMSE from the 3 models

The optimized random forest regressor has the lowest RMSE.

```python
# Compare RMSE
print('Lin Reg.     : ' + str(RMSE_linreg))
print('Rand Forest  : ' + str(RMSE_rf))
print('Rand Forest opti.  : ' + str(RMSE_rf2))

```

    Lin Reg.     : 5924.665844564357
    Rand Forest  : 4690.987458246692
    Rand Forest opti.  : 4581.133953398355


## Variable importance
Examining the variable importance from the model shows that the 3 most important variables are temperature, the number of stations, and precipitation.


```python
rf_imp = pd.DataFrame({'vars':X.columns,'imp':rf.feature_importances_})
rf_imp.sort_values('imp',ascending=False)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vars</th>
      <th>imp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Tmean</td>
      <td>0.531921</td>
    </tr>
    <tr>
      <th>4</th>
      <td>N_stations</td>
      <td>0.278861</td>
    </tr>
    <tr>
      <th>1</th>
      <td>precip_In</td>
      <td>0.105064</td>
    </tr>
    <tr>
      <th>3</th>
      <td>cloud_cover</td>
      <td>0.021947</td>
    </tr>
    <tr>
      <th>2</th>
      <td>max_gust_mph</td>
      <td>0.018876</td>
    </tr>
    <tr>
      <th>10</th>
      <td>wkday_6</td>
      <td>0.018181</td>
    </tr>
    <tr>
      <th>9</th>
      <td>wkday_5</td>
      <td>0.011947</td>
    </tr>
    <tr>
      <th>6</th>
      <td>wkday_2</td>
      <td>0.004160</td>
    </tr>
    <tr>
      <th>11</th>
      <td>public</td>
      <td>0.002814</td>
    </tr>
    <tr>
      <th>5</th>
      <td>wkday_1</td>
      <td>0.002503</td>
    </tr>
    <tr>
      <th>7</th>
      <td>wkday_3</td>
      <td>0.002008</td>
    </tr>
    <tr>
      <th>8</th>
      <td>wkday_4</td>
      <td>0.001717</td>
    </tr>
  </tbody>
</table>
</div>



### Plot model residuals for both linear regression and random forest
- The plot below shows that the residuals are significantly reduced by the random forest model.
- However there are still a small number of large residuals remaining.


```python
plt.figure(figsize=(14,8))
#plt.plot(y,label='Actual')
plt.plot(y-reg.predict(X_all),'k',label='LinReg')
plt.plot(y-rf.predict(X_all),'rd',label='RF')
plt.legend();
plt.xlabel('Yearday')
plt.ylabel('Residual # Daily Rides')
plt.title('Model Residuals ');
plt.hlines(xmin=0,xmax=360,y=0);
plt.ylim(-25000,20000)

plt.savefig('/Users/Andy/andypicke.github.io/images/Citibike/linreg_rf_residuals.png')

```


![](/images/Citibike/linreg_rf_residuals.png)


## What dates do largest residuals fall on?
Sorting by residual, we can see that a lot of the largest negative residuals (model overpredicts actual value) occur near holidays (July 4th, Christmas, Thanksgiving). We did include holidays in the model, but the day or two before/after a holiday are also strongly affected. This makes sense; people often take off multiple days or a long weekend for big holidays. If really wanted to try to capture this behavior, we might create a 'near holiday' variable this is 1 on the day or two before/after a holiday.


```python
X2 = df_comb.copy()
# add residuals
X2['resid'] = y - reg.predict(X_all)
X2[['resid','date']].sort_values('resid').head(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>resid</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>-23457.483704</td>
      <td>2013-07-05</td>
    </tr>
    <tr>
      <th>1094</th>
      <td>-23002.179137</td>
      <td>2016-12-25</td>
    </tr>
    <tr>
      <th>15</th>
      <td>-20979.056450</td>
      <td>2013-07-18</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-20503.650893</td>
      <td>2013-07-04</td>
    </tr>
    <tr>
      <th>1099</th>
      <td>-20413.004310</td>
      <td>2017-01-02</td>
    </tr>
    <tr>
      <th>16</th>
      <td>-19863.673724</td>
      <td>2013-07-19</td>
    </tr>
    <tr>
      <th>779</th>
      <td>-19483.244629</td>
      <td>2015-12-24</td>
    </tr>
    <tr>
      <th>5</th>
      <td>-19107.903351</td>
      <td>2013-07-06</td>
    </tr>
    <tr>
      <th>7</th>
      <td>-17724.623738</td>
      <td>2013-07-08</td>
    </tr>
    <tr>
      <th>1065</th>
      <td>-17219.932118</td>
      <td>2016-11-25</td>
    </tr>
  </tbody>
</table>
</div>



Looking at the positive residuals (model underestimates actual values), there isn't any obvious pattern that jumps out to me. These could be days there were special events (races, concerts etc.) that disrupted the usual transportation patterns.


```python
X2[['resid','date']].sort_values('resid').tail(10)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>resid</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>912</th>
      <td>13251.521710</td>
      <td>2016-06-02</td>
    </tr>
    <tr>
      <th>1004</th>
      <td>13604.826915</td>
      <td>2016-09-16</td>
    </tr>
    <tr>
      <th>1005</th>
      <td>14157.219264</td>
      <td>2016-09-17</td>
    </tr>
    <tr>
      <th>1043</th>
      <td>14443.317593</td>
      <td>2016-11-02</td>
    </tr>
    <tr>
      <th>697</th>
      <td>14449.835545</td>
      <td>2015-09-25</td>
    </tr>
    <tr>
      <th>1014</th>
      <td>14912.695199</td>
      <td>2016-09-26</td>
    </tr>
    <tr>
      <th>1031</th>
      <td>15114.680414</td>
      <td>2016-10-20</td>
    </tr>
    <tr>
      <th>1003</th>
      <td>15120.889805</td>
      <td>2016-09-15</td>
    </tr>
    <tr>
      <th>740</th>
      <td>16168.785091</td>
      <td>2015-11-11</td>
    </tr>
    <tr>
      <th>260</th>
      <td>19579.541596</td>
      <td>2014-04-30</td>
    </tr>
  </tbody>
</table>
</div>



# Conclusions
- We built several models to predict the number of NYC Citibike rides taken on a certain date.
- The baseline model, a simple linear regression, performed fairly well with a cross-validated estimated R^2 of 0.82.
- A random forest regressor had a cross-validated estimated R^2 of 0.85.
- Hyperparameter tuning of the random forest improved the cross-validated estimate of R^2 to 0.86.
- This model had a R^2 of 0.89 on the test set.
- Examining residuals suggests that the model is significantly overpredicting on days surrounding holidays; this could be an area for future improvement.
