Interacting with Polygon Financial Data API
================
David Shaw
10/1/2021

-   [1 Requirements](#requirements)
-   [2 API Interactions](#api-interactions)
    -   [2.1 Stocker Ticker values](#stocker-ticker-values)
-   [3 Exploratory](#exploratory)
-   [4 Conclusion](#conclusion)

# 1 Requirements

The following packages must be installed using
`install.packages('packageName')` and imported into environment using
`library(packageName)`

-   [tidyverse](https://www.tidyverse.org/)
-   [httr](https://cran.r-project.org/web/packages/httr/vignettes/quickstart.html)
-   [jsonlite](https://cran.r-project.org/web/packages/jsonlite/vignettes/json-aaquickstart.html)

Accessing the API

-   Retrieve API token at <https://polygon.io/docs/getting-started>

# 2 API Interactions

First, place your api token in a global variable called API_TOKEN

``` r
API_TOKEN='qpnTdSpewzcPBpPm_jUHtmhe_peuQsi6'
```

## 2.1 Stocker Ticker values

Parameters:

-   `stocksTicker`: ticker symbol of stock

-   `timespan`: time between measurements (minute, hour, day, week,
    month, quarter, year)

-   `from`: date to collect market prices from in format YYYY-mm-dd

-   `to`: date to collect market prices to in format YYYY-mm-dd

    ``` r
    stockPrices <- function(stocksTicker='AAPL', timespan='day', from='2021-01-01', to='2021-01-14') {
      # build url
      url = paste('https://api.polygon.io/v2/aggs/ticker/', stocksTicker, '/range/1/', timespan, '/', from, '/', to,
                    '/?adjusted=true&sort=asc&apiKey=', API_TOKEN, sep = '')

      # make api call and convert to dataframe
      raw <- GET(url)
      parsed <- fromJSON(rawToChar(raw$content))
      parsed$results
    }
    ```

# 3 Exploratory

placeholder

# 4 Conclusion

placeholder
