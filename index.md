Interacting with Polygon Financial Data API
================
David Shaw
10/1/2021

-   [1 Requirements](#requirements)
-   [2 API Interactions](#api-interactions)
    -   [2.1
        `tickerPrices(ticker, timespan, from, to)`](#tickerpricesticker-timespan-from-to)
    -   [2.2
        `marketPricesAll(market, date)`](#marketpricesallmarket-date)
    -   [2.3
        `stockPrices(stockTicker, timespan, from, to)`](#stockpricesstockticker-timespan-from-to)
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
API_TOKEN='xxxx'
```

## 2.1 `tickerPrices(ticker, timespan, from, to)`

Get data for specified ticker over desired timespan between dates
specified by from and to.

Parameters:

-   `ticker`: ticker symbol of stock/forex/crypto asset

    -   Stocks have a ticker that is no more than 4 letters, all
        capitalized (ex.
        [Apple](https://finance.yahoo.com/quote/AAPL?p=AAPL&.tsrc=fin-srch)
        is AAPL)
    -   Forex has a ticker that begins with ‘C:’ followed by identifier
        (ex.
        [EUR/USD](https://finance.yahoo.com/quote/EURUSD%3DX?p=EURUSD%3DX)
        is C:EURUSD)
    -   Crypto has a ticker that begins with ‘X:’ followed by an
        identifier (ex.
        [Bitcoin](https://finance.yahoo.com/quote/BTC-USD/) is X:BTCUSD)

-   `timespan`: time between measurements (minute, hour, day, week,
    month, quarter, year)

-   `from`: date to collect market prices from in format YYYY-mm-dd

-   `to`: date to collect market prices to in format YYYY-mm-dd

    ``` r
    tickerPrices <- function(ticker='AAPL', 
                            timespan='day', 
                            from='2021-01-01', 
                            to='2021-01-14') {
      # build url
      url <- paste('https://api.polygon.io/v2/aggs/ticker/', ticker, 
                      '/range/1/', timespan, 
                      '/', from, 
                      '/', to,
                      '/?adjusted=true&sort=asc&apiKey=', API_TOKEN, 
                      sep = '')

      # make api call and convert to dataframe
      raw <- GET(url)
      parsed <- fromJSON(rawToChar(raw$content))
      parsed$results
    }
    ```

## 2.2 `marketPricesAll(market, date)`

Entire stock/forex/crypto market values for specified day.

Parameters:

-   `market`: One of `stocks`, `fx` (forex), or `crypto`
-   `date`: date to get stock market values in format YYYY-mm-dd

``` r
marketPricesAll <- function(market = 'stocks', date='2021-01-01') {
  # build url
  marketType <- 'global'
  if(market == 'stocks') {
    marketType <- 'us'
  }
  url <- paste('https://api.polygon.io/v2/aggs/grouped/locale/', marketType,
                  '/market/', market, 
                  '/', date,
                  '?adjusted=true&apiKey=', API_TOKEN, 
                  sep = '')
  
  # make api call and convert to dataframe
  raw <- GET(url)
  parsed <- fromJSON(rawToChar(raw$content))
  parsed$results
}
```

## 2.3 `stockPrices(stockTicker, timespan, from, to)`

Get data for specified stockTicker over desired timespan between dates
specified by from and to.

Parameters:

-   placeholder

# 3 Exploratory

placeholder

# 4 Conclusion

placeholder
