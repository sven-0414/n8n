## CRUD Operations in SQL

### Insert Operation
- **Definition**: Insert is used to add new rows of data into a table. You specify the columns and provide the corresponding values.
- **Example**:
    ```sql
    INSERT INTO orders (order_id, customer_id, order_date, status) 
    VALUES (1, 1, '2021-01-02', 'delivered');
    ```
- **Gotchas**:
    - Ensure the order of columns matches the order of values.
    - If a column does not have a default value, a null value will be inserted.
    - You can insert multiple rows at once by listing them in a single `INSERT INTO` statement with multiple value sets separated by commas.
- **Why It Matters**: Insert is fundamental for adding new records to your database.

### Update Operation
- **Definition**: Update modifies existing rows in a table based on specified conditions.
- **Example**:
    ```sql
    UPDATE orders
    SET status = 'delivered'
    WHERE order_id = 9;
    ```
- **Gotchas**:
    - Always include a `WHERE` clause to specify which rows should be updated.
    - Be cautious with updates that lack a `WHERE` clause, as they will affect all rows in the table.
- **Why It Matters**: Update ensures data accuracy and reflects real-world changes (e.g., status updates).

### Delete Operation
- **Definition**: Delete removes rows from a table based on specified conditions.
- **Example**:
    ```sql
    DELETE FROM customers WHERE customer_id = 9;
    ```
- **Gotchas**:
    - Ensure the `WHERE` clause is precise to prevent accidental deletion of data.
    - Pay attention to foreign key constraints; deleting a record with associated data in another table may result in errors.
- **Why It Matters**: Delete allows for the removal of outdated or incorrect data from the database.

## Best Practices for CRUD Operations

### Use WHERE Clause
- **Rule**: Always include a `WHERE` clause in `UPDATE` and `DELETE` statements to prevent unintended modifications.
- **Example**:
    ```sql
    UPDATE orders
    SET status = 'delivered'
    WHERE order_id = 9;
    ```
- **Why It Matters**: Ensures data integrity and avoids accidental database-wide modifications.

### Use SELECT Before UPDATE or DELETE
- **Rule**: Before executing `UPDATE` or `DELETE`, run a `SELECT` query with the same `WHERE` clause to verify which rows will be affected.
- **Why It Matters**: Provides a safety check to prevent data loss or corruption.

### Transactions
- **Definition**: A transaction is a set of SQL operations that must be completed together or not at all.
- **Commit and Rollback**:
    - **Commit**: Finalizes all changes made within the transaction.
    - **Rollback**: Undoes all changes made within the transaction.
- **Example**:
    ```sql
    BEGIN TRANSACTION;
    UPDATE products SET price = price * 0.8 WHERE category = 'shoes';
    COMMIT;
    ```
- **Gotchas**:
    - Always ensure a transaction is completed successfully before committing.
- **Why It Matters**: Transactions ensure data consistency and can roll back changes if errors occur.

## Database Design Concepts

### Entity-Relationship Diagram (ER Diagram)
- **Definition**: An ER diagram visually represents the structure of a database, showing entities and the relationships between them.
- **Why It Matters**: Helps in designing a well-structured database before implementation.

### Normalization
- **First Normal Form (1NF)**:
    - **Rule**: Ensure each column contains atomic (indivisible) values and no repeating groups.
    - **Why It Matters**: Eliminates redundancy and ensures data integrity.
- **Second Normal Form (2NF)**:
    - **Rule**: Ensure the table is in 1NF and every non-key column is fully dependent on the primary key.
    - **Why It Matters**: Removes partial dependency issues.
- **Third Normal Form (3NF)**:
    - **Rule**: Ensure the table is in 2NF and every non-key column is independent of other non-key columns.
    - **Why It Matters**: Eliminates transitive dependency issues.

### One-to-One, One-to-Many, Many-to-Many Relationships
- **Definition**:
    - **One-to-One**: A single instance of one entity is related to a single instance of another entity.
    - **One-to-Many**: A single instance of one entity is related to multiple instances of another entity.
    - **Many-to-Many**: Multiple instances of one entity are related to multiple instances of another entity.
- **Example**:
    - **One-to-One**: A person and their passport.
    - **One-to-Many**: A customer and their orders.
    - **Many-to-Many**: An order and its items.
- **Why It Matters**: Properly defining relationships ensures efficient data management and integrity.

## Lab Exercises
- **Objective**: Implement CRUD operations and apply normalization principles to design a database.
- **Steps**:
    - Create a database schema following ER diagram principles.
    - Implement CRUD operations.
    - Normalize the database to 3NF.
- **Why It Matters**: Practical application reinforces understanding of theoretical concepts.

These notes cover the critical aspects of CRUD operations, database design principles, and transaction management as discussed in the lecture. Use these to reinforce your understanding and practice the concepts through exercises.