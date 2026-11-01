# Python FastAPI Lecture Notes

## Introduction to Building APIs with FastAPI

### What is FastAPI?
FastAPI is a modern, fast (high-performance) web framework for building APIs with Python 3.7+ based on standard Python type hints. It's designed specifically for creating APIs and offers several advantages over traditional frameworks like Flask.

### Why Use FastAPI Instead of Flask?
1. **Automatic Data Validation**: FastAPI automatically validates data using Pydantic models
2. **Built-in Documentation**: Automatically generates interactive API documentation
3. **Type Hints**: Uses Python's type hinting system for validation and documentation
4. **Simplified Code**: You return dictionaries directly, FastAPI converts them to JSON automatically

### Basic Setup
To install FastAPI:
```bash
pip install fastapi[all]
```

The `[all]` part installs additional dependencies needed for running the server (like uvicorn).

## Creating Your First API

### Minimal Working Example
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello World"}
```

When you run this with `uvicorn main:app --reload`, FastAPI:
- Automatically converts your Python dictionary to JSON
- Sets proper headers (Content-Type: application/json)
- Provides interactive documentation at `/docs`
- Handles automatic server restarts when code changes

### Key Concepts
- **Route**: A combination of HTTP method and path that maps to a function
- **Function**: The code that executes when a request matches the route
- **Return Value**: FastAPI automatically converts Python objects to JSON responses

## Working with Data Models

### Pydantic Models for Validation
Pydantic is used to define data models that validate input data:

```python
from pydantic import BaseModel

class Book(BaseModel):
    title: str
    author: str
    year: int
```

This model defines what a valid book looks like with type checking.

### Using Models in Routes
```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class Book(BaseModel):
    title: str
    author: str
    year: int

books = [
    {"id": 1, "title": "1984", "author": "George Orwell", "year": 1948},
    {"id": 2, "title": "To Kill a Mockingbird", "author": "Harper Lee", "year": 1960}
]

@app.get("/books")
def get_books():
    return books
```

## CRUD Operations

### GET Requests
#### Getting All Books
```python
@app.get("/books")
def get_books():
    return books
```

#### Getting Specific Book by ID
```python
@app.get("/books/{book_id}")
def get_book(book_id: int):
    for book in books:
        if book["id"] == book_id:
            return book
    raise HTTPException(status_code=404, detail="Book not found")
```

**Important Rule**: Path parameters must be defined in the path using curly braces `{parameter_name}` and must match the function parameter name.

### POST Requests (Creating Resources)
```python
@app.post("/books", status_code=201)
def create_book(book: Book):
    new_id = max([b["id"] for b in books], default=0) + 1
    new_book = {
        "id": new_id,
        "title": book.title,
        "author": book.author,
        "year": book.year
    }
    books.append(new_book)
    return new_book
```

**Key Points**:
- Use `app.post()` instead of `app.get()`
- The parameter `book` with type `Book` tells FastAPI to validate the request body against the Book model
- Status code 201 indicates successful creation
- Pydantic automatically validates that required fields are present and have correct types

### PUT Requests (Updating Resources)
```python
@app.put("/books/{book_id}")
def update_book(book_id: int, updated_book: Book):
    existing = find_book(book_id)
    existing["title"] = updated_book.title
    existing["author"] = updated_book.author
    existing["year"] = updated_book.year
    return existing

def find_book(book_id: int):
    for book in books:
        if book["id"] == book_id:
            return book
    raise HTTPException(status_code=404, detail="Book not found")
```

### DELETE Requests
```python
@app.delete("/books/{book_id}", status_code=204)
def delete_book(book_id: int):
    existing = find_book(book_id)
    books.remove(existing)
```

## Query Parameters

Query parameters are optional values that come after a question mark in the URL:

```python
@app.get("/books")
def get_books(author: str = None):
    if author:
        return [book for book in books if author.lower() in book["author"].lower()]
    return books
```

**Key Points**:
- Query parameters are defined as function parameters not in the path
- They are optional by default (using `=None`)
- FastAPI automatically handles validation and conversion
- In the documentation, query parameters show as "query" type with "string or null" type

## Validation Features

### Automatic Input Validation
FastAPI uses Pydantic for automatic validation:

```python
@app.get("/books/{book_id}")
def get_book(book_id: int):  # The 'int' type hint triggers validation
    pass
```

If someone sends "abc" instead of a number, FastAPI returns HTTP 422 with clear error message.

### Error Handling with HTTP Exceptions
```python
from fastapi import HTTPException

@app.get("/books/{book_id}")
def get_book(book_id: int):
    for book in books:
        if book["id"] == book_id:
            return book
    raise HTTPException(status_code=404, detail="Book not found")
```

**Important Rule**: Always use appropriate status codes:
- 200: Success (default)
- 201: Created 
- 204: No content (for DELETE)
- 404: Not found
- 422: Unprocessable entity (validation errors)

## API Documentation

FastAPI automatically generates documentation at `/docs`:

### Features of Auto-generated Docs:
1. **Interactive Testing**: Test endpoints directly from browser
2. **Code Examples**: Shows curl commands for each endpoint
3. **Request/Response Inspection**: View headers, parameters, and responses
4. **Automatic Updates**: Documentation updates as you add routes

## Working with the Client Side

### Making Requests to Your API
```python
import requests

base_url = "http://127.0.0.1:8000"

# Get all books
response = requests.get(f"{base_url}/books")
print(response.json())

# Create a new book
new_book = {
    "title": "Snow Crash",
    "author": "Neal Stephenson",
    "year": 1992
}
response = requests.post(f"{base_url}/books", json=new_book)
print(response.status_code)
print(response.json())

# Get specific book
response = requests.get(f"{base_url}/books/5")
print(response.json())
```

## Key Technical Concepts

### Type Hints in FastAPI
Type hints serve multiple purposes:
1. **Documentation**: Automatically generates API documentation
2. **Validation**: Validates input data types
3. **IDE Support**: Provides better code completion and error detection

```python
@app.get("/books/{book_id}")
def get_book(book_id: int):  # book_id must be an integer
    pass
```

### Path vs Query Parameters
- **Path parameters**: Part of the URL path (required)
- **Query parameters**: After `?` in URL (optional)

```python
# Path parameter - required
@app.get("/books/{book_id}")

# Query parameter - optional  
@app.get("/books")
def get_books(author: str = None):
```

### Status Codes
| Code | Meaning |
|------|---------|
| 200 | OK (success) |
| 201 | Created (new resource) |
| 204 | No Content (delete successful) |
| 404 | Not Found |
| 422 | Unprocessable Entity (validation error) |

## Complete Example

Here's a complete working example that demonstrates all concepts:

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class Book(BaseModel):
    title: str
    author: str
    year: int

books = [
    {"id": 1, "title": "1984", "author": "George Orwell", "year": 1948},
    {"id": 2, "title": "To Kill a Mockingbird", "author": "Harper Lee", "year": 1960}
]

def find_book(book_id: int):
    for book in books:
        if book["id"] == book_id:
            return book
    raise HTTPException(status_code=404, detail="Book not found")

@app.get("/books")
def get_books(author: str = None):
    if author:
        return [book for book in books if author.lower() in book["author"].lower()]
    return books

@app.get("/books/{book_id}")
def get_book(book_id: int):
    return find_book(book_id)

@app.post("/books", status_code=201)
def create_book(book: Book):
    new_id = max([b["id"] for b in books], default=0) + 1
    new_book = {
        "id": new_id,
        "title": book.title,
        "author": book.author,
        "year": book.year
    }
    books.append(new_book)
    return new_book

@app.put("/books/{book_id}")
def update_book(book_id: int, updated_book: Book):
    existing = find_book(book_id)
    existing["title"] = updated_book.title
    existing["author"] = updated_book.author
    existing["year"] = updated_book.year
    return existing

@app.delete("/books/{book_id}", status_code=204)
def delete_book(book_id: int):
    existing = find_book(book_id)
    books.remove(existing)
```

## Important Notes for Learning

1. **Memory-only Storage**: All data is stored in memory and lost when the server restarts
2. **Development Workflow**: Use `--reload` flag to automatically restart server on code changes
3. **Testing**: Always keep `/docs` open for testing endpoints
4. **Validation**: Pydantic handles all validation automatically based on type hints
5. **Documentation**: FastAPI generates documentation automatically from your code

## Lab Preparation

The lab will include:
1. **Part 1**: Guided practice building your own API (same as today's lesson)
2. **Part 2**: Implement PATCH functionality (partial updates)
3. **Part 3**: Add API key protection
4. **Bonus**: Additional challenges for advanced practice

Remember that FastAPI provides many conveniences:
- Automatic JSON conversion
- Built-in validation and error handling
- Interactive documentation
- Type-based routing and validation
- Status code management
- No need to manually set headers or handle JSON serialization