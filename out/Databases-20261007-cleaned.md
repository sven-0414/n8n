# CRUD Operations

## What is CRUD?

CRUD stands for **Create, Read, Update, Delete** - these are the four fundamental operations that any database system must support. These operations form the basis of how data is managed in applications.

### The Rule
Every application in the world performs these four basic operations:
- **Create**: Adding new data to the database
- **Read**: Retrieving data from the database  
- **Update**: Modifying existing data
- **Delete**: Removing data from the database

## CREATE Operation - INSERT

### The Rule
The `INSERT` statement adds new rows to a database table. You specify which columns you want to fill and provide corresponding values in the same order.

### Code Example
```sql
INSERT INTO orders (order_id, customer_id, order_date, status) 
VALUES (1, 1, '2026-01-01', 'delivered');
```

### Key Points
- When you skip a column, it gets a `NULL` value or default value if one is specified
- You can insert multiple rows in a single statement by putting each row in parentheses separated by commas
- This is much faster than writing multiple `INSERT` statements

### Why It Matters
This is the foundation for adding new data to your database. Without proper `INSERT` operations, your application cannot create new records.

## READ Operation - SELECT

### The Rule
The `SELECT` statement retrieves data from the database. You specify what columns you want and optionally filter which rows to return.

### Code Example
```sql
SELECT * FROM orders WHERE order_id = 1;
```

### Key Points
- Use `SELECT` with the same `WHERE` clause before performing updates or deletes
- This helps prevent accidentally modifying the wrong records
- `SELECT` is the most commonly used operation in databases

## UPDATE Operation

### The Rule
The `UPDATE` statement modifies existing data in a database table. You specify which columns to change and use `WHERE` to identify which rows to update.

### Code Example
```sql
UPDATE products 
SET price = 549 
WHERE product_id = 1;
```

### Critical Warning - The Most Important Rule
**Always write the `WHERE` clause first before running an update or delete operation!**

### Gotchas and Edge Cases
- If you forget the `WHERE` clause, every row in the table will be updated
- This can cause catastrophic data loss in real applications
- Example of what NOT to do:
```sql
UPDATE products SET price = 0; -- Updates ALL products to $0!
```

### Safe Practice
1. First run a `SELECT` with the same `WHERE` clause to see which rows will be affected
2. Then run the `UPDATE`
3. This is like "measure twice, cut once"

### Why It Matters
Updates are frequently used in applications but can be extremely dangerous if not handled carefully. Proper use of `WHERE` clauses prevents accidental data corruption.

## DELETE Operation

### The Rule
The `DELETE` statement removes rows from a database table. Like updates, you must specify which rows to delete using a `WHERE` clause.

### Code Example
```sql
DELETE FROM customers WHERE customer_id = 10;
```

### Key Points
- Delete removes entire rows, not individual column values
- If you want to clear just one column's value, use `UPDATE` with `NULL`
- You cannot delete a record if other records reference it (foreign key constraint)

### Foreign Key Constraints
If a customer has orders, the database will prevent deletion of that customer because the orders would point to a non-existent customer.

### Why It Matters
Deletes are one of the most dangerous operations. They can permanently remove data from your system, so careful use of `WHERE` clauses is essential.

## Transactions

### The Rule
A transaction groups multiple database changes together so they either all succeed or all fail. This ensures data consistency.

### Code Example
```sql
-- In DB Browser:
-- Press Ctrl+S to COMMIT changes
-- Use ROLLBACK to undo changes since last save
```

### Key Points
- **COMMIT**: Saves all changes permanently
- **ROLLBACK**: Undoes all changes since the last save point
- Transactions are like "save points" in video games

### Real World Example
When transferring money via Swish:
1. Money is deducted from your account  
2. Money is added to your friend's account
3. Both operations must succeed or both must fail

### Why It Matters
Transactions ensure data integrity in applications where multiple related changes must happen together.

# Database Design Principles

## The Golden Rule of Good Database Design

**Every fact should be stored in exactly one place**

### Problems with Poor Design

#### 1. Update Problem
If the same information is stored in multiple places, updating becomes difficult and error-prone.

Example: Anna's email appears twice in different rows - if you update one but not the other, data becomes inconsistent.

#### 2. Delete Problem  
Deleting a record might accidentally remove important information if it's referenced elsewhere.

#### 3. Insert Problem
You can't add new records until they have related data.

### Relationships Between Tables

#### One-to-One (1:1)
- One person has one passport
- Rare but exists

#### One-to-Many (1:N)  
- One customer can have many orders
- Most common relationship type

#### Many-to-Many (N:M)
- One order can contain many products
- One product can be in many orders

### Solution: Middle Tables
Many-to-many relationships are solved by creating a middle table that breaks the complex relationship into simple one-to-many relationships.

Example: 
- Customers → Orders (1:N)
- Orders → Order_Items (1:N) 
- Order_Items → Products (1:N)

## Entity Relationship Diagram (ERD)

### The Rule
An ER diagram is a visual map showing:
- Tables (boxes)
- Columns (inside boxes)
- Relationships (lines between tables)

### Key Elements
- Draw lines showing relationships
- Add "1" and "N" at the ends of lines to show relationship types
- Always draw the map before building the database

### Why It Matters
ER diagrams help you plan your database structure before implementation, making it easier to modify than changing an existing database.

## Normalization

### The Rule
Normalization is the process of organizing data to minimize redundancy and improve data integrity.

### Three Normal Forms

#### First Normal Form (1NF)
**Rule**: Every column must contain only one value - no lists in cells.

**Example of Violation**: 
```
Product: "Joggers, Cap, Hoodie"  -- This violates 1NF
```

**Solution**: Create separate rows for each item:
```
Order Item 1: Product = Joggers
Order Item 2: Product = Cap  
Order Item 3: Product = Hoodie
```

#### Second Normal Form (2NF)
**Rule**: Every non-key column must depend on the entire primary key.

**Example Problem**: 
If your `order_items` table has both `order_id` and `product_id` as keys, but also stores `product_name`, that's wrong because `product_name` only depends on `product_id`, not the full key.

**Solution**: Move `product_name` to a separate `products` table.

#### Third Normal Form (3NF)
**Rule**: No non-key column should depend on another non-key column.

**Example Problem**:
If your orders table has both `customer_id` and `customer_city`, but city depends on customer, not order, this violates 3NF.

**Solution**: Move `city` to the `customers` table.

### The Test
For any column, ask: "If this value changes, do I have to change many rows?" If yes, it's probably in the wrong table.

### Design Recipe
1. Read the requirements and underline important nouns (these become tables)
2. Determine how tables are connected
3. For many-to-many relationships, add a middle table
4. Draw the ER diagram with pen and paper first
5. Only then write the actual SQL

## Practical Example: Library Database Design

### Step 1: Identify Nouns
- Library
- Books  
- Members
- Loans

### Step 2: Define Relationships
- One member can have many loans (1:N)
- One book can have many loans (1:N) 
- This creates a many-to-many relationship between members and books

### Step 3: Create Tables

#### Members Table
```sql
CREATE TABLE members (
    member_id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT UNIQUE NOT NULL
);
```

#### Books Table  
```sql
CREATE TABLE books (
    book_id INTEGER PRIMARY KEY,
    title TEXT NOT NULL,
    author TEXT,
    year INTEGER
);
```

#### Loans Table (Middle Table)
```sql
CREATE TABLE loans (
    loan_id INTEGER PRIMARY KEY,
    member_id INTEGER NOT NULL,
    book_id INTEGER NOT NULL,
    loan_date TEXT NOT NULL,
    return_date TEXT,
    
    FOREIGN KEY (member_id) REFERENCES members(member_id),
    FOREIGN KEY (book_id) REFERENCES books(book_id)
);
```

### Step 4: Test Data
```sql
INSERT INTO members VALUES (1, 'Leo', 'leo@gmail.com');
INSERT INTO members VALUES (2, 'Mira', 'mira@gmail.com');

INSERT INTO books VALUES (1, 'The Hobbit', 'J.R.R. Tolkien', 1937);
INSERT INTO books VALUES (2, 'Matilda', 'Roald Dahl', 1989);

INSERT INTO loans VALUES (1, 1, 1, '2026-01-01', '2026-01-15');
INSERT INTO loans VALUES (2, 2, 1, '2026-01-02', NULL);
```

### Step 5: Query Examples
```sql
-- Find currently borrowed books:
SELECT * FROM loans WHERE return_date IS NULL;

-- Find all books borrowed by a member:
SELECT b.title FROM books b 
JOIN loans l ON b.book_id = l.book_id 
WHERE l.member_id = 1;
```

## Summary of Key Concepts

### CRUD Operations (Create, Read, Update, Delete)
- **INSERT**: Add new data
- **SELECT**: Retrieve data  
- **UPDATE**: Modify existing data
- **DELETE**: Remove data

### Safety Rules
1. Always use `WHERE` clauses with updates and deletes
2. Test your `WHERE` conditions with a `SELECT` first
3. Use transactions for related changes
4. Commit or rollback changes appropriately

### Database Design Principles
1. Store each fact in exactly one place
2. Use proper relationships (1:1, 1:N, N:M)
3. Create middle tables for many-to-many relationships
4. Draw ER diagrams before implementation
5. Follow normalization rules (1NF, 2NF, 3NF)

### Important Gotchas
- Forgetting `WHERE` in updates/deletes can destroy your entire database
- Foreign key constraints prevent accidental data loss
- Always test with `SELECT` before making destructive changes
- Use `COMMIT` and `ROLLBACK` appropriately to maintain data integrity