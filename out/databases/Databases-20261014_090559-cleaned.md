# MongoDB with PyMongo

## Document Structure in MongoDB

### What is a document?
A document in MongoDB is like a Python dictionary, but it can contain lists and nested objects. Each document has a unique ID field called `_id` if you don't specify one yourself.

```python
# Example of a document structure
document = {
    "_id": ObjectId("..."),
    "name": "John Doe",
    "age": 30,
    "hobbies": ["reading", "swimming"],
    "address": {
        "street": "123 Main St",
        "city": "Uppsala"
    }
}
```

### Why documents matter
Understanding document structure is crucial because it forms the foundation of how data is stored and retrieved in MongoDB. Unlike SQL tables, documents can have varying structures within the same collection.

## PyMongo Connection

### What is PyMongo?
PyMongo is a Python driver that allows you to communicate with MongoDB from your Python applications. It provides methods to perform CRUD operations on MongoDB collections.

```python
from pymongo import MongoClient

# Connect to MongoDB
client = MongoClient('mongodb://localhost:27017/')
db = client['mydatabase']
collection = db['customers']
```

## Querying Documents

### Filters as Dictionaries
Filters in MongoDB are represented as Python dictionaries. When you query, you're essentially creating a dictionary that specifies what to look for.

```python
# Find documents where city equals "Uppsala"
filter_dict = {"city": "Uppsala"}

# Find all customers from Uppsala
customers = collection.find(filter_dict)
```

### Comparison Operators

#### Basic operators (starting with $)
MongoDB uses dollar-sign prefixed operators to perform comparisons:

```python
# Greater than
{"price": {"$gt": 500}}

# Less than or equal to
{"stock": {"$lte": 10}}

# Not equal
{"category": {"$ne": "electronics"}}

# In (matches any value in the list)
{"tags": {"$in": ["winter", "summer"]}}

# Not in
{"category": {"$nin": ["electronics", "books"]}}
```

### Why Dollar Signs?
The dollar sign (`$`) tells MongoDB to treat the key as an operator rather than a field name. This distinction is crucial for proper query interpretation.

```python
# Correct way - using $gt operator
{"price": {"$gt": 500}}

# Wrong way - this looks for field named "price" with value "$gt"
{"price": "$gt"}  # This won't work as expected
```

### Complex Queries with AND/OR

#### AND operations
For AND conditions, add multiple fields to the same dictionary:

```python
# Find shoes with price > 1000 AND category = "shoes"
result = collection.find({
    "category": "shoes",
    "price": {"$gt": 1000}
})
```

#### OR operations
For OR conditions, use the `$or` operator with a list of filters:

```python
# Find customers in Uppsala OR who get newsletter
result = collection.find({
    "$or": [
        {"city": "Uppsala"},
        {"newsletter": True}
    ]
})
```

### Range Queries
To specify ranges on the same field, put both operators in the same dictionary:

```python
# Find products with price between 200 and 600
result = collection.find({
    "price": {
        "$gte": 200,
        "$lte": 600
    }
})
```

## Nested Objects and Dot Notation

### Accessing nested fields
When documents contain nested objects, you use dot notation to access their fields:

```python
# Find products where details.color equals "black"
result = collection.find({"details.color": "black"})

# Find products where address.city equals "Stockholm"
result = collection.find({"address.city": "Stockholm"})
```

### The Dot Notation Trap
Be careful with dot notation vs. exact object matching:

```python
# This looks for documents where details is EXACTLY {"color": "black"}
# (No other fields allowed in details)
result = collection.find({"details": {"color": "black"}})

# This looks for documents where details.color equals "black"
# (Other fields in details are allowed)
result = collection.find({"details.color": "black"})
```

## Arrays and List Operations

### Searching in arrays
Arrays in MongoDB are handled naturally - you don't need special operators:

```python
# Find products with "winter" tag
result = collection.find({"tags": "winter"})

# Find products with both "winter" AND "cotton" tags
result = collection.find({"tags": {"$all": ["winter", "cotton"]}})

# Find products with exactly 4 tags
result = collection.find({"tags": {"$size": 4}})
```

### Array update operations

#### Push (add to array)
```python
# Add a new tag to the tags array
collection.update_one(
    {"_id": product_id},
    {"$push": {"tags": "new_tag"}}
)

# Add multiple values
collection.update_one(
    {"_id": product_id},
    {"$push": {"tags": {"$each": ["tag1", "tag2"]}}}
)
```

#### Add to Set (add only if not already present)
```python
# Add tag only if it doesn't exist
collection.update_one(
    {"_id": product_id},
    {"$addToSet": {"tags": "new_tag"}}
)
```

#### Pull (remove from array)
```python
# Remove all instances of a value
collection.update_one(
    {"_id": product_id},
    {"$pull": {"tags": "old_tag"}}
)
```

## Field Existence Checking

### Using `$exists` operator
This helps distinguish between missing fields and fields with null values:

```python
# Find documents that have a phone field (regardless of value)
result = collection.find({"phone": {"$exists": True}})

# Find documents that don't have a phone field
result = collection.find({"phone": {"$exists": False}})

# Find documents where phone is explicitly null
result = collection.find({"phone": None})
```

## Projection (Selecting Fields)

### Basic projection syntax
Projection lets you specify which fields to include or exclude:

```python
# Include only name and email fields
projection = {"name": 1, "email": 1, "_id": 0}
result = collection.find({}, projection)

# Exclude age field
projection = {"age": 0}
result = collection.find({}, projection)
```

### Special rules for projection
- `_id` is always included unless explicitly excluded (`_id": 0`)
- You cannot mix include (1) and exclude (0) in the same projection except for `_id`
- If you want to use both, separate them properly:

```python
# Correct way - only include specific fields
projection = {"name": 1, "email": 1}

# Correct way - only exclude specific fields  
projection = {"age": 0, "password": 0}
```

## Sorting, Limiting, and Skipping

### Methods after find()
These are methods that can be chained to `find()`:

```python
# Sort by price descending (most expensive first)
result = collection.find().sort("price", -1)

# Sort by category then price
result = collection.find().sort([("category", 1), ("price", -1)])

# Limit results to 5 documents
result = collection.find().limit(5)

# Skip first 10 documents (useful for pagination)
result = collection.find().skip(10)

# Combine all methods
result = collection.find().sort("price", -1).skip(10).limit(5)
```

### Pagination example
```python
# Page 2 with 10 items per page
page = 2
items_per_page = 10
skip_items = (page - 1) * items_per_page

result = collection.find().sort("price", -1).skip(skip_items).limit(items_per_page)
```

## Document Counting and Distinct Values

### Count documents
```python
# Count all documents matching filter
count = collection.count_documents({"category": "shoes"})

# Count all documents (empty filter)
total_count = collection.count_documents({})
```

### Distinct values
```python
# Get unique categories
categories = collection.distinct("category", {})

# Get unique tags from all documents
all_tags = collection.distinct("tags", {})
```

## Updating Documents

### Update One vs Update Many
```python
# Update only the first matching document
collection.update_one(
    {"_id": product_id},
    {"$set": {"price": 199}}
)

# Update all matching documents
collection.update_many(
    {"category": "shoes"},
    {"$set": {"on_sale": True}}
)
```

### Update Operators

#### `$set` - Set field values
```python
# Change a single field
collection.update_one({"_id": product_id}, {"$set": {"price": 199}})

# Change multiple fields
collection.update_one(
    {"_id": product_id},
    {"$set": {
        "price": 199,
        "updated_at": datetime.now()
    }}
)
```

#### `$unset` - Remove fields
```python
# Remove a field completely
collection.update_one({"_id": product_id}, {"$unset": {"phone": ""}})
```

#### `$inc` - Increment numeric values
```python
# Decrease stock by 2
collection.update_one(
    {"_id": product_id},
    {"$inc": {"stock": -2}}
)
```

#### `$push`, `$addToSet`, `$pull`
```python
# Add to array
collection.update_one({"_id": product_id}, {"$push": {"tags": "new_tag"}})

# Add only if not already present
collection.update_one({"_id": product_id}, {"$addToSet": {"tags": "new_tag"}})

# Remove from array
collection.update_one({"_id": product_id}, {"$pull": {"tags": "old_tag"}})
```

## Upsert Operations

### What is upsert?
Upsert combines update and insert operations. If a document matches the filter, it updates it; otherwise, it creates a new document.

```python
# Upsert - if customer exists, update; if not, create
collection.update_one(
    {"email": "john@example.com"},
    {
        "$set": {
            "name": "John Doe",
            "last_login": datetime.now()
        }
    },
    upsert=True
)
```

## Delete Operations

### Delete One vs Delete Many
```python
# Delete first matching document
result = collection.delete_one({"_id": product_id})

# Delete all matching documents
result = collection.delete_many({"category": "deprecated"})
```

### Important safety tip
Always check your results before deleting:

```python
# Always verify what you're about to delete
count_before = collection.count_documents({"category": "test"})
print(f"About to delete {count_before} documents")

# Then perform the actual deletion
result = collection.delete_many({"category": "test"})
print(f"Deleted {result.deleted_count} documents")
```

## Best Practices

### Safety habit
Always run a `find()` or `count_documents()` with your exact filter first, examine the results, then proceed with update or delete operations.

```python
# Safe approach
filter_criteria = {"city": "Uppsala"}
print(f"Documents to be updated: {collection.count_documents(filter_criteria)}")

# Then update only if results look correct
collection.update_many(filter_criteria, {"$set": {"status": "verified"}})
```

### Understanding results
Update operations return result objects that tell you what happened:

```python
result = collection.update_many(
    {"category": "shoes"},
    {"$set": {"discount": 0.1}}
)

print(f"Matched: {result.matched_count}")
print(f"Modified: {result.modified_count}")
```

## Summary of Key Concepts

### Operators List
- `$gt` - Greater than
- `$gte` - Greater than or equal to  
- `$lt` - Less than
- `$lte` - Less than or equal to
- `$ne` - Not equal
- `$in` - In list
- `$nin` - Not in list
- `$all` - All items in array
- `$size` - Array size
- `$exists` - Field exists
- `$set` - Set field value
- `$unset` - Remove field
- `$inc` - Increment numeric field
- `$push` - Add to array
- `$addToSet` - Add to array only if not present
- `$pull` - Remove from array

### Important Rules
1. Empty filters match all documents
2. No undo in MongoDB - always check first
3. Use dot notation for nested fields
4. Use `$or` with a list of conditions
5. Combine multiple operators on same field in one dictionary
6. Projection rules: only 1s or only 0s (except `_id`)
7. Chain sort, skip, limit methods after find() with dots

These concepts form the foundation of working with MongoDB through PyMongo and will be essential for completing your lab assignments.