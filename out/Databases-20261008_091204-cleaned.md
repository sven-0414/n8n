## Introduction to SQL Joins in Python

In Python, when working with databases, you often need to combine data from multiple tables. This is where SQL joins come in handy. Joins allow you to combine data from different tables based on a related column, such as an `ID` or a `customer_id`.

### Understanding Joins

#### Definition
A join is a way to link two or more tables based on a related column (e.g., `customer_id`), creating a new result set that includes the combined data.

#### Types of Joins
- **Inner Join**: Returns records that have matching values in both tables.
- **Left Join**: Returns all records from the left table and matching records from the right table. If there is no match, the result is `NULL`.

#### Why Joins Matter
Joins are crucial for integrating data from multiple tables into a single view, allowing you to create comprehensive reports and queries that span across different data sets.

### Practical Example of Joins

#### Inner Join Example
Let's see how to use an inner join to combine the `orders` and `customers` tables based on the `customer_id` column.

```python
import sqlite3

# Connect to the database
conn = sqlite3.connect('webshop.db')
cursor = conn.cursor()

# Query to join orders and customers
query = """
SELECT orders.order_id, customers.first_name
FROM orders
JOIN customers
ON orders.customer_id = customers.customer_id;
"""

# Execute the query
cursor.execute(query)
results = cursor.fetchall()

# Print the results
for row in results:
    print(row)

# Close the connection
conn.close()
```

#### Explanation
- **SQL Query**: The `JOIN` clause is used to combine rows from the `orders` table with rows from the `customers` table based on a related column (`customer_id`).
- **Output**: The result will include `order_id` and `first_name` of the customers, only for those customers who have placed orders.

### Advanced Join Usage

#### Left Join Example
A left join includes all records from the left table (here, `customers`) and the matched records from the right table (here, `orders`). If there is no match, the result is `NULL`.

```python
# Query to perform a left join
query = """
SELECT customers.first_name, orders.order_id
FROM customers
LEFT JOIN orders
ON customers.customer_id = orders.customer_id;
"""

# Execute the query
cursor.execute(query)
results = cursor.fetchall()

# Print the results
for row in results:
    print(row)
```

#### Explanation
- **Output**: This query will show all customers, including those who haven't placed any orders. Orders without corresponding customers will result in `NULL` in the output.

### Joining Multiple Tables

#### Example with Four Tables
Let's see how to join `orders`, `customers`, `order_items`, and `products` tables.

```python
# Query to join four tables
query = """
SELECT customers.first_name, orders.order_date, products.name, order_items.quantity
FROM order_items
JOIN orders ON order_items.order_id = orders.order_id
JOIN customers ON orders.customer_id = customers.customer_id
JOIN products ON order_items.product_id = products.product_id;
"""

# Execute the query
cursor.execute(query)
results = cursor.fetchall()

# Print the results
for row in results:
    print(row)
```

#### Explanation
- **SQL Query**: This query joins multiple tables to create a comprehensive view of orders, including customer details, order date, product names, and quantities.
- **Output**: The result set will include information from all four tables, showing who ordered what and when.

### Conclusion
Joins are essential for combining data across multiple tables in a database. Understanding how to use inner joins and left joins effectively can significantly enhance your ability to query and analyze data in Python.

This covers the basics of SQL joins in the context of Python and working with databases.