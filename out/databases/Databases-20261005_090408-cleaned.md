# Python Database Concepts and SQL Basics

## What is a Database?

A database is a structured collection of data that is stored and organized in a way that allows for efficient access, management, and updating. Think of it like a digital filing cabinet where information is stored systematically so it can be easily retrieved when needed.

### Key Characteristics:
- **Organized storage**: Data is kept in an organized manner
- **Persistence**: Information remains even when the computer is turned off
- **Structured format**: Data follows specific rules and formats
- **Access control**: Can be accessed by multiple applications simultaneously

### Why Use Databases Instead of Excel?
1. **Performance**: Databases handle millions of rows efficiently, while Excel becomes slow with large datasets
2. **Concurrency**: Multiple users can access and modify database information without conflicts
3. **Data integrity**: Databases enforce rules to prevent invalid data entry
4. **Query capabilities**: Complex searches can be performed with simple commands

### Real-world Examples:
- Spotify uses databases to store playlists and user preferences
- BankID systems use databases to verify user identities
- Mobile apps rely on databases for their functionality

## Database Structure

### Tables
A database is made up of tables, which are similar to spreadsheets or class lists. Each table represents one type of information.

**Example Table Structures:**
- Customer table (contains customer information)
- Product table (contains product details)  
- Order table (contains order information)

Each table focuses on one specific kind of data.

### Rows and Columns
- **Row**: Represents a single record or instance of the data type
  - Example: One customer's information
- **Column**: Represents a specific attribute or piece of information about each row
  - Example: City column for all customers

**Memory Aid**: Think of rows as individual people and columns as questions asked about everyone.

### Data Types
Every column has a specific data type that determines what kind of information it can hold:

```sql
-- Integer: Whole numbers (1, 2, 3)
-- Real/Float: Numbers with decimals (1.5, 3.14)
-- Text/String: Words and characters ("Hello", "John")
-- Null: Represents unknown or missing data
```

### Primary Key
A primary key is a special column that uniquely identifies each row in a table:

```sql
-- Example of a customer table with primary key
CREATE TABLE customers (
    customer_id INTEGER PRIMARY KEY,
    first_name TEXT,
    last_name TEXT,
    city TEXT
);
```

**Key Points:**
- Every row must have a unique value in the primary key column
- No two rows can have the same primary key value
- Primary keys are essential for identifying specific records
- Common examples: customer ID, product ID, employee number

## SQL Language

SQL (Structured Query Language) is the standard language used to communicate with databases. It's designed to be readable like English.

### Basic SQL Syntax Structure:
```sql
SELECT column1, column2, ... 
FROM table_name
WHERE condition
ORDER BY column_name [ASC|DESC]
LIMIT number_of_rows;
```

### The SELECT Recipe
SQL queries follow a specific order of operations:

1. **SELECT** - Specifies which columns to retrieve
2. **FROM** - Identifies the table(s) to query  
3. **WHERE** - Filters rows based on conditions (optional)
4. **ORDER BY** - Sorts results (optional)
5. **LIMIT** - Restricts number of returned rows (optional)

### Key Rules for SQL:
1. **Text values must be enclosed in single quotes**: `'John'`
2. **Numbers do not require quotes**: `100`, `3.14`
3. **Every query must end with a semicolon**: `;`
4. **Error messages are helpful**: Read them carefully to understand what went wrong

## Working with DB Browser for SQLite

### Installation and Setup
- Download "DB Browser for SQLite" (free tool)
- Works on Windows, Mac, and Linux
- File format: `.db` extension (e.g., `webshop.db`)

### Basic Interface Navigation:
1. **Database Structure Tab**: Shows table schemas and relationships
2. **Browse Data Tab**: Displays actual data in tables  
3. **Execute SQL Tab**: Where you write and run SQL queries

### Example Database Structure:
```sql
-- Customer Table
customer_id (INTEGER, PRIMARY KEY)
first_name (TEXT)
last_name (TEXT)
city (TEXT)
joined_date (TEXT)

-- Product Table  
product_id (INTEGER, PRIMARY KEY)
name (TEXT)
category (TEXT)
price (REAL)
stock (INTEGER)
```

## SQL Query Types and Examples

### Basic SELECT Queries
```sql
-- Select all columns from a table
SELECT * FROM customers;

-- Select specific columns
SELECT first_name, last_name, city FROM customers;

-- Select with column aliases
SELECT first_name AS "First Name", last_name AS "Last Name" FROM customers;
```

### Filtering Data with WHERE Clause

```sql
-- Filter by exact match
SELECT * FROM customers WHERE city = 'Uppsala';

-- Filter numeric values
SELECT name, price FROM products WHERE price < 500;

-- Combine multiple conditions with AND
SELECT name, category, price 
FROM products 
WHERE category = 'Clothing' AND price < 500;

-- Combine conditions with OR
SELECT name, category 
FROM products 
WHERE category = 'Shoes' OR category = 'Accessories';

-- Using IN operator for multiple values
SELECT name, category 
FROM products 
WHERE category IN ('Shoes', 'Accessories');
```

### Pattern Matching with LIKE

```sql
-- Find items starting with specific letter
SELECT name FROM products WHERE name LIKE 'S%';

-- Find items containing specific pattern
SELECT name FROM products WHERE name LIKE '%shoe%';
```

### Sorting Results with ORDER BY

```sql
-- Sort ascending (default)
SELECT name, price FROM products ORDER BY price ASC;

-- Sort descending  
SELECT name, price FROM products ORDER BY price DESC;

-- Multiple sorting criteria
SELECT first_name, last_name, city FROM customers ORDER BY city, last_name;
```

### Limiting Results with LIMIT

```sql
-- Get only top 3 results
SELECT name, price FROM products ORDER BY price DESC LIMIT 3;

-- Get first 5 rows
SELECT * FROM customers LIMIT 5;
```

## Advanced SQL Concepts

### DISTINCT Keyword
The DISTINCT keyword removes duplicate values from query results:

```sql
-- Get unique cities (no duplicates)
SELECT DISTINCT city FROM customers;

-- Count distinct values
SELECT COUNT(DISTINCT city) FROM customers;
```

### Handling NULL Values
NULL represents unknown or missing data in databases:

```sql
-- Find records where column is NULL
SELECT * FROM customers WHERE city IS NULL;

-- Find records where column is NOT NULL  
SELECT * FROM customers WHERE city IS NOT NULL;

-- NULL comparison behavior:
-- NULL = NULL returns FALSE (unknown)
-- Use IS NULL to check for null values
```

### Using Column Aliases
Column aliases provide more readable output without changing the actual table structure:

```sql
-- Rename columns in results
SELECT name AS product_name, price AS "Price in SEK" FROM products;
```

## Common SQL Operations and Patterns

### Combining Multiple Conditions
```sql
-- AND operator (both conditions must be true)
SELECT * FROM products WHERE category = 'Clothing' AND price > 100;

-- OR operator (either condition can be true)  
SELECT * FROM products WHERE category = 'Shoes' OR category = 'Accessories';

-- BETWEEN operator (inclusive range)
SELECT name, price FROM products WHERE price BETWEEN 200 AND 600;
```

### Date and Time Handling
Dates are typically stored as text in YYYY-MM-DD format:

```sql
-- Filter by date range
SELECT first_name, joined_date FROM customers WHERE joined_date >= '2025-01-01';

-- Sort by date
SELECT name, joined_date FROM customers ORDER BY joined_date DESC;
```

## Best Practices and Gotchas

### Common Mistakes to Avoid:
1. **Missing semicolons**: Every query must end with `;`
2. **Incorrect quoting**: Text values must be in single quotes
3. **Case sensitivity**: SQL keywords are typically case-insensitive but column names might be
4. **Column name typos**: Misspelled column names cause "no such column" errors
5. **Missing commas**: Forgetting commas between column names breaks queries

### Error Handling:
- Read error messages carefully - they're helpful guides
- Always check the results, not just whether there are errors
- When in doubt, test simple queries first

### File Management:
- Save SQL queries with `.sql` extension for future reference
- Use comments (`--`) to document your code
- Keep track of query history in the SQL log

## Practical Exercise Examples

### Basic Data Retrieval:
```sql
-- View all customer information
SELECT * FROM customers;

-- Get specific customer details  
SELECT first_name, last_name, city FROM customers WHERE city = 'Stockholm';
```

### Advanced Filtering:
```sql
-- Find expensive products in multiple categories
SELECT name, price, category 
FROM products 
WHERE (category = 'Clothing' OR category = 'Accessories') 
AND price > 300;

-- Get top 5 most expensive products
SELECT name, price FROM products ORDER BY price DESC LIMIT 5;
```

### Data Analysis:
```sql
-- Count customers by city
SELECT city, COUNT(*) as customer_count 
FROM customers 
GROUP BY city;

-- Find unique cities with null values
SELECT DISTINCT city FROM customers WHERE city IS NULL;
```

## Key Takeaways

1. **Databases organize information systematically** - Like a well-organized filing cabinet
2. **SQL is English-like language** - Makes queries readable and intuitive  
3. **Primary keys ensure uniqueness** - Essential for identifying specific records
4. **Proper syntax is crucial** - Missing semicolons, quotes, or commas cause errors
5. **Practice with real examples** - Working with actual data helps understanding

The database concepts covered today form the foundation for all future database work. Understanding tables, rows, columns, and SQL queries will enable you to interact with databases effectively in any programming context.