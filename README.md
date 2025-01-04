# API-for-Currency-Conversion
from flask import Flask, jsonify, request
import requests

app = Flask(__name__)

# Example of third-party API: Open Exchange Rates
API_KEY = 'your_open_exchange_rate_api_key'
BASE_URL = 'https://openexchangerates.org/api/latest.json'

# Function to get conversion rate from the third-party API
def get_exchange_rate(base, target):
    url = f'{BASE_URL}?app_id={API_KEY}&symbols={target}'
    response = requests.get(url)
    data = response.json()
    if response.status_code == 200:
        rate = data['rates'].get(target)
        return rate
    else:
        return None

# API endpoint to convert currency
@app.route('/convert', methods=['GET'])
def convert():
    base_currency = request.args.get('base', 'USD')
    target_currency = request.args.get('target', 'EUR')
    amount = float(request.args.get('amount', 1))

    # Get the exchange rate from the third-party service
    rate = get_exchange_rate(base_currency, target_currency)
    if rate:
        converted_amount = amount * rate
        return jsonify({
            'base_currency': base_currency,
            'target_currency': target_currency,
            'amount': amount,
            'converted_amount': converted_amount,
            'rate': rate
        })
    else:
        return jsonify({'error': 'Unable to fetch exchange rate'}), 500

if __name__ == '__main__':
    app.run(debug=True)
