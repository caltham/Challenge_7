# Challenge_7

The goal of this project is to build a financial database and web application by using SQL, Python, and the Voilà library to analyze the performance of a hypothetical fintech ETF.

## Technology

Import the required libraries.
~~~
import numpy as np
import pandas as pd
import hvplot.pandas
import sqlalchemy
~~~

## Installation Guide

Create a temporary SQLite database and populate the database with content from the etf.db seed file.
~~~
database_connection_string = 'sqlite:///etf.db'
~~~

Create an engine to interact with the SQLite database.
~~~
engine = sqlalchemy.create_engine(database_connection_string)
~~~

## Usage

### Analyze a single asset in the FinTech ETF

1. Write a SQL SELECT statement by using an f-string that reads all the PYPL data from the database. Using the SQL SELECT statement, execute a query that reads the PYPL data from the database into a Pandas DataFrame.
~~~
query = pd.read_sql_query(
    """
    SELECT * FROM PYPL
    """, database_connection_string)
~~~

Use the query to read the PYPL data into a Pandas DataFrame.
~~~
pypl_dataframe = pd.DataFrame(query)
pypl_dataframe.set_index('time', inplace=True)
~~~

2. Using hvPlot, create an interactive visualization for the PYPL daily returns. Reflect the “time” column of the DataFrame on the x-axis. Make sure that you professionally style and format your visualization to enhance its readability.
~~~
pypl_dataframe.hvplot(legend = True, figsize=(20, 15), title="PYPL Daily Returns", x='time', y='daily_returns')
~~~
<img width="707" alt="Plot_1" src="https://user-images.githubusercontent.com/94569323/150719841-05641f39-3f7b-47ea-b5db-91df94774fa9.png">

3. Using hvPlot, create an interactive visualization for the PYPL cumulative returns. Reflect the “time” column of the DataFrame on the x-axis. Make sure that you professionally style and format your visualization to enhance its readability.
~~~
cumulative_returns = (1 + pypl_dataframe['daily_returns']).cumprod() - 1
cumulative_returns.hvplot(legend = True, figsize=(20, 15), title="PYPL Cumulative Returns", x='time')
~~~
<img width="703" alt="Plot_2" src="https://user-images.githubusercontent.com/94569323/150719910-93e93727-3357-42da-b4c8-01b48ea54a59.png">

### Optimize the SQL Queries.

1. Access the closing prices for PYPL that are greater than 200.
~~~
query_2 = pd.read_sql_query(
    """
    SELECT time, close FROM PYPL
    WHERE close > 200
    """, database_connection_string)
~~~

Using the query, read the data from the database into a Pandas DataFrame.
~~~
pypl_higher_than_200 = pd.DataFrame(query_2)
pypl_higher_than_200.set_index('time', inplace=True)
~~~

2. Find the top 10 daily returns for PYPL.
~~~
query_3 = pd.read_sql_query(
    """
    SELECT time, daily_returns FROM PYPL
    ORDER BY daily_returns DESC
    LIMIT 10;
    """, database_connection_string)
~~~

Using the query, read the data from the database into a Pandas DataFrame.
~~~
pypl_top_10_returns = pd.DataFrame(query_3)
pypl_top_10_returns.set_index('time', inplace=True)
~~~

### Analyze the Fintech ETF Portfolio.

1. Write a SQL query to join each table in the portfolio into a single DataFrame.
~~~
query_4 = pd.read_sql_query(
    """
    SELECT *
    FROM GDOT
    JOIN GS ON GDOT.time = GS.time
    JOIN PYPL ON GDOT.time = PYPL.time
    JOIN SQ ON GDOT.time = SQ.time
    """, database_connection_string)
~~~

Using the query, read the data from the database into a Pandas DataFrame.
~~~
etf_portfolio = pd.DataFrame(query_4)
~~~

2. Create a DataFrame that averages the “daily_returns” columns for all four assets. Review the resulting DataFrame.
~~~
query_5 = pd.read_sql_query(
    """
    SELECT GDOT.time, GDOT.daily_returns, GS.daily_returns, PYPL.daily_returns, SQ.daily_returns
    FROM GDOT
    JOIN GS ON GDOT.time = GS.time
    JOIN PYPL ON GDOT.time = PYPL.time
    JOIN SQ ON GDOT.time = SQ.time
    """, database_connection_string)

etf_portfolio_returns = pd.DataFrame(query_5)
etf_portfolio_returns.set_index("time", inplace = True)
etf_portfolio_returns_mean = etf_portfolio['daily_returns'].mean()
~~~

3. Use the average daily returns in the etf_portfolio_returns DataFrame to calculate the annualized returns for the portfolio. Display the annualized return value of the ETF portfolio.
~~~
annualized_etf_portfolio_returns = etf_portfolio_returns_mean * 252
~~~

4. Use the average daily returns in the etf_portfolio_returns DataFrame to calculate the cumulative returns of the ETF portfolio.
~~~
etf_cumulative_returns = ((1 + etf_portfolio_returns).cumprod() - 1).set_axis(['GDOT', 'GS', 'PYPL', 'SQ'], axis='columns')
etf_cumulative_returns_mean = ((1 + etf_portfolio_returns_mean).cumprod() - 1).set_axis(['GDOT', 'GS', 'PYPL', 'SQ'], axis='index')
~~~

5. Using hvPlot, create an interactive line plot that visualizes the cumulative return values of the ETF portfolio. Reflect the “time” column of the DataFrame on the x-axis. Make sure that you professionally style and format your visualization to enhance its readability.
~~~
etf_cumulative_returns.hvplot(title = "Cumulative Returns", legend = True)
~~~
<img width="703" alt="Plot_3" src="https://user-images.githubusercontent.com/94569323/150719946-5573bbbf-4e66-4491-a0ed-a71cbaf273e6.png">

### Voila

<img width="489" alt="Voila_1" src="https://user-images.githubusercontent.com/94569323/150721412-84f42521-5a16-4c2a-9409-5d5d53f356f6.png">
<img width="1171" alt="Voila_2" src="https://user-images.githubusercontent.com/94569323/150721443-8e9aae43-8072-45fa-8b81-9aece243fc6a.png">
<img width="1187" alt="Voila_3" src="https://user-images.githubusercontent.com/94569323/150721454-5d2d25dc-658e-4a14-8f90-2448f7211a9c.png">

### Conclusion

Based on the hvPlot of the cumulative returns for the four stocks it is evident that the SQ stock was the highest performing stock among the group. However, it can be noted that the mean values of the daily returns for the four stocks were all postive numbers.

## Contributors

Chancie Altham: Developer

## License

MIT License has been added to the project.