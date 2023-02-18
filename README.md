# -Analyzing-Historical-Stock-Revenue-Data-and-Building-a-Dashboard
!pip install yfinance==0.1.67
!mamba install bs4==4.10.0 -y
!pip install nbformat==4.2.0

import yfinance as yf

# define the ticker symbol
tickerSymbol = 'TSLA'

# get data on this ticker
tickerData = yf.Ticker(tickerSymbol)

# get the historical prices for this ticker
tickerDf = tickerData.history(period='1d', start='2010-1-1', end='2022-2-17')

# display the first five rows of the dataframe
print(tickerDf.head())

# reset the index, save, and display the first five rows of the tesla_data dataframe
tesla_data = tickerDf.reset_index()
tesla_data.to_csv('tesla_data_updated.csv', index=False)
print(tesla_data.head())

import requests
from bs4 import BeautifulSoup
import pandas as pd

url = 'https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue'
html_data = requests.get(url).text

soup = BeautifulSoup(html_data, 'html.parser')

table = soup.find('table', class_='historical_data_table')

data = []
for row in table.tbody.find_all('tr'):
    cols = row.find_all('td')
    cols = [col.text.strip() for col in cols]
    data.append(cols)

tesla_revenue = pd.DataFrame(data, columns=['Date', 'Revenue'])

# remove dollar signs and commas from Revenue column
tesla_revenue['Revenue'] = tesla_revenue['Revenue'].str.replace(',', '').str.replace('$', '')

# convert Revenue column to float
tesla_revenue['Revenue'] = tesla_revenue['Revenue'].astype(float)

# display the last five rows of the tesla_revenue dataframe
print(tesla_revenue.tail())

import pandas as pd

# scrape GME revenue data from macrotrends.net
gme_revenue_url = 'https://www.macrotrends.net/stocks/charts/GME/gamestop/revenue'
gme_revenue = pd.read_html(gme_revenue_url)[1]

# select relevant columns and rename them
gme_revenue = gme_revenue[['Year', 'Revenue']]
gme_revenue.columns = ['Year', 'Revenue (Millions of USD)']

# remove commas and dollar signs from Revenue column
gme_revenue['Revenue (Millions of USD)'] = gme_revenue['Revenue (Millions of USD)'].str.replace(',', '')
gme_revenue['Revenue (Millions of USD)'] = gme_revenue['Revenue (Millions of USD)'].str.replace('$', '')

# convert Revenue column to numeric data type
gme_revenue['Revenue (Millions of USD)'] = pd.to_numeric(gme_revenue['Revenue (Millions of USD)'])

# display last five rows of gme_revenue DataFrame
print(gme_revenue.tail())

import matplotlib.pyplot as plt

def make_graph(x, y, title="", xlabel="", ylabel=""):
    fig, ax = plt.subplots()
    ax.plot(x, y)
    ax.set_title(title)
    ax.set_xlabel(xlabel)
    ax.set_ylabel(ylabel)
    plt.show()

# plot Tesla stock data with make_graph function
make_graph(tesla_data['Date'], tesla_data['Close'], title="Tesla Stock Price", xlabel="Date", ylabel="Closing Price (USD)")

import matplotlib.pyplot as plt

def make_graph(x, y, title="", xlabel="", ylabel=""):
    fig, ax = plt.subplots()
    ax.plot(x, y)
    ax.set_title(title)
    ax.set_xlabel(xlabel)
    ax.set_ylabel(ylabel)
    plt.show()

# plot GameStop stock data with make_graph function
make_graph(gme_data['Date'], gme_data['Close'], title="GameStop Stock Price", xlabel="Date", ylabel="Closing Price (USD)")
