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
    -   [2.3 `tickerInfo(ticker)`](#tickerinfoticker)
    -   [2.4 `marketInfo(market)`](#marketinfomarket)
    -   [2.5 `stockSplit(ticker)`](#stocksplitticker)
    -   [2.6
        `stockFinancials(ticker, limit)`](#stockfinancialsticker-limit)
-   [3 Exploratory Analysis](#exploratory-analysis)
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

Return:

-   Dataframe containing volume, open/close prices, high/low prices, and
    number of transactions on each timespan of trading for this ticker
    symbol across specified dates

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

Return:

-   Dataframe of market prices of all symbols on specified day
-   If market is not open for that day (i.g. stock market is not open on
    weekends), return will be 0

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

## 2.3 `tickerInfo(ticker)`

Get a dataframe with information about tickers in a market.

Parameters:

-   `ticker`: ticker symbol of stock/forex/crypto asset
    -   Stocks have a ticker that is no more than 4 letters, all
        capitalized (ex.
        [Apple](https://finance.yahoo.com/quote/AAPL?p=AAPL&.tsrc=fin-srch)
        is AAPL)

Return:

-   Dataframe with date, and amount of dividend payment
-   If a stock does not pay dividends, an empty list will be returned

``` r
stockDividends <- function(ticker = 'AAPL') {
  # build url
  url <- paste('https://api.polygon.io/v2/reference/dividends/', ticker,
                  '?apiKey=', API_TOKEN,
                  sep = '')
  
  # make api call and convert to dataframe
  raw <- GET(url)
  parsed <- fromJSON(rawToChar(raw$content))
  parsed$results
}
```

## 2.4 `marketInfo(market)`

Get a dataframe with information about tickers in a market.

Parameters:

-   `market`: One of `stocks`, `fx` (forex), or `crypto`

Return:

-   Dataframe containing info about active tickers in specified market

``` r
marketInfo <- function(market = 'stocks') {
  # build url
  url <- paste('https://api.polygon.io/v3/reference/tickers?market=', market,
                  '&active=true&sort=ticker&order=asc&apiKey=', API_TOKEN,
                  sep = '')
  
  # make api call and convert to dataframe
  raw <- GET(url)
  parsed <- fromJSON(rawToChar(raw$content))
  parsed$results
}
```

## 2.5 `stockSplit(ticker)`

Get a dataframe with information about tickers splits in a market.

Parameters:

-   `ticker`: ticker symbol of stock/forex/crypto asset
    -   Stocks have a ticker that is no more than 4 letters, all
        capitalized (ex.
        [Apple](https://finance.yahoo.com/quote/AAPL?p=AAPL&.tsrc=fin-srch)
        is AAPL)

Return:

-   Dataframe with date, and ratio of split
-   If a stock has no history of splits, an empty list will be returned

``` r
stockSplit <- function(ticker = 'AAPL') {
  # build url
  url <- paste('https://api.polygon.io/v2/reference/splits/', ticker,
                  '?apiKey=', API_TOKEN,
                  sep = '')
  
  # make api call and convert to dataframe
  raw <- GET(url)
  parsed <- fromJSON(rawToChar(raw$content))
  parsed$results
}
```

## 2.6 `stockFinancials(ticker, limit)`

Get a dataframe with historical financial data for a stock ticker

Parameters:

-   `ticker`: ticker symbol of stock/forex/crypto asset
    -   Stocks have a ticker that is no more than 4 letters, all
        capitalized (ex.
        [Apple](https://finance.yahoo.com/quote/AAPL?p=AAPL&.tsrc=fin-srch)
        is AAPL)
-   `limit`: number of financial reports to see

Return:

-   Dataframe with date, and ratio of split
-   If a stock has no history of splits, an empty list will be returned

``` r
stockFinancials <- function(ticker = 'AAPL', limit = 5) {
  # build url
  url <- paste('https://api.polygon.io/v2/reference/financials/', ticker,
                  '?limit=', limit,
                  '&apiKey=', API_TOKEN,
                  sep = '')
  
  # make api call and convert to dataframe
  raw <- GET(url)
  parsed <- fromJSON(rawToChar(raw$content))
  parsed$results
}
```

# 3 Exploratory Analysis

placeholder

# 4 Conclusion

placeholder
