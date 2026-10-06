## Introduction to Databases and SQL

### What is a Database?
A database is an organized collection of data that is stored and accessed electronically. It provides a structured way to manage and retrieve data efficiently. Databases are used in a variety of applications, including web applications, enterprise systems, and personal computing, to store and manage data.

### Why Use a Database?
Databases are used instead of simple file systems or spreadsheets like Excel for several reasons:
- **Scalability**: Databases can handle large amounts of data efficiently.
- **Consistency**: They ensure that data is consistent across the system.
- **Security**: Databases provide mechanisms for securing data.
- **Concurrency**: Multiple users can access and modify data simultaneously.
- **Efficiency**: They optimize data retrieval and storage.

### Database Components
#### Tables
A table in a database is similar to a class list. Each table represents a specific kind of information. For example, a web shop database might have tables like `Customers`, `Products`, and `Orders`.

#### Rows and Columns
- **Row**: A single entry in a table, representing a specific record. For example, in a `Customers` table, each customer is a row.
- **Column**: A column in a table represents a specific piece of information about each row. For example, a `Customers` table might have columns like `CustomerID`, `FirstName`, `LastName`, and `City`.

### Data Types
Each column in a table has a data type which defines the kind of data it can hold:
- `Integer`: Whole numbers (e.g., 1, 2, 3).
- `Real`: Floating-point numbers (e.g., 1.5, 2.7).
- `Text`: Strings of characters.
- `Null`: Special value indicating unknown or missing data.

### Primary Key
A primary key is a column (or a combination of columns) that uniquely identifies each row in a table. It must have a unique value for each row and cannot contain null values.

Example of a primary key:
```sql
CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,
    FirstName TEXT,
    LastName TEXT,
    City TEXT
);
```

### SQL Language
SQL (Structured Query Language) is used to interact with databases. It allows you to query, insert, update, and manipulate data.

#### Select Statement
The `SELECT` statement is used to retrieve data from a database. Basic syntax:
```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

Example:
```sql
SELECT FirstName, LastName, City
FROM Customers
WHERE City = 'Uppsala';
```

### SQLite and DB Browser
SQLite is a lightweight database that stores its data in a single file. DB Browser for SQLite is a tool to view and edit SQLite databases.

#### Installation
To install DB Browser for SQLite:
1. Visit the official website: <https://sqlitebrowser.org/>
2. Download the version for your operating system.
3. Install and open the application.

### Basic SQL Operations
#### Filtering Data
Use the `WHERE` clause to filter data based on conditions.

Example:
```sql
SELECT Name, Price
FROM Products
WHERE Price < 500;
```

#### Sorting Data
Use the `ORDER BY` clause to sort data.

Example:
```sql
SELECT Name, Price
FROM Products
ORDER BY Price DESC;
```

#### Limiting Results
Use the `LIMIT` clause to limit the number of rows returned.

Example:
```sql
SELECT Name, Price
FROM Products
ORDER BY Price DESC
LIMIT 3;
```

### Null Values
When dealing with null values:
```sql
SELECT * 
FROM Customers 
WHERE City IS NULL;
```

### Practical Example: Creating a Database and Table
1. Open DB Browser for SQLite.
2. Create a new database and table.

```sql
CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,
    FirstName TEXT,
    LastName TEXT,
    City TEXT
);
```

3. Insert sample data:
```sql
INSERT INTO Customers (CustomerID, FirstName, LastName, City) 
VALUES (1, 'Anna', 'Lindqvist', 'Uppsala'),
       (2, 'Sven', 'Nilsson', 'Stockholm'),
       (3, 'Linda', 'Andersson', NULL);
```

### Conclusion
Today's session covered the basics of databases, SQL, and how to use SQLite with DB Browser for SQLite. You learned how to structure data, query databases, and handle various data types and conditions. Continue to practice these concepts with the provided exercises and explore more advanced SQL features.