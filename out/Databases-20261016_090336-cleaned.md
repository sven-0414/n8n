# Designing Documents in MongoDB

## Core Concepts of Document Design

### What is Document Design?

Document design in MongoDB involves deciding how to structure data within documents to optimize for read operations. Unlike SQL databases where normalization is the primary concern, MongoDB emphasizes **how data will be used** rather than how it's related.

The fundamental principle: **data that is read together should be stored together**.

This approach means:
- When you access an order, you also get all its items
- When you look at a customer, you can see their orders without additional queries
- The design reflects your application's usage patterns

### Embedding vs Referencing Data

#### Embedding
- **Definition**: Storing related data directly inside the parent document
- **Example**: Items are stored directly within an order document
- **Advantages**:
  - Faster reads (single database trip)
  - No need for joins or lookups
  - Atomic operations on the entire document

#### Referencing
- **Definition**: Storing only a reference (like a foreign key) to another document
- **Example**: An order contains just a customer_id that points to a separate customer document
- **Advantages**:
  - Keeps data in one place for easy updates
  - Avoids duplication of data
  - Prevents documents from growing too large

```python
# Example of embedded structure (order with items)
order = {
    "_id": ObjectId("..."),
    "customer_id": ObjectId("..."),
    "items": [
        {"product_name": "Hoodie", "price": 49.99, "quantity": 2},
        {"product_name": "Cap", "price": 29.99, "quantity": 1}
    ],
    "total": 129.97
}

# Example of referenced structure (order with customer_id)
order = {
    "_id": ObjectId("..."),
    "customer_id": ObjectId("..."),
    "items": [
        {"product_id": ObjectId("..."), "quantity": 2},
        {"product_id": ObjectId("..."), "quantity": 1}
    ]
}

customer = {
    "_id": ObjectId("..."),
    "name": "Anna",
    "email": "anna@example.com"
}
```

### When to Embed vs Reference

**Embed when:**
1. Data is always read together with the parent document
2. The related data belongs only to that specific parent
3. There aren't too many of these items (avoid unlimited growth)
4. The data doesn't change frequently

**Reference when:**
1. Many documents share the same piece of data
2. A list could grow indefinitely
3. Data needs to be updated in one place and reflected everywhere

### Practical Examples

#### Blog Post and Comments
- **Embed**: Few comments (typically less than 100)
- **Reference**: Thousands of comments

#### Student and Courses
- **Reference**: Many students share the same courses, and course details change frequently

#### Person and Address
- **Embed**: Address belongs to that person and is read together with them

#### Product and Reviews
- **Depends**: Few reviews per product (embed), thousands of reviews (reference)

## Denormalization and Copying Data

### What is Denormalization?

Denormalization refers to the practice of duplicating data across multiple documents for performance reasons. In MongoDB, this is often necessary because:
- Single document reads are faster than joins
- It prevents having to access multiple collections during read operations

```python
# Example of denormalized data (price copied into order item)
order = {
    "_id": ObjectId("..."),
    "items": [
        {
            "product_name": "Hoodie",
            "unit_price": 49.99,  # Copied from product collection
            "quantity": 2,
            "subtotal": 99.98
        }
    ]
}
```

### Why Copy Data?

Copying data is useful for:
1. **Performance**: Everything needed in one read operation
2. **Historical accuracy**: Price on receipt should remain constant even if product price changes later
3. **Atomicity**: Updates to a single document are atomic

### The Update Problem

When you copy data, you face the "update problem":
- If you update the original field and forget to update all copies, data becomes inconsistent
- Example: Copying a customer's email into every order means updating thousands of documents when it changes

```python
# Bad practice - copying email into orders
order = {
    "_id": ObjectId("..."),
    "customer_email": "anna@example.com",  # This should be referenced, not copied
    "items": [...]
}
```

### Rule for Copying Data

**Copy things that describe the moment.**
**Reference things that describe the present.**

This means:
- **Copy**: Price on receipt, product name at time of purchase
- **Reference**: Customer's current email, address, phone number

## Document Size Limitations

### 16-MB Document Limit

MongoDB has a hard limit of 16 megabytes per document. This is a critical constraint that affects design decisions:

```python
# This would be problematic - customer with thousands of orders
customer = {
    "_id": ObjectId("..."),
    "name": "Anna",
    "orders": [  # This could easily exceed 16MB
        {"order_id": ObjectId("..."), "total": 129.97, "date": "2023-01-01"},
        # ... thousands more orders
    ]
}
```

### Best Practices for Large Arrays

**Rule**: If a list can grow without limit, give it its own collection.

Arrays that should be separate collections:
- Customer's order history
- Product reviews (if there could be thousands)
- Blog comments (if they could reach thousands)

## Schema Validation

### What is Schema Validation?

Schema validation in MongoDB provides rules for documents to ensure data integrity. It's MongoDB's version of SQL constraints like NOT NULL and CHECK.

```python
# Example of schema validation rules
db.createCollection("products", {
    validator: {
        $jsonSchema: {
            bsonType: "object",
            required: ["name", "price", "category"],
            properties: {
                name: {
                    bsonType: "string",
                    description: "must be a string and is required"
                },
                price: {
                    bsonType: "double",
                    minimum: 0,
                    description: "must be a positive number"
                },
                rating: {
                    bsonType: "int",
                    minimum: 1,
                    maximum: 5,
                    description: "must be an integer between 1 and 5"
                }
            }
        }
    }
})
```

### Key Features of Schema Validation

1. **Required fields**: Define which fields must exist
2. **Type checking**: Ensure data is of correct type (string, number, etc.)
3. **Value constraints**: Set minimum/maximum values
4. **Enum validation**: Allow only specific values from a list

```python
# Example with multiple validation rules
db.createCollection("orders", {
    validator: {
        $jsonSchema: {
            bsonType: "object",
            required: ["customer_id", "items", "status"],
            properties: {
                customer_id: {
                    bsonType: "objectId"
                },
                items: {
                    bsonType: "array",
                    minItems: 1,
                    maxItems: 100
                },
                status: {
                    enum: ["pending", "processing", "shipped", "delivered", "cancelled"]
                }
            }
        }
    }
})
```

### Benefits of Schema Validation

- Provides data integrity similar to SQL constraints
- Prevents invalid documents from being inserted
- Offers flexibility - only validate fields you care about
- Allows for safe document updates without validation rules for other fields

## Aggregation Pipeline

### What is the Aggregation Pipeline?

The aggregation pipeline in MongoDB is MongoDB's version of SQL's GROUP BY, JOIN, and other complex operations. It works like an assembly line where documents flow through stages to produce results.

```python
# Example aggregation pipeline
pipeline = [
    {"$match": {"status": "delivered"}},
    {"$group": {"_id": "$customer_id", "order_count": {"$sum": 1}}},
    {"$sort": {"order_count": -1}},
    {"$limit": 5}
]
```

### Pipeline Stages

#### Match Stage
- Equivalent to SQL WHERE clause
- Filters documents before further processing

```python
# Find delivered orders with stock > 0
pipeline = [
    {"$match": {"status": "delivered", "stock": {"$gt": 0}}}
]
```

#### Group Stage
- Equivalent to SQL GROUP BY
- Groups documents and performs aggregations

```python
# Group orders by customer and count them
pipeline = [
    {"$group": {
        "_id": "$customer_id",
        "order_count": {"$sum": 1},
        "total_spent": {"$sum": "$total"}
    }}
]
```

#### Sort Stage
- Equivalent to SQL ORDER BY
- Orders results

```python
# Sort by total spent descending
pipeline = [
    {"$sort": {"total_spent": -1}}
]
```

#### Project Stage
- Equivalent to SQL SELECT
- Determines which fields appear in the output

```python
# Select only specific fields
pipeline = [
    {"$project": {
        "customer_name": 1,
        "order_count": 1,
        "_id": 0
    }}
]
```

#### Unwind Stage
- Breaks arrays into separate documents
- Used when you need to process array elements individually

```python
# Unwind order items to count each item separately
pipeline = [
    {"$unwind": "$items"},
    {"$group": {
        "_id": "$items.product_name",
        "total_sold": {"$sum": "$items.quantity"}
    }}
]
```

#### Lookup Stage
- MongoDB's version of JOIN
- Performs a left outer join between collections

```python
# Join orders with customers
pipeline = [
    {"$lookup": {
        "from": "customers",
        "localField": "customer_id",
        "foreignField": "_id",
        "as": "customer_info"
    }},
    {"$unwind": "$customer_info"}
]
```

### Important Pipeline Concepts

#### Dollar Sign Notation
In aggregation pipelines, a dollar sign ($) before a field name refers to the value of that field:

```python
# Group by category (category field's value)
{"$group": {
    "_id": "$category",
    "count": {"$sum": 1}
}}
```

#### Accumulators
Accumulators perform calculations on grouped data:
- `$sum`: Add up values
- `$avg`: Calculate average
- `$min`: Find minimum value
- `$max`: Find maximum value
- `$push`: Collect values into an array

### Pipeline Order Matters

The order of stages in the pipeline is crucial:

```python
# Correct: Match first, then group
pipeline = [
    {"$match": {"status": "delivered"}},
    {"$group": {"_id": "$customer_id", "count": {"$sum": 1}}}
]

# Incorrect: Group first, then match (inefficient)
pipeline = [
    {"$group": {"_id": "$customer_id", "count": {"$sum": 1}}},
    {"$match": {"status": "delivered"}}  # This won't work as expected
]
```

### Practical Pipeline Example

```python
# Complete example: Find top 5 customers by total spending
pipeline = [
    {"$match": {"status": "delivered"}},
    {"$group": {
        "_id": "$customer_id",
        "total_spent": {"$sum": "$total"},
        "order_count": {"$sum": 1}
    }},
    {"$sort": {"total_spent": -1}},
    {"$limit": 5},
    {"$lookup": {
        "from": "customers",
        "localField": "_id",
        "foreignField": "_id",
        "as": "customer"
    }},
    {"$unwind": "$customer"},
    {"$project": {
        "customer_name": "$customer.name",
        "total_spent": 1,
        "order_count": 1
    }}
]
```

## Security Considerations

### Operator Injection Attacks

MongoDB is vulnerable to operator injection attacks, similar to SQL injection:

```python
# Example of dangerous user input handling
user_input = {"email": {"$ne": null}}  # Attack payload

# Dangerous - directly inserting user input into filter
filter = user_input  # This would match all customers!

# Safe approach - validate and build filters manually
if isinstance(user_input.get("email"), str):
    filter = {"email": user_input["email"]}
else:
    raise ValueError("Invalid email format")
```

### Best Practices for Security

1. **Never let user input become part of the query itself**
2. **Build filter dictionaries yourself in code**
3. **Check data types** - validate that inputs are what you expect
4. **Convert inputs appropriately** - use str(), int() with error handling
5. **Provide friendly error messages** instead of stack traces

### Personal Data Protection

#### GDPR Considerations in MongoDB

1. **Flexible schema temptation**: Don't save unnecessary personal data like birth dates
2. **Copying personal data**: If you copy customer data into multiple documents, deletion becomes complex

```python
# Bad practice for personal data
order = {
    "customer_name": "Anna",  # Copied from customer collection
    "customer_email": "anna@example.com",  # Copied from customer collection
    "items": [...]
}

# Better approach - reference customer data
order = {
    "customer_id": ObjectId("..."),  # Reference to customer document
    "items": [...]
}
```

### Recommended Practices for Personal Data

1. **Collect only what you need**
2. **Keep it correct** - maintain data accuracy
3. **Delete it when no longer needed**
4. **Protect it** - implement proper security measures
5. **Prefer references over copies** for personal data
6. **Copy only what law or business requirements mandate**

## Summary of Key Points

### Design Principles
1. **Read patterns drive design**: How will your app use the data?
2. **Data read together = stored together**
3. **Consider trade-offs**: Performance vs data consistency

### Embedding and Referencing
- Embed when data is always used together, rarely changes, and is limited in quantity
- Reference when data is shared, can grow infinitely, or needs to be updated in one place

### Denormalization
- Copy data that describes a moment (price on receipt)
- Reference data that describes the present (customer email)

### Schema Validation
- Provides SQL-like constraints in MongoDB
- Validates required fields, types, and value ranges
- Offers flexibility - only validate what matters

### Aggregation Pipeline
- Multi-stage processing of documents
- Match → Group → Sort → Project → Unwind → Lookup
- Order matters for efficiency and correctness

### Security
- Protect against operator injection attacks
- Never let user input directly become part of database queries
- Validate all inputs and provide appropriate error handling

These concepts form the foundation of effective MongoDB document design, helping you create efficient, secure, and maintainable database structures.