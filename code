import requests
from flask import Flask, render_template, request
import sqlite3
import random
import string

app = Flask(__name__)
app.debug = True  # Enable debug mode for detailed error messages

def create_table():
    cursor.execute("CREATE TABLE IF NOT EXISTS website_health (id INTEGER PRIMARY KEY AUTOINCREMENT, url TEXT, status TEXT)")

def generate_random_string(length):
    letters = string.ascii_letters
    return ''.join(random.choice(letters) for _ in range(length))

def insert_random_data():
    for _ in range(10):
        url = f"https://{generate_random_string(10)}.com"
        status = random.choice(["Healthy"])
        cursor.execute("INSERT INTO website_health (url, status) VALUES (?, ?)", (url, status))

    db.commit()

def check_health(url):
    try:
        response = requests.get(url)
        if response.status_code == 200:
            return "Healthy"
        else:
            return "Unhealthy"
    except requests.exceptions.RequestException as e:
        print(f"An error occurred while checking health: {str(e)}")
        return "Unhealthy"

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/check', methods=['POST'])
def check():
    website_url = request.form['website_url']
    health_status = check_health(website_url)

    try:
        cursor.execute("INSERT INTO website_health (url, status) VALUES (?, ?)", (website_url, health_status))
        db.commit()
    except sqlite3.Error as e:
        print(f"An error occurred while inserting data into database: {str(e)}")

    return render_template('result.html', website_url=website_url, health_status=health_status)

if __name__ == '__main__':
    try:
        db = sqlite3.connect('website_health.db')
        cursor = db.cursor()
        create_table()
        insert_random_data()
        app.run()
    except Exception as e:
        print(f"An error occurred: {str(e)}")
