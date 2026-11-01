# Python API Integration

## What is an API?

### Definition
An API (Application Programming Interface) is a set of rules that allows programs to communicate with each other. It acts as an intermediary between different software systems, enabling them to exchange data and functionality.

### How APIs Work
- **Client**: The program or application that wants to get something
- **Server**: The system that has the data or service being requested
- **API**: The interface that handles requests and responses between client and server

### Why Use APIs?
1. **Data Sharing**: Same data can be served to multiple clients (website, mobile app, smartwatch)
2. **Security**: Protects databases by having an API stand in between
3. **Scalability**: Allows systems to handle many different types of requests efficiently

## Making API Requests with Python

### Installation
```bash
pip install requests python-dotenv
```

### Basic Request Structure
Every API request consists of four components:
1. **Endpoint**: The address where the resource is located
2. **Method**: What action you want to perform (GET, POST, PUT, PATCH, DELETE)
3. **Headers**: Extra information about the request
4. **Body**: Data sent with the request (for POST, PUT, PATCH)

### Simple GET Request Example
```python
import requests

# Basic GET request
response = requests.get('https://fakestoreapi.com/products')
print(response.status_code)  # Shows status code like 200
```

## Understanding API Responses

### Response Object Structure
When you make a request, Python returns a response object containing:
1. **Status Code**: Indicates success or failure (200=OK, 404=Not Found, etc.)
2. **Headers**: Information about the response
3. **Body**: The actual data returned by the API

### Accessing Response Data
```python
import requests

response = requests.get('https://fakestoreapi.com/products')

# Check status code
print(response.status_code)

# Check headers
print(response.headers['content-type'])  # Shows 'application/json'

# Get raw text data
print(response.text[:200])  # First 200 characters

# Convert to Python data structure (JSON)
data = response.json()  # Converts JSON string to Python list/dict
print(type(data))  # Shows it's now a list
```

### Common Status Codes
- **2xx**: Success (200=OK, 201=Created)
- **3xx**: Redirect
- **4xx**: Client error (404=Not Found, 401=Unauthorized, 403=Forbidden)
- **5xx**: Server error (500=Internal Server Error)

### Understanding 401 vs 403
- **401 Unauthorized**: You're not authenticated - the system doesn't know who you are
- **403 Forbidden**: You're authenticated but don't have permission to access this resource

## Working with JSON Data

### JSON and Python Data Types
| JSON | Python |
|------|--------|
| Object | Dictionary |
| Array | List |
| String | String |
| Number | Integer/Float |
| Boolean | True/False |
| Null | None |

### Converting JSON to Python Objects
```python
import requests

response = requests.get('https://fakestoreapi.com/products')
data = response.json()  # Converts JSON to Python list of dictionaries

# Now you can work with it like normal Python data
print(len(data))  # Shows number of products (20)
print(data[0]['title'])  # Gets title of first product
```

### Looping Through API Data
```python
import requests

response = requests.get('https://fakestoreapi.com/products')
products = response.json()

# Loop through all products and display key information
for product in products[:5]:  # First 5 products
    print(f"ID: {product['id']}, Title: {product['title']}, Price: ${product['price']}")
```

## API Query Parameters

### What are Query Parameters?
Query parameters are additional instructions sent with an API request to specify how you want the data returned. They appear after a `?` in the URL.

### Adding Parameters via URL
```python
import requests

# Method 1: Directly in URL
response = requests.get('https://fakestoreapi.com/products?limit=5')
data = response.json()
print(len(data))  # Will show 5 items
```

### Adding Parameters Using a Dictionary (Recommended)
```python
import requests

# Method 2: Using params parameter (cleaner approach)
params = {
    'limit': 5,
    'sort': 'desc'
}

response = requests.get('https://fakestoreapi.com/products', params=params)
data = response.json()
print(len(data))  # Will show 5 items
print(response.url)  # Shows the complete URL built by requests
```

### Working with Different Endpoints
```python
import requests

# Get categories
response = requests.get('https://fakestoreapi.com/products/categories')
categories = response.json()
print(categories)  # ['electronics', 'jewelry', "men's clothing", "women's clothing"]
```

## Headers in API Requests

### What are Headers?
Headers contain extra information about the request. They can specify:
- Content type
- Authentication information
- Acceptance preferences

### Setting Custom Headers
```python
import requests

# Set custom headers
headers = {
    'Accept': 'application/json',
    'User-Agent': 'MyPythonApp/1.0'
}

response = requests.get('https://fakestoreapi.com/products', headers=headers)
```

## Error Handling in API Requests

### Why Error Handling is Important
API calls can fail for many reasons:
- Network connectivity issues
- Server timeouts
- Invalid requests (4xx status codes)
- Unexpected data formats

### Basic Error Checking
```python
import requests

response = requests.get('https://fakestoreapi.com/users/octocat')
if response.status_code == 200:
    print("Success!")
    data = response.json()
    print(f"User: {data['name']}")
elif response.status_code == 404:
    print("User not found")
else:
    print(f"Error: {response.status_code}")
```

### Using raise_for_status() (Recommended)
```python
import requests

try:
    response = requests.get('https://fakestoreapi.com/users/octocat')
    response.raise_for_status()  # Raises exception for bad status codes
    data = response.json()
    print(f"User: {data['name']}")
except requests.exceptions.HTTPError as err:
    print(f"HTTP error occurred: {err.response.status_code}")
except requests.exceptions.ConnectionError:
    print("Connection error - check your internet")
except requests.exceptions.Timeout:
    print("Request timed out")
except requests.exceptions.RequestException as err:
    print(f"An error occurred: {err}")
```

### Creating a Reusable Error Handling Function
```python
import requests

def get_json(url, params=None, headers=None, timeout=5):
    """
    Safely fetch JSON data from an API with proper error handling
    """
    try:
        response = requests.get(url, params=params, headers=headers, timeout=timeout)
        response.raise_for_status()
        return response.json()
    except requests.exceptions.HTTPError as err:
        print(f"HTTP Error {err.response.status_code}: {err.response.reason}")
        return None
    except requests.exceptions.ConnectionError:
        print("Connection error - check your internet connection")
        return None
    except requests.exceptions.Timeout:
        print("Request timed out")
        return None
    except requests.exceptions.JSONDecodeError:
        print("Invalid JSON response")
        return None
    except requests.exceptions.RequestException as err:
        print(f"An error occurred: {err}")
        return None

# Usage example
data = get_json('https://fakestoreapi.com/products')
if data is not None:
    print(f"Retrieved {len(data)} products")
else:
    print("Failed to retrieve data")
```

## Creating and Updating Data with APIs

### POST Requests (Creating New Resources)
```python
import requests

# Create a new product
new_product = {
    'title': 'Test Product',
    'price': 13.5,
    'description': 'A test product',
    'image': 'https://example.com/image.jpg',
    'category': 'electronics'
}

response = requests.post('https://fakestoreapi.com/products', json=new_product)
if response.status_code in [200, 201]:
    created_product = response.json()
    print(f"Created product with ID: {created_product['id']}")
```

### PUT Requests (Replacing Entire Resources)
```python
import requests

# Update an existing product completely
updated_product = {
    'title': 'Updated Product',
    'price': 99.0,
    'description': 'An updated description',
    'image': 'https://example.com/updated-image.jpg',
    'category': 'electronics'
}

response = requests.put('https://fakestoreapi.com/products/7', json=updated_product)
if response.status_code == 200:
    print("Product updated successfully")
    print(response.json())
```

### PATCH Requests (Partial Updates)
```python
import requests

# Update only specific fields
partial_update = {
    'price': 49.0
}

response = requests.patch('https://fakestoreapi.com/products/7', json=partial_update)
if response.status_code == 200:
    print("Product partially updated")
    print(response.json())
```

### DELETE Requests (Removing Resources)
```python
import requests

# Delete a product
response = requests.delete('https://fakestoreapi.com/products/6')
if response.status_code == 200:
    print("Product deleted successfully")
    print(response.json())
```

## Authentication and Security

### Why Authentication is Needed
1. **Rate Limiting**: Prevents abuse of the API
2. **Billing**: Charge for usage
3. **Privacy**: Protect sensitive user data

### Using API Keys and Tokens
```python
import requests
from dotenv import load_dotenv
import os

# Load environment variables from .env file
load_dotenv()

# Get token from environment variable
token = os.getenv('GITHUB_TOKEN')

# Set up headers with authentication
headers = {
    'Authorization': f'Bearer {token}'
}

response = requests.get('https://api.github.com/user', headers=headers)
if response.status_code == 200:
    user_data = response.json()
    print(f"Logged in as: {user_data['login']}")
```

### Environment File Setup (.env)
Create a file named `.env` in your project directory:
```
GITHUB_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Security Best Practices
1. **Never commit secrets to version control**
2. **Use .gitignore to exclude .env files**
3. **Revoke compromised tokens immediately**
4. **Use headers instead of query parameters for sensitive data**

### Example .gitignore Entry
```gitignore
.env
*.env
```

## Complete Working Examples

### Complete API Client Class
```python
import requests
from dotenv import load_dotenv
import os

class APIClient:
    def __init__(self, base_url, token=None):
        self.base_url = base_url.rstrip('/')
        self.session = requests.Session()
        
        if token:
            self.session.headers.update({
                'Authorization': f'Bearer {token}'
            })
    
    def get(self, endpoint, params=None):
        try:
            response = self.session.get(
                f"{self.base_url}/{endpoint}",
                params=params,
                timeout=10
            )
            response.raise_for_status()
            return response.json()
        except requests.exceptions.RequestException as e:
            print(f"Error in GET request: {e}")
            return None
    
    def post(self, endpoint, data):
        try:
            response = self.session.post(
                f"{self.base_url}/{endpoint}",
                json=data,
                timeout=10
            )
            response.raise_for_status()
            return response.json()
        except requests.exceptions.RequestException as e:
            print(f"Error in POST request: {e}")
            return None

# Usage example
# api = APIClient('https://fakestoreapi.com')
# products = api.get('products')
# new_product = api.post('products', {'title': 'New Product', 'price': 29.99})
```

## Key Takeaways

1. **Always check status codes first** before processing data
2. **Use parameters dictionary** instead of building URLs manually
3. **Set timeouts** to prevent hanging requests
4. **Handle exceptions gracefully** with try/except blocks
5. **Keep API keys secure** in environment variables, never in code or version control
6. **Read documentation** thoroughly for endpoint details and expected formats
7. **Test error conditions** to ensure robust code

This comprehensive approach to API integration will help you build reliable applications that can communicate effectively with external services while handling potential failures gracefully.