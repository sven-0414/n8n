# Python NoSQL and MongoDB Study Notes

## Introduction to NoSQL Databases

### What is NoSQL?
NoSQL stands for "Not Only SQL" and refers to a family of databases that are different from traditional SQL databases. It's not that SQL is bad - it's just another tool in the toolbox. NoSQL databases are designed to handle data differently, often with more flexibility than traditional relational databases.

### Why Use NoSQL?
There are several common reasons to use NoSQL databases:
1. **Data changes shape frequently** - New product types with new properties every week
2. **Data naturally fits into objects** - Python dictionaries map easily to NoSQL documents
3. **Need for horizontal scaling** - NoSQL databases can spread data across many servers
4. **Complex relationships** - Instead of joins, data that belongs together stays together

### Key Differences from SQL
| SQL | NoSQL |
|-----|-------|
| Tables with fixed columns | Documents with flexible schema |
| Join operations to connect data | Embedded documents store related data together |
| Predefined structure | Schema-less by default |
| Primary keys in tables | Unique `_id` field in documents |

## MongoDB Overview

### What is MongoDB?
MongoDB is a document database, one of the most popular NoSQL databases. It's built to handle large amounts of data and can spread across multiple servers. The name comes from "humongous" - referring to its ability to handle massive datasets.

### Key Concepts
- **Database**: Same as SQL - contains collections
- **Collection**: Similar to SQL tables but more flexible
- **Document**: Similar to SQL rows but stored as JSON-like documents
- **Field**: Similar to SQL columns
- **Primary Key**: Always called `_id`

### Document Structure
MongoDB documents look like Python dictionaries:
```python
{
    "_id": ObjectId("..."),
    "name": "hoodie black",
    "category": "clothing",
    "price": 599,
    "tags": ["winter", "casual", "black"],
    "details": {
        "color": "black",
        "material": "cotton"
    }
}
```

## PyMongo Library

### Installation
To use MongoDB with Python, you need to install the PyMongo library:
```bash
pip install pymongo
```

### Basic Connection Setup
```python
from pymongo import MongoClient

# Connect to MongoDB server
client = MongoClient("mongodb://localhost:27017/")

# Access database
db = client["webshop"]

# Access collection
collection = db["products"]
```

## Core Operations

### 1. Finding Documents

#### Find One Document
```python
# Find first document that matches criteria
product = db.products.find_one({"name": "Beanie"})
print(product)  # Returns a Python dictionary
```

#### Find Many Documents
```python
# Find all documents matching criteria
customers = db.customers.find({"city": "Uppsala"})
for customer in customers:
    print(customer["first_name"])
```

### 2. Inserting Documents

#### Insert One Document
```python
# Insert a single document
new_product = {
    "name": "gloves",
    "category": "accessories",
    "price": 199,
    "stock": 20
}

result = db.products.insert_one(new_product)
print(result.inserted_id)  # Shows the generated _id
```

#### Insert Multiple Documents
```python
# Insert multiple documents at once
products = [
    {
        "name": "scarf",
        "category": "accessories",
        "price": 149,
        "stock": 15,
        "tags": ["winter", "casual"],
        "details": {"color": "red"}
    },
    {
        "name": "gloves",
        "category": "accessories",
        "price": 199,
        "stock": 20,
        "tags": ["winter", "casual"],
        "details": {"color": "black"}
    }
]

result = db.products.insert_many(products)
print(result.inserted_ids)  # Shows all generated _ids
```

### 3. Counting Documents
```python
# Count documents in a collection
count = db.products.count_documents({})
print(f"Total products: {count}")

# Count documents with filter
shoe_count = db.products.count_documents({"category": "shoes"})
print(f"Shoe products: {shoe_count}")
```

### 4. Deleting Documents
```python
# Delete one document
result = db.products.delete_one({"name": "gloves"})
print(f"Deleted {result.deleted_count} documents")

# Delete multiple documents
result = db.products.delete_many({"category": "shoes"})
print(f"Deleted {result.deleted_count} documents")
```

## Data Types in MongoDB

MongoDB supports various data types that map to Python equivalents:
- **String**: Text values (Python str)
- **Number**: Integers and floating-point numbers (Python int/float)
- **Boolean**: True or false (Python True/False)
- **Date**: Date and time values (Python datetime)
- **Array**: Lists of values (Python list)
- **Object**: Nested documents (Python dict)
- **Null**: Missing or empty values (Python None)

### Important Note About Data Types
In Python, you must use correct capitalization:
```python
# Correct - Python uses capital T/F and None
{"active": True, "name": "John", "email": None}

# Incorrect - JSON uses lowercase true/false/null
# {"active": true, "name": "John", "email": null}
```

## Working with Filters

Filters in MongoDB are Python dictionaries that specify search criteria:
```python
# Find all customers from Uppsala
customers = db.customers.find({"city": "Uppsala"})

# Find products with price > 100 and category = 'shoes'
expensive_shoes = db.products.find({
    "price": {"$gt": 100},
    "category": "shoes"
})

# Find documents where field doesn't exist
no_email = db.customers.find({"email": {"$exists": False}})
```

## Flexible Schema

### What is Flexible Schema?
In MongoDB, documents in the same collection can have different structures. This contrasts with SQL where all rows must have the same columns.

### Example of Flexible Schema
```python
# These are both valid products in the same collection
product1 = {
    "name": "sneakers",
    "category": "shoes",
    "price": 899,
    "size": 42  # Only sneakers have size
}

product2 = {
    "name": "t-shirt",
    "category": "clothing",
    "price": 199
    # No size field - that's okay!
}
```

### Important Considerations
- **No automatic validation**: MongoDB doesn't check data consistency by default
- **Typo risk**: Writing "pric" instead of "price" will create a new field
- **Manual enforcement**: You need to implement validation rules yourself

## The _id Field

### Primary Key in MongoDB
Every document must have an `_id` field, similar to primary keys in SQL:
```python
# Manual ID assignment (recommended for comparison with SQL)
product = {
    "_id": 1,
    "name": "hoodie black",
    "price": 599
}

# Automatic ID generation
product = {
    "name": "hoodie black",
    "price": 599
}
# MongoDB automatically creates an ObjectId for _id
```

### Object IDs
When no `_id` is provided, MongoDB generates an ObjectId:
- **Format**: 24-character hexadecimal string
- **Structure**: Built from timestamp + unique parts
- **Uniqueness**: Guaranteed to be unique across computers
- **Use case**: Perfect for distributed systems

## Practical Examples and Code Patterns

### Basic Data Access Pattern
```python
from pymongo import MongoClient
import pprint  # For pretty printing dictionaries

# Connect to database
client = MongoClient("mongodb://localhost:27017/")
db = client["webshop"]

# Find one document
product = db.products.find_one({"name": "Beanie"})
print(product)

# Loop through multiple documents
for customer in db.customers.find({"city": "Uppsala"}):
    print(customer["first_name"])

# Insert new document
new_customer = {
    "first_name": "Noah",
    "last_name": "Svensson",
    "email": "noah@gmail.com",
    "city": "Stockholm"
}

result = db.customers.insert_one(new_customer)
print(f"Inserted customer with ID: {result.inserted_id}")
```

### Safe Dictionary Access
When field might not exist:
```python
# This would cause KeyError if city field doesn't exist
# print(customer["city"])

# Safe approach using .get()
city = customer.get("city", "Unknown")
print(f"City: {city}")
```

## Common Gotchas and Best Practices

### 1. Case Sensitivity
MongoDB filters are case-sensitive like SQL:
```python
# This finds nothing because of capitalization
db.customers.find({"city": "Uppsala"})  # Finds customers from Uppsala
db.customers.find({"city": "uppsala"})  # Finds nothing

# If you want to ignore case, use regex:
db.customers.find({"city": {"$regex": "^uppsala$", "$options": "i"}})
```

### 2. Missing Fields and Null Values
```python
# Document without city field
customer = {"first_name": "Lucas"}

# This will cause KeyError
# print(customer["city"])

# Safe approach
city = customer.get("city", "Unknown")
print(f"City: {city}")  # Prints: City: Unknown
```

### 3. Schema Validation
Since MongoDB doesn't enforce schema by default, be careful about typos:
```python
# This creates a new field called "pric" instead of "price"
product = {"name": "hat", "pric": 49}  # Notice the typo

# The document will have both fields!
```

### 4. Document Size Limits
MongoDB has a 16MB limit per document, so avoid creating overly large documents.

## Database Operations Summary

| SQL Operation | MongoDB Equivalent |
|---------------|-------------------|
| SELECT * FROM customers WHERE city = 'Uppsala' | db.customers.find({"city": "Uppsala"}) |
| INSERT INTO customers VALUES (...) | db.customers.insert_one(document) |
| DELETE FROM customers WHERE name = 'John' | db.customers.delete_one({"name": "John"}) |
| SELECT COUNT(*) FROM products | db.products.count_documents({}) |

## Complete Working Example

```python
from pymongo import MongoClient
import pprint

# Connect to MongoDB
client = MongoClient("mongodb://localhost:27017/")
db = client["webshop"]

# Check collections
print("Database collections:", db.list_collection_names())

# Find a specific product
product = db.products.find_one({"name": "Beanie"})
print("Product found:")
pprint.pprint(product)

# Count documents in each collection
for collection_name in db.list_collection_names():
    count = db[collection_name].count_documents({})
    print(f"{collection_name}: {count} documents")

# Find customers from Uppsala
print("\nCustomers from Uppsala:")
for customer in db.customers.find({"city": "Uppsala"}):
    print(f"- {customer['first_name']} {customer['last_name']}")

# Insert new product
new_product = {
    "name": "sweater",
    "category": "clothing",
    "price": 299,
    "stock": 15
}

result = db.products.insert_one(new_product)
print(f"\nInserted product with ID: {result.inserted_id}")

# Verify insertion
print("New product:")
pprint.pprint(db.products.find_one({"name": "sweater"}))
```

## Key Takeaways

1. **MongoDB is document-based** - Data stored as JSON-like documents
2. **Flexible schema** - Documents in same collection can have different structures
3. **No joins required** - Related data stored together in one document
4. **PyMongo translates data types automatically** - Python lists become arrays, dicts become objects
5. **Always have _id field** - Either manual or auto-generated ObjectId
6. **Case-sensitive searches** - Like SQL, but no automatic error checking for typos
7. **Immediate persistence** - Changes are saved immediately, no rollback capability

This comprehensive understanding of MongoDB and PyMongo will allow you to work effectively with document databases in Python applications.