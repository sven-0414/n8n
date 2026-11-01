# NoSQL and MongoDB

## What is NoSQL?

### Definition
NoSQL stands for "Not Only SQL" and refers to a family of database systems that differ from traditional relational SQL databases. These databases are designed to handle data in flexible, non-tabular formats and can scale across multiple servers more easily than SQL databases.

### Key Characteristics
- **Flexible Schema**: Unlike SQL databases where you must define all columns upfront, NoSQL allows documents within the same collection to have different structures
- **Horizontal Scaling**: Built to distribute data across many servers from the start
- **Document-Oriented**: Data is stored as documents (similar to Python dictionaries) rather than rows in tables

### Why Use NoSQL?
1. **Changing Data Structure**: When your data schema changes frequently, NoSQL adapts more easily
2. **Object-Oriented Programming**: NoSQL works naturally with Python dictionaries and objects
3. **Large Scale Data**: Handles massive amounts of data that don't fit on a single computer
4. **Performance**: Often faster for specific operations like read-heavy workloads

### Code Example
```python
# This is how you might see data in NoSQL vs SQL
# NoSQL document structure (similar to Python dict):
product = {
    "name": "hoodie black",
    "price": 599,
    "tags": ["winter", "casual", "black"],
    "details": {
        "color": "black",
        "material": "cotton"
    }
}
```

### Why It Matters
NoSQL databases provide a different approach to data storage that's particularly useful for modern web applications where data structures change frequently and scalability is crucial. Understanding both SQL and NoSQL gives you more tools to choose the right database for your specific needs.

## MongoDB Overview

### Definition
MongoDB is a popular document-based NoSQL database that stores data as documents in collections. It's designed to handle large volumes of data and scale across multiple servers.

### Key Features
- **Document Database**: Stores data in flexible, JSON-like documents
- **Schema Flexibility**: Documents within the same collection can have different fields
- **Built for Scaling**: Designed to work across multiple servers from the beginning
- **Popular Usage**: Widely used in job ads and modern applications

### Installation and Setup
MongoDB can be installed locally on your computer for free. The database runs as a background service (like a kitchen) that Python programs communicate with through libraries like PyMongo.

### Code Example
```python
# MongoDB connection example
from pymongo import MongoClient

client = MongoClient("mongodb://localhost:27017/")
db = client["webshop"]
```

### Why It Matters
MongoDB is the most widely used NoSQL database and understanding it gives you access to many modern application architectures that require flexible, scalable data storage solutions.

## MongoDB Terminology

### Key Terms
| SQL Term | MongoDB Term |
|----------|--------------|
| Database | Database |
| Table | Collection |
| Row | Document |
| Column | Field |
| Primary Key | _id (always called underscore ID) |

### Document Structure
- Every document in MongoDB must have a unique `_id` field (primary key equivalent)
- Documents can contain nested objects and arrays
- Data types include strings, numbers, booleans, dates, arrays, and objects

### Code Example
```python
# A MongoDB document structure example:
{
    "_id": ObjectId("507f1f77bcf86cd799439011"),
    "name": "hoodie black",
    "price": 599,
    "category": "clothing",
    "tags": ["winter", "casual", "black"],
    "details": {
        "color": "black",
        "material": "cotton"
    }
}
```

### Why It Matters
Understanding these terms is crucial because they form the foundation of how you'll interact with MongoDB throughout this course. The terminology differences from SQL can be confusing at first but become intuitive with practice.

## Data Types in MongoDB

### Supported Data Types
- **Strings**: Text data (like Python strings)
- **Numbers**: Integers, floats (stored as double in MongoDB)
- **Booleans**: True/False values
- **Dates**: Date and time information
- **Arrays**: Lists of values
- **Objects**: Nested dictionaries
- **Null**: Represents missing or empty values

### PyMongo Translation Rules
PyMongo automatically translates between Python types and MongoDB types:
- Python `str` → MongoDB `string`
- Python `int` → MongoDB `int`
- Python `float` → MongoDB `double`
- Python `list` → MongoDB `array`
- Python `dict` → MongoDB `object`
- Python `datetime` → MongoDB `date`
- Python `None` → MongoDB `null`

### Code Example
```python
# Python to MongoDB type conversion examples
import datetime

# Python data types
python_data = {
    "name": "Anna",           # str → string
    "age": 30,                # int → int
    "height": 5.6,            # float → double
    "active": True,           # bool → boolean
    "birth_date": datetime.datetime.now(),  # datetime → date
    "hobbies": ["reading", "swimming"],     # list → array
    "address": {              # dict → object
        "street": "123 Main St",
        "city": "Uppsala"
    },
    "middle_name": None       # None → null
}
```

### Gotchas and Edge Cases
- **Case Sensitivity**: In Python, `true` is not the same as `True` (lowercase vs uppercase)
- **JSON Format**: MongoDB uses JSON format which requires lowercase boolean values
- **Automatic Type Conversion**: PyMongo handles most conversions automatically but be aware of potential issues

## Database Structure and Collections

### Flexible Schema Concept
Unlike SQL databases where every row must have the same columns, MongoDB allows documents in the same collection to have different structures. This flexibility is called "flexible schema."

### Code Example
```python
# Two products in the same collection with different fields
product1 = {
    "name": "sneakers",
    "price": 899,
    "size": 42,  # Only sneakers have size
    "_id": 1
}

product2 = {
    "name": "t-shirt",
    "price": 199,
    "color": "blue",  # T-shirts don't have size
    "_id": 2
}
```

### Why It Matters
This flexibility allows you to store data that naturally changes over time without having to modify database schema or create complex join operations.

## The _id Field

### Definition and Properties
- Every document in MongoDB must have a unique `_id` field
- It works like a primary key in SQL databases
- If you don't provide an `_id`, MongoDB automatically creates one called an ObjectID
- ObjectIDs are globally unique across all computers and time

### ObjectID Structure
ObjectIDs contain:
1. Timestamp (4 bytes)
2. Machine identifier (3 bytes)
3. Process ID (2 bytes)
4. Counter (3 bytes)

This ensures no two ObjectIDs will ever collide, even when created simultaneously on different computers.

### Code Example
```python
# Manual ID assignment vs automatic creation
manual_product = {
    "_id": 1,
    "name": "hoodie black",
    "price": 599
}

auto_product = {
    "name": "scarf",
    "price": 299
}
# MongoDB automatically adds: "_id": ObjectId("...")

# Both are valid, but automatic creates ObjectID
```

### Why It Matters
Understanding the `_id` field is crucial because it's how documents are uniquely identified. Missing or duplicate IDs will cause errors in your database operations.

## PyMongo Library Basics

### Installation and Setup
```bash
pip install pymongo
```

### Basic Connection Pattern
```python
from pymongo import MongoClient

# Connect to MongoDB server
client = MongoClient("mongodb://localhost:27017/")

# Access database
db = client["webshop"]

# Access collection
collection = db["customers"]
```

### Code Example
```python
from pymongo import MongoClient

# Establish connection
client = MongoClient("mongodb://localhost:27017/")
db = client["webshop"]

# Find one document
customer = db.customers.find_one()
print(customer)  # Returns a Python dictionary

# Find multiple documents
customers = db.customers.find()
for customer in customers:
    print(customer)
```

### Why It Matters
PyMongo makes MongoDB feel like regular Python code. You get back standard Python dictionaries instead of special cursor objects, making it intuitive for Python developers.

## Core Operations

### Find One Document
```python
# Returns a single document or None if not found
customer = db.customers.find_one()
print(customer)  # Direct Python dictionary
```

### Find Multiple Documents
```python
# Returns a cursor - must iterate over results
customers = db.customers.find()  # All customers
for customer in customers:
    print(customer)

# With filter
customers = db.customers.find({"city": "Uppsala"})
for customer in customers:
    print(customer)
```

### Insert One Document
```python
new_customer = {
    "first_name": "Noah",
    "last_name": "Svensson",
    "email": "noah@gmail.com"
}

result = db.customers.insert_one(new_customer)
print(result.inserted_id)  # Shows the new document's ID
```

### Insert Multiple Documents
```python
new_customers = [
    {
        "first_name": "Olivia",
        "last_name": "Johnson",
        "email": "olivia@gmail.com"
    },
    {
        "first_name": "Liam",
        "last_name": "Williams",
        "email": "liam@gmail.com"
    }
]

result = db.customers.insert_many(new_customers)
print(result.inserted_ids)  # Shows all new document IDs
```

### Count Documents
```python
count = db.products.count_documents({})
print(f"Total products: {count}")

# Count with filter
shoe_count = db.products.count_documents({"category": "shoes"})
print(f"Shoes: {shoe_count}")
```

### Why It Matters
These basic operations form the foundation of working with MongoDB. Understanding how they work and their differences from SQL operations is essential for effective database programming.

## Filters in MongoDB

### Filter Syntax
Filters are Python dictionaries that specify conditions for finding documents:
- Empty dictionary `{}` returns all documents
- Field-value pairs match documents where the field equals that value
- Multiple fields must match (AND operation)

### Code Examples
```python
# Find all documents
all_customers = db.customers.find({})

# Find by single field
customers_in_uppsala = db.customers.find({"city": "Uppsala"})

# Find by multiple fields (AND)
customers_in_uppsala_with_newsletter = db.customers.find({
    "city": "Uppsala",
    "newsletter": True
})

# Find with different operators
# Note: MongoDB supports various comparison operators in queries
```

### Case Sensitivity
MongoDB filters are case-sensitive like SQL:
```python
# This will return no results (case mismatch)
customers = db.customers.find({"city": "uppsala"})  # Wrong capitalization

# This will work correctly
customers = db.customers.find({"city": "Uppsala"})  # Correct capitalization
```

### Why It Matters
Filtering is one of the most important aspects of database operations. Understanding how to properly structure filters helps you retrieve exactly the data you need.

## Document Embedding vs Normalization

### Concept
In SQL, related data often requires joining multiple tables. In MongoDB, related data can be stored together within a single document (embedding).

### Example Comparison
```python
# SQL approach - requires joins
# Orders table
{
    "order_id": 1,
    "customer_id": 101
}

# Order_items table  
{
    "item_id": 1,
    "order_id": 1,
    "product_name": "hoodie",
    "price": 599
}

# MongoDB approach - embedded
{
    "_id": 1,
    "customer_id": 101,
    "items": [
        {
            "product_name": "hoodie",
            "price": 599,
            "quantity": 2
        }
    ]
}
```

### Why It Matters
This approach eliminates the need for complex JOIN operations and can significantly improve read performance for related data.

## Working with Collections

### Collection Creation
Collections are created automatically when you insert the first document:
```python
# This creates the 'customers' collection automatically
db.customers.insert_one({"name": "Anna"})
```

### Why It Matters
This automatic creation makes MongoDB very easy to start working with, but it also means typos in collection names won't throw errors - they'll create new empty collections instead.

## Error Handling and Best Practices

### Common Issues
1. **Typo in collection name**: Creates new collection instead of error
2. **Missing ID field**: Automatically creates ObjectID
3. **Case sensitivity**: Filters are case-sensitive like SQL
4. **No rollback capability**: Operations happen immediately

### Code Example for Safe Access
```python
# Safely access potentially missing fields
customer = db.customers.find_one({"first_name": "Anna"})
if customer:
    city = customer.get("city", "Unknown")  # Returns "Unknown" if city missing
    print(f"City: {city}")
```

### Why It Matters
Understanding these potential issues helps prevent common mistakes and makes your database code more robust.

## Database Management with Compass

### MongoDB Compass Overview
MongoDB Compass is a GUI tool for exploring and managing MongoDB databases:
- Visual interface to view data
- Easy connection management
- Database and collection browsing
- Data manipulation capabilities

### Connection Process
1. Download MongoDB Compass from official website
2. Connect using the address: `mongodb://localhost:27017/`
3. Refresh to see available databases
4. Navigate through collections and documents

### Why It Matters
Compass provides a visual way to understand your data structure and verify that your Python operations are working correctly.

## Practical Implementation

### Complete Working Example
```python
from pymongo import MongoClient
import pprint

# Connect to database
client = MongoClient("mongodb://localhost:27017/")
db = client["webshop"]

# Insert a new product
new_product = {
    "name": "gloves",
    "category": "accessories",
    "price": 199,
    "stock": 20
}

result = db.products.insert_one(new_product)
print(f"Inserted product with ID: {result.inserted_id}")

# Find the inserted product
product = db.products.find_one({"name": "gloves"})
pprint.pprint(product)  # Pretty print for better readability

# Count documents
count = db.products.count_documents({})
print(f"Total products: {count}")
```

### Why This Matters
This example demonstrates the complete workflow from connection through insertion, retrieval, and counting - all fundamental operations you'll use regularly when working with MongoDB.