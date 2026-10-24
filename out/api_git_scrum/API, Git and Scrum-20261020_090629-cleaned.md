# Python FastAPI Lecture Notes

## Installation and Setup

### Installing FastAPI
FastAPI is a modern, fast (high-performance) web framework for building APIs with Python 3.7+ based on standard Python type hints.

**Rule:** Install FastAPI using pip with quotation marks to ensure compatibility across different Python versions.
```bash
pip install "fastapi[all]"
```

**Why it matters:** FastAPI provides automatic validation, documentation generation, and type checking capabilities that make API development more robust and efficient than traditional frameworks like Flask.

### Why FastAPI Over Flask?
FastAPI offers three main advantages:
1. Built specifically for APIs with automatic JSON conversion from Python dictionaries
2. Automatic data validation using Pydantic models
3. Free auto-generated documentation at `/docs` endpoint

## Creating a Basic API

### Minimal API Structure
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello World"}
```

**Rule:** The `@app.get()` decorator creates a route that handles GET requests to the specified path.
**Code Example:**
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello World"}
```

**Gotchas:** 
- FastAPI automatically converts Python dictionaries to JSON responses
- No need to manually set headers or call JSON methods
- The server restarts automatically when files are saved (using `uvicorn main:app --reload`)

### Running the Server
```bash
uvicorn main:app --reload
```

**Why it matters:** This command starts the development server that listens for requests on localhost port 8000 by default.

## API Routes and Data Handling

### GET Routes with Path Parameters
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/books/{book_id}")
def read_book(book_id: int):
    return {"book_id": book_id}
```

**Rule:** Path parameters are defined using curly braces `{parameter_name}` in the route path.
**Code Example:**
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/books/{book_id}")
def read_book(book_id: int):
    return {"book_id": book_id}
```

**Gotchas:** 
- Type hints like `int` provide automatic validation and error handling
- If invalid data is provided (like "abc" instead of a number), FastAPI returns HTTP 422 Unprocessable Entity before the function executes

### GET Routes with Query Parameters
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/books")
def read_books(author: str = None):
    if author:
        return [book for book in books if author.lower() in book["author"].lower()]
    return books
```

**Rule:** Query parameters are function parameters not included in the path, with optional default values.
**Code Example:**
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/books")
def read_books(author: str = None):
    if author:
        return [book for book in books if author.lower() in book["author"].lower()]
    return books
```

**Why it matters:** Query parameters allow flexible filtering of data without changing the URL structure.

### Error Handling with HTTP Exceptions
```python
from fastapi import FastAPI, HTTPException

app = FastAPI()

@app.get("/books/{book_id}")
def read_book(book_id: int):
    for book in books:
        if book["id"] == book_id:
            return book
    raise HTTPException(status_code=404, detail="Book not found")
```

**Rule:** Use `HTTPException` to return specific HTTP status codes when errors occur.
**Code Example:**
```python
from fastapi import FastAPI, HTTPException

app = FastAPI()

@app.get("/books/{book_id}")
def read_book(book_id: int):
    for book in books:
        if book["id"] == book_id:
            return book
    raise HTTPException(status_code=404, detail="Book not found")
```

**Gotchas:** 
- HTTP 404 is the standard status code for "not found" responses
- The exception provides both a status code and a clear error message
- Client applications can check status codes to understand what went wrong

## Data Validation with Pydantic Models

### Creating Data Models
```python
from pydantic import BaseModel

class Book(BaseModel):
    title: str
    author: str
    year: int
```

**Rule:** Pydantic models define the structure and validation rules for data.
**Code Example:**
```python
from pydantic import BaseModel

class Book(BaseModel):
    title: str
    author: str
    year: int
```

**Why it matters:** Pydantic automatically validates incoming data and provides clear error messages when data doesn't match the expected structure.

### POST Routes with Data Validation
```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class Book(BaseModel):
    title: str
    author: str
    year: int

@app.post("/books", status_code=201)
def create_book(book: Book):
    new_id = max([b["id"] for b in books], default=0) + 1
    new_book = {"id": new_id, **book.dict()}
    books.append(new_book)
    return new_book
```

**Rule:** Function parameters with Pydantic model types automatically validate request body data.
**Code Example:**
```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class Book(BaseModel):
    title: str
    author: str
    year: int

@app.post("/books", status_code=201)
def create_book(book: Book):
    new_id = max([b["id"] for b in books], default=0) + 1
    new_book = {"id": new_id, **book.dict()}
    books.append(new_book)
    return new_book
```

**Gotchas:** 
- Pydantic automatically converts string numbers to integers when possible
- Validation happens before the function executes
- Invalid data returns HTTP 422 Unprocessable Entity

## CRUD Operations

### Complete Book API Implementation
```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class Book(BaseModel):
    title: str
    author: str
    year: int

books = [
    {"id": 1, "title": "The Great Gatsby", "author": "F. Scott Fitzgerald", "year": 1925},
    {"id": 2, "title": "To Kill a Mockingbird", "author": "Harper Lee", "year": 1960}
]

def find_book(book_id: int):
    for book in books:
        if book["id"] == book_id:
            return book
    raise HTTPException(status_code=404, detail="Book not found")

@app.get("/books")
def read_books(author: str = None):
    if author:
        return [book for book in books if author.lower() in book["author"].lower()]
    return books

@app.get("/books/{book_id}")
def read_book(book_id: int):
    return find_book(book_id)

@app.post("/books", status_code=201)
def create_book(book: Book):
    new_id = max([b["id"] for b in books], default=0) + 1
    new_book = {"id": new_id, **book.dict()}
    books.append(new_book)
    return new_book

@app.put("/books/{book_id}")
def update_book(book_id: int, book: Book):
    existing = find_book(book_id)
    existing["title"] = book.title
    existing["author"] = book.author
    existing["year"] = book.year
    return existing

@app.delete("/books/{book_id}", status_code=204)
def delete_book(book_id: int):
    existing = find_book(book_id)
    books.remove(existing)
```

### PUT Route for Updating Resources
```python
@app.put("/books/{book_id}")
def update_book(book_id: int, book: Book):
    existing = find_book(book_id)
    existing["title"] = book.title
    existing["author"] = book.author
    existing["year"] = book.year
    return existing
```

**Rule:** PUT routes replace entire resources and require the complete updated data.
**Why it matters:** PUT operations are idempotent - multiple identical requests should have the same effect.

### DELETE Route for Removing Resources
```python
@app.delete("/books/{book_id}", status_code=204)
def delete_book(book_id: int):
    existing = find_book(book_id)
    books.remove(existing)
```

**Rule:** DELETE operations return HTTP 204 No Content when successful.
**Why it matters:** The 204 status code indicates success but no response body, which is appropriate for deletion operations.

## API Testing and Documentation

### Built-in Documentation
FastAPI automatically generates documentation at `/docs` endpoint:
- Shows all endpoints with methods
- Allows testing endpoints directly in browser
- Displays request format and expected responses
- Provides curl command examples

**Why it matters:** This auto-generated documentation saves development time and provides clear API specifications.

### Status Codes Reference
- **200**: Default successful response
- **201**: Created (POST requests)
- **204**: No content (DELETE requests)
- **404**: Not found
- **422**: Unprocessable entity (validation errors)

## Client-Side API Interaction

### Using API from Python Code
```python
import requests

base_url = "http://127.0.0.1:8000"

# Get all books
response = requests.get(f"{base_url}/books")
books = response.json()

# Add a new book
new_book = {
    "title": "Dune Messiah",
    "author": "Frank Herbert",
    "year": 1969
}
response = requests.post(f"{base_url}/books", json=new_book)
created_book = response.json()
```

**Rule:** When using external APIs, ensure proper error handling for different status codes.
**Why it matters:** Client applications need to handle both successful responses and various error conditions appropriately.

## Key Concepts Summary

### Core Principles
1. **Routes are methods + paths connected to functions**
2. **Type hints provide real validation work**
3. **Path parameters with `int` type give automatic validation**
4. **Pydantic classes describe request body structure**
5. **Raise HTTP exceptions for error conditions**
6. **Set status codes when they should be other than 200**

### Memory Limitations
**Rule:** All data is stored in memory only, so server restarts lose all changes.
**Why it matters:** This limitation explains why real applications require databases or persistent storage solutions.

### Lab Preparation
The lab includes:
1. Guided implementation of CRUD operations (part 1)
2. Practice implementing PATCH operations (part 2)  
3. API key protection implementation (part 3 - bonus)

## Important Notes

### Development Environment
- Keep the docs page (`/docs`) open during development for easy testing
- Use `uvicorn main:app --reload` for automatic server restarts
- All code changes are immediately reflected in the running server

### Production Considerations
- Real applications require persistent storage (databases, files)
- API security (authentication, rate limiting) needs additional implementation
- Proper error handling and logging should be implemented
- Performance optimization may be needed for production deployment