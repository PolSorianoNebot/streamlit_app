import streamlit as st
import requests
import pandas as pd

st.title("Cryptocurrency Price Notifier")
st.write("Enter the cryptocurrency and fiat currency you want to track, along with a threshold value.")

# Inputs
coin = st.text_input("Cryptocurrency (all in caps, no blanks)", "BTC")  # Default: BTC
currency = st.text_input("Fiat Currency (all in caps, no blanks)", "USD")  # Default: USD
threshold = st.number_input("Threshold Value", min_value=0.0, value=90000.0, step=1.0)  # Default: 90k
notify_button = st.button("Notify me")

# Function to plot percentage change chart using st.line_chart
def plot_percentage_change_chart_line(quote_data, coin):
    # Create DataFrame
    data = pd.DataFrame(quote_data, index=[coin])

    # Extract percentage changes
    data_perc_change = data[[
        'percent_change_1h',
        'percent_change_24h',
        'percent_change_7d',
        'percent_change_30d',
        'percent_change_60d',
        'percent_change_90d'
    ]]
    data_perc_change.columns = ['1h', '24h', '7d', '30d', '60d', '90d']
    data_transposed = data_perc_change.T
    #data_transposed.columns = [coin]

    # Show line chart
    st.subheader(f"{coin} - % Change Over Timeframes")
    st.line_chart(data_transposed)

# On button click
if notify_button:
    headers = {
        'Accepts': 'application/json',
        'X-CMC_PRO_API_KEY': st.secrets['API_KEY'],
    }

    user_request_url = f"https://pro-api.coinmarketcap.com/v1/cryptocurrency/quotes/latest?symbol={coin}&convert={currency}"
    response = requests.get(user_request_url, headers=headers)
    response_json = response.json()

    try:
        price_data = response_json['data'][coin]['quote'][currency]['price']
        st.success(f"Current Price of {coin}/{currency} pair: {round(price_data, 2)}.\nWe will notify you when the price reaches {threshold}.")

        # Plot percentage change chart
        quote_data = response_json['data'][coin]['quote'][currency]
        plot_percentage_change_chart_line(quote_data, coin)

    except KeyError:
        st.error("Could not fetch data. Please check the cryptocurrency and fiat currency symbols.")
