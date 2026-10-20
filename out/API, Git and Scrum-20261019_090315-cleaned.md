# Python API Interaction

## What is an API?

An Application Programming Interface (API) is a set of rules that allows programs to communicate with each other. It's essentially a contract between two software systems that defines how they should interact.

**Key Components:**
- **Client**: The program that wants something
- **Server**: The system that provides the data or service
- **API**: The interface that handles requests and responses

## Installing Required Libraries

```bash
pip install requests python-dotenv
```

## Making HTTP Requests with Requests Library

### Basic GET Request

```python
import requests

response = requests.get('https://fakestoreapi.com/products')
print(response)  # Returns a Response object
print(response.status_code)  # Returns the HTTP status code (200 for success)
```

### Understanding Response Objects

When you make an API call, `requests.get()` returns a Response object containing:
1. **Status Code**: Indicates if request succeeded (2xx), client error (4xx), or server error (5xx)
2. **Headers**: Metadata about the response
3. **Content/Body**: The actual data returned by the API

### Extracting Data from Responses

```python
import requests

response = requests.get('https://fakestoreapi.com/products')
data = response.json()  # Converts JSON text to Python data structures

print(type(data))  # <class 'list'>
print(len(data))   # 20 (number of products)
print(data[0])     # First product dictionary
```

### Working with API Data Structure

```python
import requests

response = requests.get('https://fakestoreapi.com/products')
products = response.json()

# Loop through all products
for product in products[:5]:  # First 5 products
    print(f"ID: {product['id']}, Title: {product['title']}, Price: ${product['price']}")

# Access specific product by ID
specific_product = requests.get('https://fakestoreapi.com/products/15')
product_data = specific_product.json()
print(product_data['title'])  # Gets title of product with ID 15

# Access nested data (like ratings)
rating = product_data['rating']
print(f"Average rating: {rating['rate']}")
```

## API Request Anatomy

### Four Essential Components:
1. **Endpoint**: The URL address where the request is sent
2. **Method**: What action to perform (GET, POST, PUT, PATCH, DELETE)
3. **Headers**: Extra information about the request
4. **Body**: Data to send with the request (for POST, PUT, PATCH)

### Endpoint Structure:
```
https://api.example.com/users/octocat/repos?sort=pushed&per_page=5
```

**Components:**
- Root: `https://api.example.com`
- Path: `/users/octocat/repos` 
- Query Parameters: `?sort=pushed&per_page=5`

### HTTP Methods:
1. **GET**: Read data (no changes to server)
2. **POST**: Create new resource
3. **PUT**: Replace entire existing resource
4. **PATCH**: Update specific parts of a resource
5. **DELETE**: Remove a resource

## Handling API Responses and Errors

### Checking Status Codes:
```python
import requests

response = requests.get('https://fakestoreapi.com/products/1000')  # Non-existent product

if response.status_code == 200:
    data = response.json()
    print("Success!")
elif response.status_code == 404:
    print("Product not found")
else:
    print(f"Error: {response.status_code}")
```

### Using `raise_for_status()` for Error Handling:

```python
import requests

try:
    response = requests.get('https://fakestoreapi.com/products/1000')
    response.raise_for_status()  # Raises exception for bad status codes
    data = response.json()
    print(data)
except requests.exceptions.HTTPError as error:
    print(f"HTTP Error: {error.response.status_code}")
except requests.exceptions.ConnectionError:
    print("Connection Error")
except requests.exceptions.Timeout:
    print("Timeout Error")
except requests.exceptions.RequestException:
    print("Request Error")
```

### Creating a Robust API Function:

```python
import requests

def get_json(url, params=None, headers=None, timeout=5):
    """
    Safely fetch JSON data from an API with proper error handling.
    
    Args:
        url (str): The API endpoint URL
        params (dict): Query parameters for the request
        headers (dict): HTTP headers to send with request
        timeout (int): Request timeout in seconds
    
    Returns:
        dict or None: JSON data if successful, None if error occurred
    """
    try:
        response = requests.get(
            url, 
            params=params, 
            headers=headers, 
            timeout=timeout
        )
        response.raise_for_status()
        return response.json()
    except requests.exceptions.HTTPError as error:
        print(f"Server Error: {error.response.status_code}")
        return None
    except requests.exceptions.ConnectionError:
        print("Connection Error")
        return None
    except requests.exceptions.Timeout:
        print("Timeout Error")
        return None
    except requests.exceptions.RequestException:
        print("Request Error")
        return None

# Usage example:
data = get_json('https://fakestoreapi.com/products')
if data is not None:
    print(f"Retrieved {len(data)} products")
else:
    print("Failed to retrieve data")
```

## Working with Query Parameters

### Method 1: Direct URL Construction
```python
import requests

response = requests.get('https://fakestoreapi.com/products?limit=5&sort=desc')
data = response.json()
print(len(data))  # 5 items
```

### Method 2: Using `params` Parameter (Recommended)
```python
import requests

params = {
    'limit': 5,
    'sort': 'desc'
}

response = requests.get('https://fakestoreapi.com/products', params=params)
data = response.json()
print(response.url)  # Shows the constructed URL with parameters
```

## Working with HTTP Headers

### Sending Custom Headers:
```python
import requests

headers = {
    'Accept': 'application/json',
    'User-Agent': 'MyPythonApp/1.0'
}

response = requests.get('https://fakestoreapi.com/products', headers=headers)
print(response.status_code)  # Should be 200
```

### Understanding Response Headers:
```python
import requests

response = requests.get('https://fakestoreapi.com/products')
print(response.headers['content-type'])  # 'application/json'
print(response.headers['server'])       # Server information
```

## Creating, Updating, and Deleting Resources

### POST (Create):
```python
import requests

new_product = {
    "title": "Test Product",
    "price": 13.5,
    "description": "A test product",
    "image": "https://example.com/image.jpg",
    "category": "electronics"
}

response = requests.post('https://fakestoreapi.com/products', json=new_product)
print(response.status_code)  # Should be 201 (Created)
created_product = response.json()
print(created_product['id'])  # New ID assigned by server
```

### PUT (Replace):
```python
import requests

updated_product = {
    "title": "Updated Product",
    "price": 99.0,
    "description": "Updated description",
    "image": "https://example.com/new-image.jpg",
    "category": "electronics"
}

response = requests.put('https://fakestoreapi.com/products/7', json=updated_product)
print(response.status_code)  # Should be 200
```

### PATCH (Update Specific Fields):
```python
import requests

partial_update = {
    "price": 49.0
}

response = requests.patch('https://fakestoreapi.com/products/7', json=partial_update)
print(response.status_code)  # Should be 200
```

### DELETE:
```python
import requests

response = requests.delete('https://fakestoreapi.com/products/6')
print(response.status_code)  # Should be 200
```

## Authentication and API Keys

### Understanding Status Codes:
- **401 Unauthorized**: Server doesn't know who you are
- **403 Forbidden**: Server knows who you are but you don't have permission

### Rate Limiting Example:
```python
import requests

response = requests.get('https://api.github.com/user')
print(response.headers['x-ratelimit-limit'])   # How many requests allowed per hour
print(response.headers['x-ratelimit-remaining'])  # How many left
```

## Secure API Key Management

### Using Environment Variables:

**Create `.env` file:**
```
GITHUB_TOKEN=ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

**Python code to read the token:**
```python
import requests
from dotenv import load_dotenv
import os

load_dotenv()  # Load environment variables from .env file

token = os.getenv('GITHUB_TOKEN')
headers = {
    'Authorization': f'Bearer {token}'
}

response = requests.get('https://api.github.com/user', headers=headers)
print(response.status_code)  # Should be 200 if token is valid
```

### Security Best Practices:
1. Never store API keys in source code
2. Never put keys in URLs (they appear in logs)
3. Use `.gitignore` to exclude environment files
4. Revoke compromised keys immediately
5. Use different tokens for different environments

## Key Concepts Summary

### Important Rules:
- Always check status codes first
- Set timeouts to prevent hanging requests
- Handle different types of errors appropriately
- Use `params` dictionary instead of manually building URLs
- Store sensitive information in environment variables
- Read API documentation thoroughly

### Common Status Codes:
- **200 OK**: Successful request
- **201 Created**: Resource created successfully
- **404 Not Found**: Requested resource doesn't exist
- **401 Unauthorized**: Authentication required
- **403 Forbidden**: Insufficient permissions
- **422 Unprocessable Entity**: Data validation error
- **429 Too Many Requests**: Rate limit exceeded
- **500 Internal Server Error**: Server problem

### API Anatomy:
```
[Endpoint] [Method] [Headers] [Body]
https://api.example.com/users GET Content-Type: application/json {}
```

This comprehensive approach to API interaction ensures your Python programs can reliably communicate with external services while handling errors gracefully and maintaining security best practices.