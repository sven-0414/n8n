## Connecting Python to a Database

Python provides several methods to connect and interact with databases, including SQLite3, which is often built into Python installations. When connecting to a database, follow these four steps:

1. **Connect**: Open a connection to the database file.
2. **Get a Cursor**: This is an object that allows SQL commands to be executed.
3. **Execute**: Run the SQL commands.
4. **Fetch**: Retrieve the results of the SQL execution.
5. **Close**: Close the connection to the database.

### Example of Connecting to SQLite3

```python
import sqlite3

# Connect to the SQLite database
connection = sqlite3.connect('webshop.db')

# Create a cursor object
cursor = connection.cursor()

# Execute SQL command
cursor.execute("SELECT name, price FROM products")

# Fetch results
rows = cursor.fetchall()

# Print results
for row in rows:
    print(row)

# Close the connection
connection.close()
```

### Fetching Results
- `fetchone()`: Fetches one row at a time.
- `fetchall()`: Fetches all rows at once and returns them as a list of tuples.

### Safety Concerns and SQL Injection
When incorporating user input into SQL queries, use placeholders instead of directly embedding user input to prevent SQL injection attacks.

#### Example of Using Placeholders to Prevent SQL Injection

```python
cursor.execute("SELECT first_name, last_name FROM customers WHERE city=?", ('Uppsala',))
rows = cursor.fetchall()
for row in rows:
    print(row)
```

## Personal Data and GDPR

### Definition of Personal Data
Personal data includes any information that can be used to identify a living individual, directly or indirectly. This can range from obvious details like name and email to less obvious identifiers like unique characteristics or combinations of data.

### Sensitive Personal Data
Sensitive personal data includes health, political opinions, religious beliefs, sexual orientation, union membership, fingerprints, and DNA.

### GDPR Principles
- **Purpose Limitation**: Data should only be used for the purpose it was collected.
- **Data Minimization**: Collect only the data that is absolutely necessary.
- **Accuracy**: Ensure data is accurate and up-to-date.
- **Storage Limitation**: Delete data when it is no longer needed.
- **Security**: Protect data from unauthorized access and leaks.

### Implementing GDPR in Python and SQL
- **Limit Access**: Provide only necessary access to data.
- **Anonymization**: Remove identifiers and anonymize data when possible.
- **Views and Joins**: Use views to limit access and protect sensitive data.
- **Backup and Security**: Ensure backups are also secure and follow the same rules as live data.

## NoSQL vs SQL

### SQL
- **Structured**: Data is stored in tables with fixed columns.
- **RDBMS**: Relational databases use relationships and foreign keys.
- **Operations**: Supports operations like `SELECT`, `JOIN`, `GROUP BY`.

### NoSQL
- **Flexible**: Data is stored in documents (often JSON-like).
- **Unstructured**: Documents can have variable fields.
- **Scalability**: Often more scalable for certain applications, especially those requiring rapid growth.

### Example of NoSQL (MongoDB) Structure
```json
{
    "order_id": 1,
    "customer": "Anna",
    "items": [
        {
            "product": "Hoodie",
            "quantity": 1
        }
    ]
}
```

These notes provide a comprehensive overview of connecting Python to databases, preventing SQL injection, handling personal data according to GDPR, and an introduction to NoSQL databases.