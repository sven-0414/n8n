# MongoDB Design and Aggregation

## Document Design Fundamentals

### Embedding vs Referencing Data

**Definition:** In MongoDB, when designing documents, you must decide whether to embed related data directly within a document or reference it through an ID. This decision affects performance, data consistency, and query patterns.

**Code Example:**
```python
# Embedded approach - Order contains its items directly
order = {
    "_id": 1,
    "customer_id": 1001,
    "items": [
        {"product_name": "Hoodie", "price": 49.99, "quantity": 2},
        {"product_name": "Cap", "price": 29.99, "quantity": 1}
    ],
    "total": 129.97
}

# Referenced approach - Order references customer by ID
order = {
    "_id": 1,
    "customer_id": 1001,
    "items": [101, 102],  # Product IDs
    "total": 129.97
}
```

**Key Points:**
- Embedding means storing all related data within one document
- Referencing means storing only an ID that points to another document
- MongoDB doesn't enforce foreign key constraints like SQL databases
- The choice depends on how your application reads and uses the data

**Why It Matters:** This design decision fundamentally affects database performance, data consistency, and query complexity. It's not just about normalization but about optimizing for your specific access patterns.

### When to Embed Data

**Rule:** Embed when you always read the child data together with the parent document, when it belongs only to that parent, and when there aren't too many of them or they don't change frequently.

**Code Example:**
```python
# Good use case for embedding - Order items
{
    "_id": 1,
    "order_date": "2023-01-15",
    "customer_id": 1001,
    "items": [
        {"product_name": "Hoodie", "price": 49.99, "quantity": 2},
        {"product_name": "Cap", "price": 29.99, "quantity": 1}
    ]
}

# Bad use case - Don't embed thousands of orders in customer
{
    "_id": 1001,
    "name": "Anna",
    "email": "anna@example.com",
    "orders": [1, 2, 3, 4, 5, ...]  # This would cause problems with document size limits
}
```

### When to Reference Data

**Rule:** Reference when multiple documents share the same data, or when a list could grow indefinitely.

**Code Example:**
```python
# Good use case for referencing - Customer with many orders
# Customer document
{
    "_id": 1001,
    "name": "Anna",
    "email": "anna@example.com"
}

# Order document referencing customer
{
    "_id": 1,
    "customer_id": 1001,  # References customer document
    "order_date": "2023-01-15",
    "total": 129.97
}
```

## Schema Validation

### What is Schema Validation?

**Definition:** Schema validation in MongoDB allows you to define rules that documents must follow before being inserted or updated in a collection. It's MongoDB's way of implementing data integrity constraints similar to SQL's NOT NULL and CHECK constraints.

**Code Example:**
```python
# Define schema validation rules
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
                    maximum: 5
                }
            }
        }
    }
})

# This will succeed
db.products.insertOne({
    "name": "Hoodie",
    "price": 49.99,
    "category": "clothing",
    "rating": 5
})

# This will fail with validation error
db.products.insertOne({
    "name": "Hoodie",
    "price": -10.00,  # Negative price
    "category": "clothing"
})
```

**Key Points:**
- Schema validation is optional but recommended for data integrity
- Rules are defined using JSON Schema format
- Only specified fields are validated; others are still allowed
- Documents that violate rules are rejected with error message

### Validation Rules and Types

**Rule:** You can specify required fields, data types, value ranges, and allowed values.

**Code Example:**
```python
# Complete validation schema example
db.createCollection("orders", {
    validator: {
        $jsonSchema: {
            bsonType: "object",
            required: ["customer_id", "items", "order_date"],
            properties: {
                customer_id: {
                    bsonType: "objectId"
                },
                items: {
                    bsonType: "array",
                    minItems: 1,
                    maxItems: 100,
                    items: {
                        bsonType: "object",
                        required: ["product_name", "price", "quantity"],
                        properties: {
                            product_name: {bsonType: "string"},
                            price: {bsonType: "double", minimum: 0},
                            quantity: {bsonType: "int", minimum: 1}
                        }
                    }
                },
                status: {
                    enum: ["pending", "processing", "shipped", "delivered"]
                },
                order_date: {bsonType: "date"}
            }
        }
    }
})
```

## Aggregation Pipeline

### What is Aggregation Pipeline?

**Definition:** The aggregation pipeline in MongoDB is a powerful feature that processes data through a series of stages to transform and analyze documents. It's MongoDB's equivalent to SQL's GROUP BY, ORDER BY, and other set operations.

**Code Example:**
```python
# Simple aggregation pipeline to count orders by customer
pipeline = [
    {"$match": {"status": "delivered"}},
    {"$group": {"_id": "$customer_id", "order_count": {"$sum": 1}}},
    {"$sort": {"order_count": -1}},
    {"$limit": 5}
]

result = db.orders.aggregate(pipeline)
```

### Pipeline Stages

#### Match Stage
**Definition:** Filters documents based on specified conditions, equivalent to SQL's WHERE clause.

**Code Example:**
```python
# Find all delivered orders with price > 100
pipeline = [
    {"$match": {
        "status": "delivered",
        "total": {"$gt": 100}
    }}
]
```

#### Group Stage
**Definition:** Groups documents by a specified field and performs aggregations like counting, summing, averaging, etc.

**Code Example:**
```python
# Group orders by category and calculate statistics
pipeline = [
    {"$group": {
        "_id": "$category",
        "total_orders": {"$sum": 1},
        "average_price": {"$avg": "$price"},
        "max_price": {"$max": "$price"}
    }}
]
```

#### Sort Stage
**Definition:** Orders documents by specified fields, equivalent to SQL's ORDER BY clause.

**Code Example:**
```python
# Sort orders by date descending
pipeline = [
    {"$sort": {"order_date": -1}}  # -1 for descending, 1 for ascending
]
```

#### Project Stage
**Definition:** Reshapes documents by including or excluding fields, equivalent to SQL's SELECT clause.

**Code Example:**
```python
# Include only specific fields and compute new ones
pipeline = [
    {"$project": {
        "customer_name": "$customer.name",
        "total_amount": 1,
        "order_date": 1,
        "discounted_total": {"$multiply": ["$total", 0.9]}
    }}
]
```

#### Unwind Stage
**Definition:** Deconstructs an array field into separate documents for each element, making it easier to perform aggregations on array elements.

**Code Example:**
```python
# Unwind order items to count individual products sold
pipeline = [
    {"$unwind": "$items"},
    {"$group": {
        "_id": "$items.product_name",
        "total_sold": {"$sum": "$items.quantity"}
    }}
]
```

#### Lookup Stage
**Definition:** Performs a join operation by looking up documents from another collection based on matching fields, equivalent to SQL's JOIN.

**Code Example:**
```python
# Join orders with customer information
pipeline = [
    {"$lookup": {
        "from": "customers",
        "localField": "customer_id",
        "foreignField": "_id",
        "as": "customer_info"
    }},
    {"$unwind": "$customer_info"}  # Deconstruct the array from lookup
]
```

### Pipeline Order Matters

**Rule:** The order of stages in an aggregation pipeline is crucial because each stage processes the output of the previous stage.

**Code Example:**
```python
# Correct order - Match first, then group
pipeline = [
    {"$match": {"status": "delivered"}},
    {"$group": {"_id": "$customer_id", "count": {"$sum": 1}}}
]

# Incorrect order - Sort before group wastes computation
pipeline = [
    {"$sort": {"order_date": -1}},  # Sorting done first
    {"$group": {"_id": "$customer_id", "count": {"$sum": 1}}}
    # The sort is wasted because group doesn't care about order
]
```

### Accumulators in Group Stage

**Definition:** Accumulators are functions used in the group stage to perform calculations on grouped data.

**Common Accumulators:**
- `$sum`: Sums values
- `$avg`: Calculates average
- `$min`: Finds minimum value
- `$max`: Finds maximum value
- `$push`: Collects values into an array
- `$addToSet`: Adds unique values to a set

**Code Example:**
```python
# Complex group operation with multiple accumulators
pipeline = [
    {"$group": {
        "_id": "$category",
        "total_products": {"$sum": 1},
        "average_price": {"$avg": "$price"},
        "min_price": {"$min": "$price"},
        "max_price": {"$max": "$price"},
        "products": {"$push": "$name"},  # Array of product names
        "unique_customers": {"$addToSet": "$customer_id"}
    }}
]
```

## Security Considerations

### Operator Injection Attacks

**Definition:** Operator injection occurs when user input is directly used in MongoDB queries, allowing attackers to manipulate query operators like `$ne`, `$gt`, or `$in`.

**Code Example:**
```python
# Vulnerable code - Direct use of user input
user_email = "attacker@example.com"
filter = {"email": user_email}  # User could send {"$ne": null}

# This becomes: {"email": {"$ne": null}} which matches all documents

# Secure approach - Validate and sanitize input
def get_customer_orders(email):
    if not isinstance(email, str) or not email:
        raise ValueError("Invalid email format")
    
    # Build filter manually with checked values
    filter = {"email": email}
    return db.customers.find(filter)
```

### Best Practices for Security

**Rule 1:** Never let user input become part of the query structure itself.

**Rule 2:** Validate all input types and reject unexpected data structures.

**Rule 3:** Convert input properly (str, int) and handle conversion errors gracefully.

**Code Example:**
```python
# Secure approach to handling user input
def find_orders_by_email(email_input):
    try:
        # Validate type - email should be a string
        if not isinstance(email_input, str):
            raise ValueError("Email must be a string")
        
        # Check for dangerous patterns or structures
        if isinstance(email_input, dict) or isinstance(email_input, list):
            raise ValueError("Invalid input format")
            
        # Safe to use in filter
        result = db.orders.find({"customer_email": email_input})
        return list(result)
        
    except (ValueError, TypeError) as e:
        # Handle errors gracefully
        print(f"Input validation error: {e}")
        return []
```

### GDPR and Personal Data Handling

**Definition:** MongoDB's flexible schema makes it easy to store extra personal data, but this creates compliance challenges for GDPR.

**Key Points:**
- Avoid collecting unnecessary personal data
- When copying personal data, consider the deletion implications
- Prefer referencing over embedding for personal data to simplify deletion requests

**Code Example:**
```python
# Better approach - Reference customer instead of copying
# Instead of this:
order = {
    "customer_name": "Anna Smith",  # Copying personal data
    "customer_email": "anna@example.com",
    "items": [...]
}

# Do this:
order = {
    "customer_id": ObjectId("507f1f77bcf86cd799439011"),  # Reference
    "items": [...]
}
```

## Practical Design Examples

### Web Shop Design Pattern

**Example:** In a web shop, the design decisions are based on how the application actually uses the data.

**Code Example:**
```python
# Order document structure
{
    "_id": ObjectId("507f1f77bcf86cd799439012"),
    "customer_id": ObjectId("507f1f77bcf86cd799439011"),  # Reference
    "items": [
        {
            "product_id": ObjectId("507f1f77bcf86cd799439013"),
            "name": "Hoodie",
            "price": 49.99,  # Copied for receipt consistency
            "quantity": 2
        }
    ],
    "order_date": ISODate("2023-01-15"),
    "status": "delivered"
}

# Product document - separate collection
{
    "_id": ObjectId("507f1f77bcf86cd799439013"),
    "name": "Hoodie",
    "price": 49.99,
    "stock": 100,
    "category": "clothing"
}
```

**Why This Design Works:**
- Order items are embedded because they're always read together with the order
- Customer is referenced because one customer can have many orders and we want to update customer info in one place
- Products have their own collection because they're accessed independently and need to be updated for stock changes
- Prices are copied into orders to maintain historical accuracy (receipts show old prices even if products change)

## Key Takeaways

### Design Principles
1. **Data that is read together should be stored together**
2. **Consider how your application will actually use the data**
3. **Embed when data belongs only to one parent and doesn't grow infinitely**
4. **Reference when data is shared or could grow without limit**

### Performance Considerations
- Embedding provides faster reads (single database trip)
- Referencing keeps data in one place, easier to maintain consistency
- Document size limits (16MB) affect design decisions
- Large documents perform poorly

### Data Integrity
- Schema validation brings SQL-like constraints to MongoDB
- Use validation for required fields, types, and value ranges
- Validation is flexible - only validate what you care about

### Aggregation Power
- Pipeline stages process data sequentially
- Match → Group → Sort → Project operations
- Unwind and Lookup enable complex data analysis
- Order matters - optimization requires understanding pipeline flow

This comprehensive approach to MongoDB design ensures both performance optimization and data integrity while following best practices for security and compliance.