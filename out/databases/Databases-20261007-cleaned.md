# CRUD Operations in Databases

## What are CRUD Operations?

CRUD stands for Create, Read, Update, and Delete. These are the four fundamental operations that every database application must support.

### Definition
CRUD operations represent the basic functions needed to manage data in a database:
- **Create**: Insert new records into tables
- **Read**: Retrieve existing records from tables  
- **Update**: Modify existing records in tables
- **Delete**: Remove records from tables

## Create Operation - INSERT

### Rule
The INSERT operation adds new rows to a database table. You specify which columns to fill and provide corresponding values in the same order.

### Code Example
```sql
INSERT INTO orders (order_id, customer_id, order_date, status) 
VALUES (1, 1, '2026-01-01', 'delivered');
```

### Gotchas and Edge Cases
- If you skip a column that has a default value or allows NULL, the database will use the default or insert NULL
- You can insert multiple rows in one statement by separating them with commas
- Column order must match the VALUES order exactly
- If a column has NOT NULL constraint but no value is provided, it will cause an error

### Why This Matters
Understanding INSERT is crucial because it's how data enters your database system. Every application that creates records (like posting on social media) uses this operation.

## Read Operation - SELECT

### Rule
The SELECT operation retrieves data from database tables. You specify which columns to return and optionally filter the results.

### Code Example
```sql
SELECT * FROM orders WHERE order_id = 1;
```

### Gotchas and Edge Cases
- SELECT * returns all columns (useful for debugging but not efficient in production)
- You can select specific columns instead of all: `SELECT customer_id, order_date FROM orders`
- Without a WHERE clause, SELECT returns all rows
- SELECT statements can be combined with WHERE, ORDER BY, LIMIT clauses for more complex queries

### Why This Matters
Reading data is the most frequent operation in applications. Understanding SELECT helps you retrieve exactly the information you need efficiently.

## Update Operation - UPDATE

### Rule
The UPDATE operation modifies existing records in a table. You specify which columns to change and use WHERE clause to target specific rows.

### Code Example
```sql
UPDATE products 
SET price = 549 
WHERE product_id = 1;
```

### Gotchas and Edge Cases
- **Most Critical Rule**: Always use WHERE clause with UPDATE
- Without WHERE, ALL rows in the table will be updated (this is dangerous!)
- The database treats "no WHERE" as meaning "update all rows"
- You can update multiple columns in one statement: `UPDATE products SET price = 549, category = 'new' WHERE product_id = 1`

### Why This Matters
UPDATE operations are powerful but dangerous. A missing WHERE clause can destroy entire datasets, as shown by real-world company disasters.

## Delete Operation - DELETE

### Rule
The DELETE operation removes rows from a database table. Like UPDATE, it requires a WHERE clause to specify which rows to delete.

### Code Example
```sql
DELETE FROM customers WHERE customer_id = 9;
```

### Gotchas and Edge Cases
- **Always use WHERE clause with DELETE** - otherwise ALL rows are deleted
- If you try to delete a record that's referenced by another table (foreign key constraint), the operation fails
- DELETE removes entire rows, not just individual column values
- To clear a column value without deleting the row, use UPDATE: `UPDATE customers SET phone_number = NULL WHERE customer_id = 9`

### Why This Matters
DELETE operations can be catastrophic if misused. They're often the most dangerous database operation and require careful planning.

## Transaction Management

### Rule
A transaction groups multiple database operations that must all succeed or all fail together. This ensures data consistency.

### Code Example
```sql
-- Start transaction (implicit in most DB browsers)
BEGIN TRANSACTION;

-- Multiple operations that should be atomic
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;

-- Commit if all successful, or rollback if error occurs
COMMIT;  -- Save changes permanently
-- ROLLBACK;  -- Undo changes if something went wrong
```

### Gotchas and Edge Cases
- In DB Browser, transactions are built-in: each change waits until you save (Ctrl+S)
- Commit = permanent save, Rollback = undo all changes since last save
- Transactions prevent partial updates that could leave data in inconsistent state
- Think of it like a "save point" in video games - if you die, you can go back to the last checkpoint

### Why This Matters
Transactions are essential for maintaining data integrity. Real-world example: transferring money between bank accounts - both operations must succeed or neither should occur.

## Database Design Principles

### The Golden Rule of Database Design
Every fact should be stored in exactly one place.

### Problems with Bad Database Design

#### 1. Update Problem
When the same information appears in multiple places, updating becomes error-prone:
```sql
-- BAD: Anna's address stored in multiple rows
-- If Anna moves, you must update every row where she appears
```

#### 2. Delete Problem  
Deleting a record can accidentally remove related data:
```sql
-- BAD: Deleting Maria from orders table also deletes Maria completely
```

#### 3. Insert Problem
You cannot add new entities until they have transactions:
```sql
-- BAD: Cannot add new customer until they make an order
```

## Database Relationships

### Types of Relationships
1. **One-to-One (1:1)** - Rare, one person has one passport
2. **One-to-Many (1:N)** - Most common, one customer has many orders  
3. **Many-to-Many (N:M)** - Complex, requires junction table

### Example Relationships
```sql
-- One Customer → Many Orders
-- One Order → One Customer

-- Many Products → Many Orders
-- Requires junction table: order_items
```

## Entity Relationship Diagrams (ERDs)

### Rule
An ERD is a visual map showing database tables and their relationships:
- Each box represents a table with its columns
- Lines show how tables connect
- Use "1" and "N" to indicate relationship types

### Why This Matters
Drawing ERDs before building databases helps prevent design flaws and makes implementation easier.

## Database Normalization

### First Normal Form (1NF)
**Rule**: Every column must contain only atomic (indivisible) values. No lists in cells.

### Code Example
```sql
-- BAD: List stored in one cell
products table with product_name column containing "joggers, caps, t-shirts"

-- GOOD: Separate rows for each product
order_items table with separate rows for each product in an order
```

### Second Normal Form (2NF)
**Rule**: Every non-key column must depend on the entire primary key.

### Example Problem
```sql
-- BAD: Order_items table with partial key dependency
order_id | product_id | product_name | price
1          1            Hoodie        59.99
1          2            T-Shirt       29.99

-- PROBLEM: Product name depends only on product_id, not the full key (order_id + product_id)
```

### Third Normal Form (3NF)
**Rule**: Every non-key column must depend only on the primary key.

### Example Problem
```sql
-- BAD: Order table with city dependency
orders table with columns:
order_id | customer_id | customer_city | total_amount

-- PROBLEM: customer_city depends on customer_id, not the order itself
```

## Design Recipe for Good Database Design

1. **Identify Nouns**: Underline important concepts in descriptions
2. **Create Tables**: Each noun becomes a table with relevant columns
3. **Find Relationships**: Determine how tables connect (1:1, 1:N, N:M)
4. **Handle Many-to-Many**: Add junction tables for complex relationships
5. **Draw ERD**: Visualize the design before implementation
6. **Verify Design**: Check against normalization rules

### Example Design Process
For a library system:
1. **Nouns**: books, members, loans
2. **Tables**: Create books, members, loans tables
3. **Relationships**: 
   - Members → Loans (1:N)
   - Books → Loans (1:N) 
   - Members ↔ Books (N:M through loans table)
4. **ERD**: Draw connections with proper 1:N and M:N relationships

## Practical Examples

### Inserting Multiple Rows
```sql
INSERT INTO orders (order_id, customer_id, order_date, status) 
VALUES 
    (16, 7, '2026-01-04', 'new'),
    (17, 2, '2026-01-04', 'delivered');
```

### Safe Update Pattern
```sql
-- Step 1: Check what you're about to change
SELECT * FROM orders WHERE order_id = 9;

-- Step 2: Make the update
UPDATE orders 
SET status = 'delivered' 
WHERE order_id = 9;

-- Step 3: Verify results
SELECT * FROM orders WHERE order_id = 9;
```

### Handling Foreign Key Constraints
```sql
-- This will fail because customer 9 has orders
DELETE FROM customers WHERE customer_id = 9;

-- First delete related orders
DELETE FROM orders WHERE customer_id = 9;
-- Then delete the customer
DELETE FROM customers WHERE customer_id = 9;
```

## Best Practices Summary

1. **Always use WHERE** with UPDATE and DELETE operations
2. **Test queries first** with SELECT using the same WHERE clause
3. **Use transactions** for complex operations that must succeed or fail together
4. **Design before implementing** - draw ERDs and apply normalization rules
5. **Store each fact once only** - avoid data duplication
6. **Always save your work** (Ctrl+S in DB Browser) to commit changes permanently
7. **Use rollback when needed** to undo accidental changes

## Common Error Messages

### Unique Constraint Failed
```sql
-- Error when trying to insert duplicate primary key
INSERT INTO orders VALUES (1, 1, '2026-01-01', 'new');
-- Error: UNIQUE constraint failed: orders.order_id
```

### Check Constraint Failed  
```sql
-- Error when violating value restrictions
INSERT INTO orders VALUES (17, 2, '2026-01-04', 'lost');
-- Error: CHECK constraint failed: status must be one of new, shipped, delivered, cancelled
```

### Foreign Key Constraint Violation
```sql
-- Error when trying to delete record referenced by another table
DELETE FROM customers WHERE customer_id = 9;
-- Error: FOREIGN KEY constraint failed - customer has orders
```

## Key Takeaways

1. CRUD operations form the foundation of database interactions
2. UPDATE and DELETE without WHERE can destroy data irreversibly  
3. Transactions ensure data consistency for multi-step operations
4. Good database design prevents common problems like data inconsistency
5. Normalization rules help create efficient, maintainable databases
6. Always plan your database structure before implementation with ERDs

This comprehensive understanding of CRUD operations and database design principles will prepare you for both practical work and advanced database concepts.