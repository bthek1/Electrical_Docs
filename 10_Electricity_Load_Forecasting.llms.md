# Nixtla - Electricity Load Forecast

[In this example we will show how to perform electricity load forecasting considering a model capable of handling multiple seasonalities (MSTL).](https://nixtla.github.io/statsforecast/docs/tutorials/electricityloadforecasting.html)

Author

Benedict Thekkel

## Introduction

Some time series are generated from very low frequency data. These data generally exhibit multiple seasonalities. For example, hourly data may exhibit repeated patterns every hour (every 24 observations) or every day (every 24 \* 7, hours per day, observations). This is the case for electricity load. Electricity load may vary hourly, e.g., during the evenings electricity consumption may be expected to increase. But also, the electricity load varies by week. Perhaps on weekends there is an increase in electrical activity.

In this example we will show how to model the two seasonalities of the time series to generate accurate forecasts in a short time. We will use hourly PJM electricity load data. The original data can be found [here](https://github.com/jnagura/Energy-consumption-prediction-analysis).

## Libraries

In this example we will use the following libraries:

- `StatsForecast`. Lightning ⚡️ fast forecasting with statistical and econometric models. Includes the MSTL model for multiple seasonalities.
- [`DatasetsForecast`](https://github.com/Nixtla/datasetsforecast). Used to evaluate the performance of the forecasts.
- [`Prophet`](https://github.com/facebook/prophet). Benchmark model developed by Facebook.
- [`NeuralProphet`](https://github.com/ourownstory/neural_prophet). Deep Learning version of `Prophet`. Used as benchark.

## Forecast using Multiple Seasonalities

### Electricity Load Data

According to the [dataset’s page](https://www.kaggle.com/datasets/robikscube/hourly-energy-consumption),

> PJM Interconnection LLC (PJM) is a regional transmission organization (RTO) in the United States. It is part of the Eastern Interconnection grid operating an electric transmission system serving all or parts of Delaware, Illinois, Indiana, Kentucky, Maryland, Michigan, New Jersey, North Carolina, Ohio, Pennsylvania, Tennessee, Virginia, West Virginia, and the District of Columbia. The hourly power consumption data comes from PJM’s website and are in megawatts (MW).

Let’s take a look to the data.

``` python
df = pd.read_csv('https://raw.githubusercontent.com/panambY/Hourly_Energy_Consumption/master/data/PJM_Load_hourly.csv')
df.head()
```

|     | Datetime            | PJM_Load_MW |
|-----|---------------------|-------------|
| 0   | 1998-12-31 01:00:00 | 29309.0     |
| 1   | 1998-12-31 02:00:00 | 28236.0     |
| 2   | 1998-12-31 03:00:00 | 27692.0     |
| 3   | 1998-12-31 04:00:00 | 27596.0     |
| 4   | 1998-12-31 05:00:00 | 27888.0     |

``` python
type(df)
```

    pandas.core.frame.DataFrame

``` python
df.columns = ['ds', 'y']
df.insert(0, 'unique_id', 'PJM_Load_hourly')
df['ds'] = pd.to_datetime(df['ds'])
df = df.sort_values(['unique_id', 'ds']).reset_index(drop=True)
df.tail()
```

|       | unique_id       | ds                  | y       |
|-------|-----------------|---------------------|---------|
| 32891 | PJM_Load_hourly | 2001-12-31 20:00:00 | 36392.0 |
| 32892 | PJM_Load_hourly | 2001-12-31 21:00:00 | 35082.0 |
| 32893 | PJM_Load_hourly | 2001-12-31 22:00:00 | 33890.0 |
| 32894 | PJM_Load_hourly | 2001-12-31 23:00:00 | 32590.0 |
| 32895 | PJM_Load_hourly | 2002-01-01 00:00:00 | 31569.0 |

``` python
type(df['ds'])
```

    pandas.core.series.Series

``` python
df.plot(x='ds', y='y')
```

![](10_Electricity_Load_Forecasting_files/figure-html/cell-6-output-1.png)

We clearly observe that the time series exhibits seasonal patterns. Moreover, the time series contains `32,896` observations, so it is necessary to use very computationally efficient methods to display them in production.

### MSTL model

The `MSTL` (Multiple Seasonal-Trend decomposition using LOESS) model, originally developed by [Kasun Bandara, Rob J Hyndman and Christoph Bergmeir](https://arxiv.org/abs/2107.13462), decomposes the time series in multiple seasonalities using a Local Polynomial Regression (LOESS). Then it forecasts the trend using a custom non-seasonal model and each seasonality using a `SeasonalNaive` model.

`StatsForecast` contains a fast implementation of the `MSTL` model. Also, the decomposition of the time series can be calculated.

First we must define the model parameters. As mentioned before, the electricity load presents seasonalities every 24 hours (Hourly) and every 24 \* 7 (Daily) hours. Therefore, we will use `[24, 24 * 7]` as the seasonalities that the MSTL model receives. We must also specify the manner in which the trend will be forecasted. In this case we will use the `AutoARIMA` model.

``` python
intervals = ConformalIntervals()

mstl = MSTL(
    season_length=[24, 24 * 7], # seasonalities of the time series 
    trend_forecaster=AutoARIMA(), # model used to forecast trend
    prediction_intervals = intervals
)
```

Once the model is instantiated, we have to instantiate the `StatsForecast` class to create forecasts.

``` python
sf = StatsForecast(
    models=[mstl], # model used to fit each time series 
    freq='H', # frequency of the data
    fallback_model = SeasonalNaive(season_length=7)
)
```

#### Fit the model

Afer that, we just have to use the `fit` method to fit each model to each time series.

``` python
sf = sf.fit(df=df)
```

#### Decompose the time series in multiple seasonalities

Once the model is fitted, we can access the decomposition using the `fitted_` attribute of `StatsForecast`. This attribute stores all relevant information of the fitted models for each of the time series.

In this case we are fitting a single model for a single time series, so by accessing the fitted\_ location \[0, 0\] we will find the relevant information of our model. The `MSTL` class generates a `model_` attribute that contains the way the series was decomposed.

``` python
sf.fitted_[0, 0].model_
```

|       | data    | trend        | seasonal24   | seasonal168 | remainder   |
|-------|---------|--------------|--------------|-------------|-------------|
| 0     | 22259.0 | 26183.898892 | -5215.124554 | 609.000432  | 681.225229  |
| 1     | 21244.0 | 26181.599305 | -6255.673234 | 603.823918  | 714.250011  |
| 2     | 20651.0 | 26179.294886 | -6905.329895 | 636.820423  | 740.214587  |
| 3     | 20421.0 | 26176.985472 | -7073.420118 | 615.825999  | 701.608647  |
| 4     | 20713.0 | 26174.670877 | -7062.395760 | 991.521912  | 609.202971  |
| ...   | ...     | ...          | ...          | ...         | ...         |
| 32891 | 36392.0 | 33123.552727 | 4387.149171  | -488.177882 | -630.524015 |
| 32892 | 35082.0 | 33148.242575 | 3479.852929  | -682.928737 | -863.166767 |
| 32893 | 33890.0 | 33172.926165 | 2307.808829  | -650.566775 | -940.168219 |
| 32894 | 32590.0 | 33197.603322 | 748.587723   | -555.177849 | -801.013195 |
| 32895 | 31569.0 | 33222.273902 | -967.124123  | -265.895357 | -420.254422 |

32896 rows × 5 columns

Let’s look graphically at the different components of the time series.

``` python
sf.fitted_[0, 0].model_.tail(24 * 28).plot(subplots=True, grid=True)
plt.tight_layout()
plt.show()
```

![](10_Electricity_Load_Forecasting_files/figure-html/cell-11-output-1.png)

We observe that there is a clear trend towards the high (orange line). This component would be predicted with the `AutoARIMA` model. We can also observe that every 24 hours and every `24 * 7` hours there is a very well defined pattern. These two components will be forecast separately using a `SeasonalNaive` model.

#### Produce forecasts

To generate forecasts we only have to use the `predict` method specifying the forecast horizon (`h`). In addition, to calculate prediction intervals associated to the forecasts, we can include the parameter `level` that receives a list of levels of the prediction intervals we want to build. In this case we will only calculate the 90% forecast interval (`level=[90]`).

``` python
forecasts = sf.predict(h=24, level=[90])
forecasts.head()
```

|                 | ds                  | MSTL         | MSTL-lo-90   | MSTL-hi-90   |
|-----------------|---------------------|--------------|--------------|--------------|
| unique_id       |                     |              |              |              |
| PJM_Load_hourly | 2002-01-01 01:00:00 | 29956.744141 | 27476.636719 | 32436.849609 |
| PJM_Load_hourly | 2002-01-01 02:00:00 | 29057.691406 | 26577.585938 | 31537.796875 |
| PJM_Load_hourly | 2002-01-01 03:00:00 | 28654.699219 | 26174.593750 | 31134.806641 |
| PJM_Load_hourly | 2002-01-01 04:00:00 | 28499.009766 | 26018.904297 | 30979.115234 |
| PJM_Load_hourly | 2002-01-01 05:00:00 | 28821.716797 | 26341.611328 | 31301.824219 |

Let’s look at our forecasts graphically.

``` python
_, ax = plt.subplots(1, 1, figsize = (20, 7))
df_plot = pd.concat([df, forecasts]).set_index('ds').tail(24 * 7)
df_plot[['y', 'MSTL']].plot(ax=ax, linewidth=2)
ax.fill_between(df_plot.index, 
                df_plot['MSTL-lo-90'], 
                df_plot['MSTL-hi-90'],
                alpha=.35,
                color='orange',
                label='MSTL-level-90')
ax.set_title('PJM Load Hourly', fontsize=22)
ax.set_ylabel('Electricity Load', fontsize=20)
ax.set_xlabel('Timestamp [t]', fontsize=20)
ax.legend(prop={'size': 15})
ax.grid()
```

![](10_Electricity_Load_Forecasting_files/figure-html/cell-13-output-1.png)

In the next section we will plot different models so it is convenient to reuse the previous code with the following function.

### Performance of the MSTL model

#### Split Train/Test sets

To validate the accuracy of the `MSTL` model, we will show its performance on unseen data. We will use a classical time series technique that consists of dividing the data into a training set and a test set. We will leave the last 24 observations (the last day) as the test set. So the model will train on `32,872` observations.

``` python
df_test = df.tail(24)
df_train = df.drop(df_test.index)
```

#### MSTL model

In addition to the `MSTL` model, we will include the `SeasonalNaive` model as a benchmark to validate the added value of the `MSTL` model. Including `StatsForecast` models is as simple as adding them to the list of models to be fitted.

``` python
sf = StatsForecast(
    models=[mstl, SeasonalNaive(season_length=24)], # add SeasonalNaive model to the list
    freq='H'
)
```

To measure the fitting time we will use the `time` module.

``` python
from time import time
```

To retrieve the forecasts of the test set we only have to do fit and predict as before.

``` python
init = time()
sf = sf.fit(df=df_train)
forecasts_test = sf.forecast(h=len(df_test), level=[90],  prediction_intervals = intervals)
end = time()
forecasts_test.head()
```

|  | ds | MSTL | MSTL-lo-90 | MSTL-hi-90 | SeasonalNaive | SeasonalNaive-lo-90 | SeasonalNaive-hi-90 |
|----|----|----|----|----|----|----|----|
| unique_id |  |  |  |  |  |  |  |
| PJM_Load_hourly | 2001-12-31 01:00:00 | 28345.212891 | 23706.974609 | 32983.449219 | 28326.0 | 23668.550781 | 32983.449219 |
| PJM_Load_hourly | 2001-12-31 02:00:00 | 27567.455078 | 22929.216797 | 32205.693359 | 27362.0 | 22704.550781 | 32019.449219 |
| PJM_Load_hourly | 2001-12-31 03:00:00 | 27260.001953 | 22621.763672 | 31898.240234 | 27108.0 | 22450.550781 | 31765.449219 |
| PJM_Load_hourly | 2001-12-31 04:00:00 | 27328.125000 | 22689.886719 | 31966.363281 | 26865.0 | 22207.550781 | 31522.449219 |
| PJM_Load_hourly | 2001-12-31 05:00:00 | 27640.673828 | 23002.435547 | 32278.910156 | 26808.0 | 22150.550781 | 31465.449219 |

``` python
time_mstl = (end - init) / 60
print(f'MSTL Time: {time_mstl:.2f} minutes')
```

    MSTL Time: 0.98 minutes

Then we were able to generate forecasts for the next 24 hours. Now let’s look at the graphical comparison of the forecasts with the actual values.

``` python
plot_forecasts(df_train, df_test, forecasts_test, models=['MSTL', 'SeasonalNaive'])
```

![](10_Electricity_Load_Forecasting_files/figure-html/cell-19-output-1.png)

Let’s look at those produced only by `MSTL`.

``` python
plot_forecasts(df_train, df_test, forecasts_test, models=['MSTL'])
```

![](10_Electricity_Load_Forecasting_files/figure-html/cell-20-output-1.png)

We note that `MSTL` produces very accurate forecasts that follow the behavior of the time series. Now let us calculate numerically the accuracy of the model. We will use the following metrics: `MAE`, `MAPE`, `MASE`, `RMSE`, `SMAPE`.

``` python
from datasetsforecast.losses import (
    mae, mape, mase, rmse, smape
)
```

``` python
evaluate_performace(df_train, df_test, forecasts_test, models=['MSTL', 'SeasonalNaive'])
```

|               | mase     | mae         | mape     | rmse        | smape    |
|---------------|----------|-------------|----------|-------------|----------|
| MSTL          | 0.341926 | 709.932048  | 2.182804 | 892.888012  | 2.162832 |
| SeasonalNaive | 0.894653 | 1857.541667 | 5.648190 | 2201.384101 | 5.868604 |

We observe that `MSTL` has an improvement of about 60% over the `SeasonalNaive` method in the test set measured in `MASE`.

#### Comparison with Prophet

One of the most widely used models for time series forecasting is `Prophet`. This model is known for its ability to model different seasonalities (weekly, daily yearly). We will use this model as a benchmark to see if the `MSTL` adds value for this time series.

``` python
from prophet import Prophet

# create prophet model
prophet = Prophet(interval_width=0.9)
init = time()
prophet.fit(df_train)
# produce forecasts
future = prophet.make_future_dataframe(periods=len(df_test), freq='H', include_history=False)
forecast_prophet = prophet.predict(future)
end = time()
# data wrangling
forecast_prophet = forecast_prophet[['ds', 'yhat', 'yhat_lower', 'yhat_upper']]
forecast_prophet.columns = ['ds', 'Prophet', 'Prophet-lo-90', 'Prophet-hi-90']
forecast_prophet.insert(0, 'unique_id', 'PJM_Load_hourly')
forecast_prophet.head()
```

    22:54:15 - cmdstanpy - INFO - Chain [1] start processing
    22:54:47 - cmdstanpy - INFO - Chain [1] done processing

|  | unique_id | ds | Prophet | Prophet-lo-90 | Prophet-hi-90 |
|----|----|----|----|----|----|
| 0 | PJM_Load_hourly | 2001-12-31 01:00:00 | 25294.246960 | 20311.733404 | 29908.815190 |
| 1 | PJM_Load_hourly | 2001-12-31 02:00:00 | 24000.725423 | 18852.571714 | 28911.111485 |
| 2 | PJM_Load_hourly | 2001-12-31 03:00:00 | 23324.771966 | 18382.021271 | 28217.281060 |
| 3 | PJM_Load_hourly | 2001-12-31 04:00:00 | 23332.519871 | 18219.344766 | 28193.514933 |
| 4 | PJM_Load_hourly | 2001-12-31 05:00:00 | 24107.126827 | 19242.526107 | 29079.004972 |

``` python
time_prophet = (end - init) / 60
print(f'Prophet Time: {time_prophet:.2f} minutes')
```

    Prophet Time: 0.58 minutes

``` python
times = pd.DataFrame({'model': ['MSTL', 'Prophet'], 'time (mins)': [time_mstl, time_prophet]})
times
```

|     | model   | time (mins) |
|-----|---------|-------------|
| 0   | MSTL    | 0.982553    |
| 1   | Prophet | 0.576759    |

We observe that the time required for `Prophet` to perform the fit and predict pipeline is greater than `MSTL`. Let’s look at the forecasts produced by `Prophet`.

``` python
forecasts_test = forecasts_test.merge(forecast_prophet, how='left', on=['unique_id', 'ds'])
```

``` python
plot_forecasts(df_train, df_test, forecasts_test, models=['MSTL', 'SeasonalNaive', 'Prophet'])
```

![](10_Electricity_Load_Forecasting_files/figure-html/cell-27-output-1.png)

We note that `Prophet` is able to capture the overall behavior of the time series. However, in some cases it produces forecasts well below the actual value. It also does not correctly adjust the valleys.

``` python
evaluate_performace(df_train, df_test, forecasts_test, models=['MSTL', 'Prophet', 'SeasonalNaive'])
```

|               | mase     | mae         | mape     | rmse        | smape    |
|---------------|----------|-------------|----------|-------------|----------|
| MSTL          | 0.341926 | 709.932048  | 2.182804 | 892.888012  | 2.162832 |
| Prophet       | 1.099551 | 2282.966977 | 7.374979 | 2721.817203 | 7.726533 |
| SeasonalNaive | 0.894653 | 1857.541667 | 5.648190 | 2201.384101 | 5.868604 |

In terms of accuracy, `Prophet` is not able to produce better forecasts than the `SeasonalNaive` model, however, the `MSTL` model improves `Prophet`’s forecasts by 69% (`MASE`).

With respect to numerical evaluation, `NeuralProphet` improves the results of `Prophet`, as expected, however, `MSTL` improves over `NeuralProphet`’s foreacasts by 68% (`MASE`).

> **IMPORTANT:**
>
> The performance of `NeuralProphet` can be improved using hyperparameter optimization, which can increase the fitting time significantly. In this example we show its performance with the default version.

## Conclusion

In this post we introduced `MSTL`, a model originally developed by [Kasun Bandara, Rob Hyndman and Christoph Bergmeir](https://arxiv.org/abs/2107.13462) capable of handling time series with multiple seasonalities. We also showed that for the PJM electricity load time series offers better performance in time and accuracy than the `Prophet` and `NeuralProphet` models.

## References

- [Bandara, Kasun & Hyndman, Rob & Bergmeir, Christoph. (2021). “MSTL: A Seasonal-Trend Decomposition Algorithm for Time Series with Multiple Seasonal Patterns”](https://arxiv.org/abs/2107.13462).

------------------------------------------------------------------------

Back to top
