## Introduction to NoSQL and MongoDB

### Overview of NoSQL
NoSQL stands for "not only SQL," not "not SQL." It is designed to handle various types of data and applications that SQL databases might struggle with. NoSQL databases are typically used for data that changes shape often, data that fits well into objects like Python dictionaries, and data that needs to be spread across many servers. 

### SQL vs. NoSQL
- **SQL**: Requires defining tables and columns in advance. Data is stored in a rigid structure.
- **NoSQL**: More flexible, allowing documents to have varying fields. No need to define schema in advance, though it's generally not enforced by the database.

### MongoDB Introduction
- **MongoDB**: A document-oriented NoSQL database.
- **Data Representation**: Stores data in documents (JSON-like structures) with unique IDs and embedded sub-documents.
- **Installation**: Can be installed on a local machine and accessed through a GUI tool like MongoDB Compass.

### Connecting to MongoDB from Python
- **PyMongo**: Python library for interacting with MongoDB.
- **Connection Setup**:
    ```python
    from pymongo import MongoClient
    client = MongoClient('mongodb://localhost:27017/')
    db = client['webshop']
    ```
- **Collections and Documents**:
    - A collection is similar to a SQL table.
    - A document is similar to a SQL row, but can have varying fields.

### CRUD Operations with PyMongo
- **Create**:
    ```python
    result = db.products.insert_one({"name": "gloves", "category": "accessories", "price": 199, "stock": 20})
    print(result.inserted_id)
    ```
- **Read**:
    ```python
    product = db.products.find_one({"name": "gloves"})
    pprint(product)  # for pretty printing
    ```
- **Update**:
    ```python
    db.products.update_one({"name": "gloves"}, {"$set": {"price": 200}})
    ```
- **Delete**:
    ```python
    db.products.delete_one({"name": "gloves"})
    ```

### Queries and Filters
- **Basic Query**:
    ```python
    product = db.products.find_one({"name": "gloves"})
    print(product['price'])
    ```
- **Complex Queries**:
    ```python
    for customer in db.customers.find({"city": "Uppsala"}):
        print(customer['first_name'])
    ```

### Important Considerations
- **Flexibility**: NoSQL databases allow for flexible schema designs, but this comes with the responsibility of ensuring data consistency.
- **No Auto-Generated Errors**: Misspelling field names or making schema errors won't result in an error, but can lead to unexpected behavior.
- **Data Integrity**: Use Python's `.get()` method to handle missing fields gracefully.
- **Upserts**: Operations like `update_one` with `upsert=True` can be used to create documents if they don't exist.

### Practical Application
Throughout the week, you'll work with MongoDB to perform various operations such as inserting data, querying data, and handling relationships that were complex with SQL. Understanding these concepts will be crucial for modern application development.