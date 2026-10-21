# MongoDB with PyMongo

## Documents and Collections

### What is a document?
A document in MongoDB is like a Python dictionary that can contain lists, nested objects, and various data types. Each document has a unique ID ( `_id` ) by default, which is automatically generated if not specified.

```python
# Example of a document structure
document = {
    "_id": ObjectId("..."),
    "name": "John Doe",
    "age": 30,
    "city": "Uppsala",
    "tags": ["winter", "cozy"],
    "details": {
        "color": "blue",
        "material": "cotton"
    }
}
```

### What is a collection?
A collection in MongoDB is like a table in SQL databases. It's a group of documents that share similar structure.

### Why does misspelled field names give zero results instead of errors?
MongoDB has a flexible schema, so it doesn't validate field names ahead of time. If you search for a non-existent field, MongoDB simply returns no results rather than throwing an error.

## Query Operators

### Comparison Operators
Comparison operators in MongoDB start with a dollar sign (`$`) and are used to compare values within documents.

#### Basic comparison operators:
- `$eq` - equal to
- `$ne` - not equal to
- `$gt` - greater than
- `$gte` - greater than or equal to
- `$lt` - less than
- `$lte` - less than or equal to

```python
# Find documents where price is less than 500
query = {"price": {"$lt": 500}}

# Find documents where category equals "shoes"
query = {"category": {"$eq": "shoes"}}

# Find documents where stock is not equal to zero
query = {"stock": {"$ne": 0}}
```

### Array Operators
Array operators work with fields that contain arrays.

#### `$in` and `$nin`:
- `$in` - matches any value in the array
- `$nin` - matches values NOT in the array

```python
# Find documents where category is either "shoes" or "clothes"
query = {"category": {"$in": ["shoes", "clothes"]}}

# Find documents where tags do not include "winter"
query = {"tags": {"$nin": ["winter"]}}
```

### `$all` operator:
- `$all` - matches documents that contain all specified values in an array

```python
# Find documents that have both "winter" and "cozy" tags
query = {"tags": {"$all": ["winter", "cozy"]}}
```

### `$size` operator:
- `$size` - matches documents with arrays of a specific size

```python
# Find documents with exactly 3 tags
query = {"tags": {"$size": 3}}

# Find shoes that come in size 44
query = {"sizes": {"$size": 44}}
```

### `$exists` operator:
- `$exists` - matches documents that have or don't have a specific field

```python
# Find documents where "phone" field exists
query = {"phone": {"$exists": True}}

# Find documents where "city" field does NOT exist
query = {"city": {"$exists": False}}
```

## Logical Operators

### `$and` operator:
- `$and` - matches documents that satisfy ALL conditions in the array
- Default behavior when using multiple fields in a single query dictionary

```python
# Find documents where category is "shoes" AND price > 1000
query = {"category": "shoes", "price": {"$gt": 1000}}

# Alternative syntax using $and (same result)
query = {"$and": [{"category": "shoes"}, {"price": {"$gt": 1000}}]}
```

### `$or` operator:
- `$or` - matches documents that satisfy ANY of the conditions in the array

```python
# Find customers in Uppsala OR who get the newsletter
query = {
    "$or": [
        {"city": "Uppsala"},
        {"newsletter": True}
    ]
}

# Find products where category is "shoes" OR stock equals 0
query = {
    "$or": [
        {"category": "shoes"},
        {"stock": 0}
    ]
}
```

### Important rule for same field queries:
When you need multiple conditions on the same field, they must be in the same dictionary. You cannot have two separate keys with the same name in a Python dictionary.

```python
# WRONG - This will only keep the last condition
query = {
    "price": {"$gt": 200},
    "price": {"$lt": 600}  # This overwrites the first one
}

# CORRECT - Both conditions go in the same dictionary
query = {
    "price": {"$gt": 200, "$lt": 600}
}
```

## Dot Notation for Nested Objects

### Accessing nested fields:
Use dot notation to access fields within nested objects.

```python
# Find products where details.color equals "black"
query = {"details.color": "black"}

# Find products with nested address object
query = {"address.city": "Uppsala"}

# Find documents with deeply nested structure
query = {"address.city.postcode": "75130"}
```

### Important trap with dot notation:
If you write a query like `{"details": {"color": "black"}}`, MongoDB looks for documents where the entire `details` object matches exactly. It will NOT find documents where `details` contains both color and material fields.

```python
# WRONG - This won't work as expected
query = {"details": {"color": "black"}}  # Looks for exact match

# CORRECT - Use dot notation to access nested field
query = {"details.color": "black"}  # Looks for color field within details object
```

## Projection (Selecting Fields)

### What is projection?
Projection determines which fields to include or exclude from the result set.

```python
# Include only name and price fields
projection = {"name": 1, "price": 1}

# Exclude only the _id field
projection = {"_id": 0}

# Include name, exclude age
projection = {"name": 1, "age": 0}
```

### Special rules for projection:
1. `_id` is always included unless explicitly excluded with `{"_id": 0}`
2. You can't mix inclusion and exclusion (except for `_id`)
3. If you use inclusion (`1`), don't use exclusion (`0`) in the same projection
4. If you use exclusion (`0`), don't use inclusion (`1`) in the same projection

```python
# Correct usage - include specific fields
projection = {"name": 1, "price": 1}

# Correct usage - exclude specific fields (except _id)
projection = {"description": 0, "details": 0}

# Incorrect usage - mixing inclusion and exclusion
# projection = {"name": 1, "age": 0}  # This would cause an error
```

## Sorting, Limiting, and Skipping

### `sort()` method:
Sorts results by one or more fields.

```python
# Sort by price ascending (smallest first)
cursor = collection.find().sort("price", 1)

# Sort by price descending (largest first)
cursor = collection.find().sort("price", -1)

# Sort by multiple fields
cursor = collection.find().sort([("category", 1), ("price", -1)])
```

### `limit()` method:
Limits the number of documents returned.

```python
# Return only first 5 documents
cursor = collection.find().limit(5)
```

### `skip()` method:
Skips a specified number of documents (used for pagination).

```python
# Skip first 10 documents, then return next 10
cursor = collection.find().skip(10).limit(10)
```

### Chaining methods:
You can chain these methods together in any order.

```python
# Find products, sort by price descending, skip first 5, limit to 10
cursor = collection.find().sort("price", -1).skip(5).limit(10)
```

## Counting Documents

### `count_documents()` method:
Counts documents matching a filter (more efficient than getting all documents).

```python
# Count all documents in collection
total_count = collection.count_documents({})

# Count documents with specific filter
shoe_count = collection.count_documents({"category": "shoes"})

# Count documents with complex filter
expensive_shoes = collection.count_documents({
    "category": "shoes",
    "price": {"$gt": 1000}
})
```

### `distinct()` method:
Returns unique values for a field across all matching documents.

```python
# Get all unique categories
categories = collection.distinct("category", {})

# Get all unique tags from documents
tags = collection.distinct("tags", {})  # Works with arrays too

# Get unique city names
cities = collection.distinct("address.city", {})
```

## Updating Documents

### `update_one()` and `update_many()` methods:
Update one or multiple documents based on a filter.

```python
# Update first matching document
result = collection.update_one(
    {"_id": ObjectId("...")},  # Filter
    {"$set": {"price": 199}}   # Update operation
)

# Update all matching documents
result = collection.update_many(
    {"category": "shoes"},     # Filter
    {"$set": {"onsale": True}} # Update operation
)
```

### Update Operators:
#### `$set` - sets field value:
```python
# Set price to 199
{"$set": {"price": 199}}

# Set multiple fields
{"$set": {"price": 199, "status": "updated"}}
```

#### `$unset` - removes field:
```python
# Remove phone field from document
{"$unset": {"phone": ""}}
```

#### `$inc` - increments/decrements numeric values:
```python
# Decrease stock by 2 (for a purchase)
{"$inc": {"stock": -2}}

# Increase stock by 5
{"$inc": {"stock": 5}}
```

#### `$push` - adds element to array:
```python
# Add tag to tags array
{"$push": {"tags": "new_tag"}}

# Add multiple elements
{"$push": {"tags": {"$each": ["tag1", "tag2"]}}}
```

#### `$addToSet` - adds element only if not already present:
```python
# Add tag only if it's not already in array
{"$addToSet": {"tags": "new_tag"}}
```

#### `$pull` - removes elements from array:
```python
# Remove specific tag from array
{"$pull": {"tags": "old_tag"}}

# Remove all occurrences of a value
{"$pull": {"tags": {"$in": ["tag1", "tag2"]}}}
```

## Upsert Operations

### `upsert` behavior:
Upsert combines update and insert operations. If no document matches the filter, it creates a new one.

```python
# Upsert - if email exists, update; otherwise create new customer
result = collection.update_one(
    {"email": "john@example.com"},  # Filter
    {
        "$set": {
            "name": "John Doe",
            "age": 30
        }
    },
    upsert=True  # This enables upsert behavior
)

# Check result
if result.upserted_id:
    print(f"Created new document with ID: {result.upserted_id}")
else:
    print("Updated existing document")
```

## Deleting Documents

### `delete_one()` and `delete_many()` methods:
Delete one or multiple documents based on a filter.

```python
# Delete first matching document
result = collection.delete_one({"_id": ObjectId("...")})

# Delete all matching documents
result = collection.delete_many({"category": "shoes"})

# Check how many were deleted
print(f"Deleted {result.deleted_count} documents")
```

## Important Safety Rules

### Always check first before updating/deleting:
```python
# Check what will be affected
documents_to_change = list(collection.find({"category": "shoes"}))
print(f"Found {len(documents_to_change)} documents")

# Then perform the actual update
result = collection.update_many(
    {"category": "shoes"},
    {"$set": {"onsale": True}}
)
```

### Empty filter danger:
An empty filter (`{}`) matches ALL documents.

```python
# DANGEROUS - This will make ALL products free!
collection.update_many({}, {"$set": {"price": 0}})

# SAFE - Always check first
print("This will make ALL products free!")
response = input("Continue? (y/n): ")
if response == "y":
    collection.update_many({}, {"$set": {"price": 0}})
```

## Key Concepts Summary

1. **Documents** are like Python dictionaries with flexible schemas
2. **Collections** are groups of documents, similar to SQL tables
3. **Query operators** start with `$` (e.g., `$lt`, `$in`, `$exists`)
4. **Logical operators** include `$and` and `$or`
5. **Dot notation** accesses nested fields (e.g., `details.color`)
6. **Projection** controls which fields are returned
7. **Sorting, limiting, and skipping** control result set size and order
8. **Updating** requires both filter and update operations
9. **Upsert** creates new documents if they don't exist
10. **Delete operations** work similarly to updates but without second dictionary
11. **Safety rule**: Always use `find()` or `count_documents()` with exact filter before modifying data
12. **No undo**: MongoDB changes are immediate - no built-in rollback mechanism

These concepts form the foundation of working with MongoDB through PyMongo and should be thoroughly understood before implementing any database operations in your applications.