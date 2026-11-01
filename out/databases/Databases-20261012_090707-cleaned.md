# Python Database Connections with SQLite3

## Overview of Database Connections in Python

### Why Connect to a Database from Python?

Connecting Python to a database allows applications to interact with data programmatically rather than manually through tools like DB Browser. In real-world applications, when users perform actions like purchasing items online, the application needs to communicate with the database to store or retrieve information.

Think of it like a restaurant scenario:
- **User** = Customer
- **Python Application** = Waiter
- **Database** = Kitchen

The Python application (waiter) takes orders from customers and communicates them to the kitchen (database). When the order is ready, the waiter brings it back to the customer.

### Basic Four-Step Process

Every time you connect to a database using Python with SQLite3, you follow this consistent four-step process:

1. **Connect**: Open the door to the database file
2. **Get Cursor**: Create a "pen" that runs SQL commands
3. **Execute**: Run your SQL queries
4. **Fetch**: Get results back from the database
5. **Close**: Close the connection when done

## Understanding SQLite3 Module

### Built-in Functionality

SQLite3 is built into Python, so no additional installation is required. This makes it perfect for learning and development.

```python
import sqlite3

# Connect to database file
con = sqlite3.connect('webshop.db')

# Get cursor object
cursor = con.cursor()

# Execute SQL command
cursor.execute("SELECT name, price FROM products")

# Fetch results
rows = cursor.fetchall()

# Close connection
con.close()
```

### Result Format

Results from database queries come back as a **list of tuples**. Each tuple represents one row from the database:

```python
import sqlite3

con = sqlite3.connect('webshop.db')
cursor = con.cursor()

cursor.execute("SELECT name, price FROM products")
rows = cursor.fetchall()
print(rows)
# Output might look like:
# [('T-shirt', 19.99), ('Jeans', 49.99), ('Jacket', 89.99)]

con.close()
```

## Working with Different Fetch Methods

### fetchall()

Returns a list of all rows from the query result:

```python
import sqlite3

con = sqlite3.connect('webshop.db')
cursor = con.cursor()

cursor.execute("SELECT name, price FROM products")
all_rows = cursor.fetchall()
print(all_rows)

con.close()
```

### fetchone()

Returns only the first row as a tuple:

```python
import sqlite3

con = sqlite3.connect('webshop.db')
cursor = con.cursor()

cursor.execute("SELECT COUNT(*) FROM customers")
first_row = cursor.fetchone()
print(first_row[0])  # Gets just the count value

con.close()
```

## SQL Injection Prevention

### The Critical Danger

**Never use f-strings to insert user input directly into SQL queries.** This creates a vulnerability called SQL injection, which is one of the most common ways websites get hacked.

```python
# DANGEROUS - Never do this!
user_input = "Uppsala"
sql = f"SELECT first_name, last_name FROM customers WHERE city = '{user_input}'"
cursor.execute(sql)
```

### How SQL Injection Works

If a user inputs something like:
- `OR 1=1`
- `'; DROP TABLE customers; --`

The SQL becomes malformed and could potentially execute destructive commands.

```python
# Example of malicious input that would break the query
malicious_input = "Uppsala' OR '1'='1"
# This would effectively make the query:
# SELECT first_name, last_name FROM customers WHERE city = 'Uppsala' OR '1'='1'
```

### The Solution: Placeholders

Always use **placeholders** (question marks) in SQL and pass values separately:

```python
import sqlite3

con = sqlite3.connect('webshop.db')
cursor = con.cursor()

city = "Uppsala"
cursor.execute("SELECT first_name, last_name FROM customers WHERE city = ?", (city,))
results = cursor.fetchall()
print(results)

con.close()
```

### Important Rules for Placeholders

1. **Always include a comma after the value** when creating a tuple:
   ```python
   # Correct way - note the comma after city
   cursor.execute("SELECT * FROM customers WHERE city = ?", (city,))

   # Wrong way - this won't work properly
   cursor.execute("SELECT * FROM customers WHERE city = ?", city)
   ```

2. **Multiple values** can be passed as a tuple:
   ```python
   cursor.execute("SELECT * FROM customers WHERE city = ? AND age = ?", (city, age))
   ```

## Data Modification and Commitment

### Important Concepts for Updates

When you modify data using INSERT, UPDATE, or DELETE statements, you must **commit** your changes to save them permanently.

```python
import sqlite3

con = sqlite3.connect('webshop.db')
cursor = con.cursor()

# Insert new customer
cursor.execute("INSERT INTO customers (first_name, last_name, email) VALUES (?, ?, ?)", 
               ("Anna", "Lindqvist", "anna@example.com"))

# Save changes to database
con.commit()
print("Customer added successfully")

con.close()
```

### Rollback Functionality

If you make changes but want to undo them:

```python
import sqlite3

con = sqlite3.connect('webshop.db')
cursor = con.cursor()

try:
    cursor.execute("UPDATE customers SET email = ? WHERE customer_id = ?", 
                   ("newemail@example.com", 1))
    
    # If everything is okay, commit
    con.commit()
except Exception as e:
    # If there's an error, rollback changes
    con.rollback()
    print(f"Error occurred: {e}")

con.close()
```

## Foreign Key Constraints

### Default Behavior

Python's SQLite3 module does not automatically enforce foreign key constraints like DB Browser does. You must explicitly enable this feature:

```python
import sqlite3

con = sqlite3.connect('webshop.db')
cursor = con.cursor()

# Enable foreign key constraints
cursor.execute("PRAGMA foreign_keys = ON")

# Now foreign keys will be enforced
con.commit()
con.close()
```

### Why This Matters

Without enabling foreign keys, Python would allow you to create invalid data relationships:

```python
# Without PRAGMA foreign_keys = ON
# This would succeed even though customer_id=999 doesn't exist
cursor.execute("INSERT INTO orders (customer_id, product_id) VALUES (999, 1)")
```

## Complete Code Examples

### Reading Data Example

```python
import sqlite3

# Connect to database
con = sqlite3.connect('webshop.db')
cursor = con.cursor()

# Execute query with sorting
cursor.execute("SELECT name, price FROM products ORDER BY price DESC")

# Fetch all results
rows = cursor.fetchall()

# Display results nicely
for name, price in rows:
    print(f"{name}: ${price}")

# Close connection
con.close()
```

### Reading Single Value Example

```python
import sqlite3

con = sqlite3.connect('webshop.db')
cursor = con.cursor()

# Count customers
cursor.execute("SELECT COUNT(*) FROM customers")
count = cursor.fetchone()[0]
print(f"Total customers: {count}")

con.close()
```

### Safe Data Insertion with Placeholders

```python
import sqlite3

con = sqlite3.connect('webshop.db')
cursor = con.cursor()

# Safe way to insert data
first_name = "Anna"
last_name = "Lindqvist"
email = "anna@example.com"

cursor.execute("INSERT INTO customers (first_name, last_name, email) VALUES (?, ?, ?)", 
               (first_name, last_name, email))

con.commit()
print("Customer inserted successfully")
con.close()
```

### Safe Data Filtering with Placeholders

```python
import sqlite3

con = sqlite3.connect('webshop.db')
cursor = con.cursor()

# Safe filtering using placeholders
city = "Uppsala"
cursor.execute("SELECT first_name, last_name FROM customers WHERE city = ?", (city,))
results = cursor.fetchall()

for first_name, last_name in results:
    print(f"{first_name} {last_name}")

con.close()
```

## Practical Considerations

### File Path Issues

When connecting to a database file, it must be in the same directory as your Python script unless you provide the full path:

```python
# If database is in the same folder
con = sqlite3.connect('webshop.db')

# If database is in a different folder
con = sqlite3.connect('/path/to/your/database/webshop.db')
```

### Error Handling

Always implement proper error handling for database operations:

```python
import sqlite3

try:
    con = sqlite3.connect('webshop.db')
    cursor = con.cursor()
    
    cursor.execute("SELECT * FROM customers WHERE customer_id = ?", (1,))
    result = cursor.fetchone()
    
    if result:
        print(f"Customer found: {result}")
    else:
        print("Customer not found")
        
except sqlite3.Error as e:
    print(f"Database error: {e}")
finally:
    if con:
        con.close()
```

## Summary of Best Practices

1. **Always use placeholders** (`?`) for user input in SQL queries
2. **Never concatenate user input directly into SQL strings with f-strings**
3. **Commit changes** when modifying data (INSERT, UPDATE, DELETE)
4. **Enable foreign key constraints** explicitly using `PRAGMA foreign_keys = ON`
5. **Always close connections** when done with database operations
6. **Handle errors appropriately** in production code
7. **Use proper data types and constraints** in your database schema

These principles ensure secure and reliable database interactions in Python applications while preventing common security vulnerabilities like SQL injection attacks.