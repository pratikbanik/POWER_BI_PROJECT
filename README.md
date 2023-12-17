# VISUALIZING STOCK MARKET TRENDS USING POWER BI

## Overview

    This project visualizes stock market trends through a dynamic Power BI dashboard, leveraging the Alpha Vantage API for comprehensive stock-related insights.

## Purpose

    The Stock Market Dashboard enhances decision-making for investors and analysts by providing a consolidated view of stock trends, financial performance, and news.


## Technologies Used
    - Power BI
    - Alpha Vantage API


## Dashboard Components

### Stock Overview Dashboard

    - Opening, closing, high, low, and volume insights.
    - Historical stock data with specific dates.
    - Company overview.

### Cashflow Dashboard

    - Operating cashflow.
    - Capital expenditure.
    - Profit loss.
    - Dividend payout.
    - Net income.
    - Fiscal data ending of the stock.

### Candlestick Dashboard

    - Visualization of stock price movements using candlestick charts.

### News & More Dashboard

    - Integration of news related to the selected stock.
    - Additional insights and information.

### Ticker

    - Real-time updating of stock data.
    - Functionality to switch between different stocks.


## Data Source

### Alpha Vantage API

    The Alpha Vantage API is utilized as the main data source for stock market information. Alpha Vantage offers a comprehensive set of financial APIs for retrieving historical and real-time stock data, technical indicators, and other financial information. The API allows the project to access key stock market metrics and enhance the dashboard with up-to-date and relevant data.


## Data Processing

### Power Query for Overview

    let
    apiKey = "YOUR API KEY",
    baseUrl = "https://www.alphavantage.co/query?",

    getCompanyOverview = (symbol as text) =>
        let
            // Define API parameters
            params = [
                #"function" = "OVERVIEW",
                #"symbol" = symbol,
                #"apikey" = apiKey
            ],

            // Construct the URL
            apiUrl = baseUrl & Uri.BuildQueryString(params),

            // Make API request
            response = Web.Contents(apiUrl),

            // Parse JSON response
            jsonResponse = Json.Document(response)
        in
            jsonResponse,

    // Reference the Ticker parameter
    symbolToQuery = Ticker,
    companyOverview = getCompanyOverview(symbolToQuery),
    #"Converted to Table" = Record.ToTable(companyOverview)
    in
    #"Converted to Table"


### Power Query for Stocks

    let
    apiKey = "YOUR API KEY",
    baseUrl = "https://www.alphavantage.co/query?",

    getStockData = (symbol as text, interval as text, outputSize as text) =>
        let
            // Define API parameters
            params = [
                #"function" = "TIME_SERIES_DAILY",
                #"symbol" = symbol,
                #"interval" = interval,
                #"outputsize" = outputSize,
                #"apikey" = apiKey
            ],

            // Construct the URL
            apiUrl = baseUrl & Uri.BuildQueryString(params),

            // Make API request
            response = Web.Contents(apiUrl),

            // Parse JSON response
            jsonResponse = Json.Document(response)
        in
            jsonResponse,

    // Set the default Ticker value
    symbolToQuery = "AAPL",
    stockData = getStockData(symbolToQuery, "1d", "full"),
    #"Time Series (Daily)" = stockData[#"Time Series (Daily)"],
    #"Converted to Table" = Record.ToTable(#"Time Series (Daily)"),
    #"Expanded Value" = Table.ExpandRecordColumn(#"Converted to Table", "Value", {"1. open", "2. high", "3. low", "4. close", "5. volume"}, {"Value.1. open", "Value.2. high", "Value.3. low", "Value.4. close", "Value.5. volume"}),
    #"Renamed Columns" = Table.RenameColumns(#"Expanded Value",{{"Name", "Date"}, {"Value.1. open", "Open"}, {"Value.2. high", "High"}, {"Value.3. low", "Low"}, {"Value.4. close", "Close"}, {"Value.5. volume", "Volume"}}),
    #"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"Date", type date}, {"Open", type number}, {"High", type number}, {"Low", type number}, {"Close", type number}, {"Volume", Int64.Type}})
    in
    #"Changed Type"


### Power Query for Cashflow Statement

    let
    apiKey = "YOUR API KEY",
    baseUrl = "https://www.alphavantage.co/query?",

    getCashFlow = (symbol as text) =>
        let
            // Define API parameters
            params = [
                #"function" = "CASH_FLOW",
                #"symbol" = symbol,
                #"apikey" = apiKey
            ],

            // Construct the URL
            apiUrl = baseUrl & Uri.BuildQueryString(params),

            // Make API request
            response = Web.Contents(apiUrl),

            // Parse JSON response
            jsonResponse = Json.Document(response)
        in
            jsonResponse,

    // Reference the Ticker parameter
    symbolToQuery = Ticker,
    cashFlowData = getCashFlow(symbolToQuery),
    annualReports = cashFlowData[annualReports],
    #"Converted to Table" = Table.FromList(annualReports, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"fiscalDateEnding", "operatingCashflow", "capitalExpenditures", "profitLoss", "dividendPayout", "netIncome"}, {"Column1.fiscalDateEnding", "Column1.operatingCashflow", "Column1.capitalExpenditures", "Column1.profitLoss", "Column1.dividendPayout", "Column1.netIncome"}),
    #"Changed Type" = Table.TransformColumnTypes(#"Expanded Column1",{{"Column1.netIncome", type number}, {"Column1.dividendPayout", type number}, {"Column1.profitLoss", type number}, {"Column1.capitalExpenditures", type number}, {"Column1.operatingCashflow", type number}, {"Column1.fiscalDateEnding", type date}})
    in
    #"Changed Type"


### Power Query for News

    let
    Source = Xml.Tables(Web.Contents("https://news.google.com/rss/search?q="&Ticker)),
    #"Changed Type" = Table.TransformColumnTypes(Source,{{"Attribute:version", Int64.Type}}),
    channel = #"Changed Type"{0}[channel],
    #"Changed Type1" = Table.TransformColumnTypes(channel,{{"generator", type text}, {"title", type text}, {"link", type text}, {"language", type text}, {"webMaster", type text}, {"copyright", type text}, {"lastBuildDate", type datetime}, {"description", type text}}),
    item = #"Changed Type1"{0}[item],
    #"Sorted Rows" = Table.Sort(item,{{"pubDate", Order.Descending}}),
    #"Renamed Columns" = Table.RenameColumns(#"Sorted Rows",{{"title", "HEADLINES"}})
    in
    #"Renamed Columns"


## How to Use :

### Opening the Dashboard

    1. Download the Power BI Desktop app from the Microsoft Store.

    2. Open the file using Power BI.


### Selecting Stocks

    Utilize the Ticker component to dynamically switch between different stocks.


### Navigating Dashboards

    Switch between different dashboards (Company Overview, Cashflow, Candlestick, News & More) using the navigation menu.


### Interacting with Visualizations

    Hover over and click on individual visualizations to access specific insights.


## Screenshots

### Snap of Stock Overview Dashboard

![Stock Overview](https://github.com/pratikbanik/POWER_BI_PROJECT/assets/104691152/4ebd0f0f-8208-4574-a0d8-ba23f73a349e)

### Snap of Stock Dashboard

![Stock Dashboard](https://github.com/pratikbanik/POWER_BI_PROJECT/assets/104691152/19187235-5b26-4d35-be2a-1b59e783ff42)

### Snap of Stock Candlestick Dashboard

![Stock Candlestick](https://github.com/pratikbanik/POWER_BI_PROJECT/assets/104691152/c3b40cfb-0ece-4c3f-89a6-de8a1d49d2ef)

### Snap of Stock News Dashboard

![Stock News   More](https://github.com/pratikbanik/POWER_BI_PROJECT/assets/104691152/d248e2bc-9236-45a0-b6ee-f1fba52b1393)


## Dashboard Links

1. [Overview Dashboard](https://app.powerbi.com/groups/me/reports/dfa2c415-233d-4169-97bb-cfc8f25d9201/ReportSection2abaaf014012b6836555?experience=power-bi)

2. [Stocks Dashboard](https://app.powerbi.com/groups/me/reports/dfa2c415-233d-4169-97bb-cfc8f25d9201/ReportSectionb276eb5a9db1e2b41c46?experience=power-bi)

3. [Candlestick Dashboard](https://app.powerbi.com/groups/me/reports/dfa2c415-233d-4169-97bb-cfc8f25d9201/ReportSection57a433c641c0b0aeda57?experience=power-bi)

4. [News & More Dashboard](https://app.powerbi.com/groups/me/reports/dfa2c415-233d-4169-97bb-cfc8f25d9201/ReportSectionda60f0bab4bc2b44fe91?experience=power-bi)


## Future Improvements

    1. Predictive Analytics
    2. Advanced Technical Indicators
    3. Sentiment Analysis Integration
    4. Integration with External APIs
    5. Community Collaboration


## Credits

1. [Power BI Documentation](https://docs.microsoft.com/en-us/power-bi/)

2. [Alpha Vantage API Documentation](https://www.alphavantage.co/documentation/)

3. [Simplilearn](https://www.simplilearn.com/)

4. [Microsoft Power Query M Language Documentation](https://docs.microsoft.com/en-us/powerquery-m/)


## Conclusion

    The Stock Market Dashboard is a valuable tool for navigating the complexities of the stock market. Thank you for using the dashboard!
