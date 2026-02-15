# Consuming Real-World Data via APIs

A hands-on practice repository for learning how to fetch live data using Python and APIs.

## Overview

An **API (Application Programming Interface)** is a bridge between two applications. We use the Python `requests` library to "ask" a server for data, which usually comes back in **JSON** format (similar to Python dictionaries).

## Key Concepts

| Concept | Description |
|---------|-------------|
| **HTTP GET** | Fetching/retrieving data from a server |
| **HTTP POST** | Sending data to a server |
| **Status 200** | Success - request completed |
| **Status 404** | Not Found - resource doesn't exist |
| **Status 401** | Unauthorized - API key missing/invalid |
| **JSON** | Data format that looks like Python dictionaries |

## Setup

```bash
# Install required library
pip install requests

# Or use requirements.txt
pip install -r requirements.txt
```

## Practice Files (Progressive Difficulty)

| File | Difficulty | Topic |
|------|------------|-------|
| `part1_basic_request.py` | Beginner | Simple GET request |
| `part2_status_codes.py` | Beginner+ | Understanding response codes |
| `part3_user_input.py` | Intermediate | Dynamic queries with input() |
| `part4_error_handling.py` | Intermediate+ | Robust error handling |
| `part5_real_api.py` | Advanced | Real-world API (Weather/Crypto) |

## How to Run

```bash
python part1_basic_request.py
python part2_status_codes.py
python part3_user_input.py
python part4_error_handling.py
python part5_real_api.py
```

## Testing APIs Before Coding

### Using cURL (Command Line)
```bash
# Test a simple API
curl https://api.coinpaprika.com/v1/coins/btc-bitcoin

# Test with headers
curl -H "Accept: application/json" https://api.open-meteo.com/v1/forecast?latitude=28.61&longitude=77.23&current_weather=true
```

### Using Postman
1. Download Postman from https://www.postman.com/downloads/
2. Create a new GET request
3. Enter the API URL
4. Click "Send" and observe the JSON response
5. Check for `200 OK` status

## Free APIs Used (No API Key Required)

| API | Description | Documentation |
|-----|-------------|---------------|
| JSONPlaceholder | Fake REST API for testing | https://jsonplaceholder.typicode.com/ |
| Open-Meteo | Weather data | https://open-meteo.com/ |
| CoinPaprika | Cryptocurrency data | https://api.coinpaprika.com/ |

## Resources

- [Requests Library Documentation](https://docs.python-requests.org/)
- [HTTP Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [JSON Format](https://www.json.org/)

## ------------------------------------ ##
Part 1: Basic API Requests

In this section, I learned how to use Python to "talk" to a server. An API request is like a conversation: I ask for data (Request), and the server gives it back (Response). 

Exercises Performed:
1. Fetching Specific Data
Task: Change the URL to fetch a different post (Post #5). 
What happened: By changing the ID at the end of the URL, the server sent back a completely different set of data. 
url = "https://jsonplaceholder.typicode.com/posts/5"

2. Fetching Lists
Task: Get a list of all users from /users. 
What I learned: APIs can return single items or large lists. I practiced using a Python for loop to print out only the names from this list. 
url = "https://jsonplaceholder.typicode.com/users"

3. Handling "Not Found" Errors
Task: Request a post that doesn't exist (Post 999). 
Observation: The Status Code changed from 200 (Success) to 404 (Not Found). This taught me that I always need to check if the server actually found what I asked for before I try to use the data.
url = "https://jsonplaceholder.typicode.com/posts/999"

## ---------------------------------------- ##
Part 2: Status Codes and Data Parsing

I learned how to handle different HTTP status codes (like 200 for OK and 404 for Not Found) and how to navigate through JSON data.

Exercises Performed:

1. Specific Data Extraction: Fetched User #5 and successfully printed only their phone number.

response = requests.get("https://jsonplaceholder.typicode.com/users/5")
data = response.json()
print(f"User 5 Phone: {data['phone']}")

2. Conditional Logic: Added if response.status_code == 200 to make the code "smarter" so it only tries to read data if the request was successful.

response = requests.get("https://jsonplaceholder.typicode.com/posts/1")
if response.status_code == 200:
    print("Success!", response.json()['title'])
else:
    print("Resource not found!")

3. List Processing: Used the len() function to count how many comment objects were returned for a specific post.

response = requests.get("https://jsonplaceholder.typicode.com/posts/1/comments")
comments = response.json()
print(f"Post 1 has {len(comments)} comments.")

## ------------------------------------------------- ##
Part 3: Dynamic Queries with User Input

I learned how to make my programs interactive by using input() and f-strings to build dynamic URLs.

Exercises Performed:

1. Real-World Weather: Connected to the Open-Meteo API to fetch live temperature data.

# Fetching weather for Delhi (Lat: 28.61, Long: 77.23)
url = "https://api.open-meteo.com/v1/forecast?latitude=28.61&longitude=77.23&current_weather=true"
response = requests.get(url)
weather = response.json()
print(f"Current Temp in Delhi: {weather['current_weather']['temperature']}°C")

2. Filtering Data: Used query parameters to search through a "To-Do" list and only show tasks that were marked as completed.

# Finding only 'completed' tasks
url = "https://jsonplaceholder.typicode.com/todos"
params = {"completed": "true"}
response = requests.get(url, params=params)
done_tasks = response.json()
print(f"Found {len(done_tasks)} completed tasks.")

3. Data Validation: Added a check to ensure the user inputs a valid number before making the API call, preventing the program from crashing.

user_id = input("Enter ID: ")
if user_id.isdigit(): # Check if it's a number
    print("Valid ID! Searching...")
else:
    print("Error: Please enter a number!")

## ----------------------------------------------- ##
Part 4: Robust Error Handling

I learned that a good programmer expects things to go wrong. I learned how to use try and except blocks to handle network issues gracefully.

Exercises Performed:

1. Retry Logic: Created a loop that automatically retries an API call 3 times if it fails, which is very useful for unstable connections.

import time

for i in range(3): # Try 3 times
    try:
        response = requests.get("https://jsonplaceholder.typicode.com/posts/1")
        if response.status_code == 200:
            print("Got it!")
            break
    except:
        print(f"Attempt {i+1} failed, retrying...")
        time.sleep(2) # Wait 2 seconds before trying again

2. Data Validation: Learned to check if specific "Keys" exist in the JSON response before trying to print them, preventing "KeyError" crashes.

data = response.json()
# Check if 'quotes' is actually in the data
if 'quotes' in data and 'USD' in data['quotes']:
    print(f"Price: {data['quotes']['USD']['price']}")
else:
    print("Data format has changed!")

3. Timeout Protection: Added a timeout limit to requests so the program doesn't hang forever if a server is slow.

import logging
import requests

# Step 1: Set up the logger
# This tells Python to show 'INFO' level messages (like status updates)
logging.basicConfig(level=logging.INFO, format='%(levelname)s - %(message)s')

def fetch_data(url):
    logging.info(f"Attempting to connect to: {url}") # Log the start
    try:
        response = requests.get(url, timeout=5)
        response.raise_for_status()
        logging.info("Successfully fetched data!") # Log success
        return response.json()
    except Exception as e:
        logging.error(f"Failed! Reason: {e}") # Log the specific error
        return None

# Test it
fetch_data("https://jsonplaceholder.typicode.com/posts/1")

## -------------------------------------------- ##
Part 5: Real-World API Dashboard

I learned how to integrate multiple APIs into one single "Main" program. I also learned the difference between GET (reading data) and POST (sending data to a server).

Exercises Performed:

1. Custom Locations: Expanded the city database using global coordinates.

Add more cities to the CITIES dictionary
Find coordinates at: https://www.latlong.net/

2. POST Requests: Successfully practiced "sending" data to a server, not just receiving it.

url = "https://jsonplaceholder.typicode.com/posts"
my_data = {"title": "My GitHub Journey", "body": "I learned APIs!", "userId": 1}

# We use .post() instead of .get()
response = requests.post(url, json=my_data)
print(f"Post Success! Created ID: {response.json()['id']}")

3. Data Export: Created a "Save to File" feature using the json library so data can be stored locally.

import json
import requests

def save_weather_to_file(city_name):
    # (Pretend we got this data from the API)
    data = {"city": city_name, "temp": 25, "condition": "Sunny"}
    
    # 'w' means Write mode
    with open("weather_report.json", "w") as f:
        # indent=4 makes the file look pretty and readable
        json.dump(data, f, indent=4)
    
    print(f"✅ Data saved to weather_report.json")

save_weather_to_file("Delhi")

4. Security Best Practices: Learned how to use Environment Variables (os.environ) to keep API keys safe and hidden from GitHub.

import os
import requests

# This looks for a 'password' stored in your computer's settings
# In VS Code terminal, you'd set it like: export API_KEY='your_secret_here'
api_key = os.environ.get("MY_SECRET_API_KEY")

if not api_key:
    print("Error: I couldn't find your API Key!")
else:
    print("Key found! Ready to connect to the private server.")