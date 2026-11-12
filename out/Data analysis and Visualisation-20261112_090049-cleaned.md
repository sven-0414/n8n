# Data Cleaning and Joining in Python

## Understanding Missing Values

### What Are Missing Values?

Missing values are entries in a dataset that are not present or have no value assigned. These can occur for various reasons like user non-response, data collection errors, or system failures. In Python with pandas, missing values are typically represented as `NaN` (Not a Number) or `None`.

### How to Detect Missing Values

You can identify missing values using the `isnull()` and `isna()` methods in pandas, which return boolean masks indicating where values are missing.

```python
import pandas as pd
import numpy as np

# Sample DataFrame with missing values
data = {
    'name': ['Alice', 'Bob', 'Charlie', 'David'],
    'age': [25, np.nan, 30, 28],
    'city': ['New York', 'London', np.nan, 'Paris']
}
df = pd.DataFrame(data)

# Detect missing values
print(df.isnull())
print(df.isna())
```

### Counting Missing Values

To get a summary of missing values per column:

```python
# Count missing values per column
print(df.isnull().sum())

# Total number of missing values in the entire DataFrame
print(df.isnull().sum().sum())
```

### Why Missing Values Matter

Missing values can significantly affect data analysis and modeling results. They can lead to biased conclusions, reduce statistical power, or cause errors in computations. Understanding and handling them properly is crucial for accurate data interpretation.

## Strategies for Handling Missing Values

### 1. Removal of Rows/Columns

Sometimes the simplest approach is removing missing values entirely using `dropna()`:

```python
# Remove rows with any missing values
df_cleaned = df.dropna()

# Remove columns with any missing values
df_cleaned = df.dropna(axis=1)

# Remove rows where specific column has missing values
df_cleaned = df.dropna(subset=['age'])
```

### 2. Imputation

Imputation involves filling missing values with calculated estimates:

```python
# Fill with a constant value
df_filled = df.fillna(0)

# Fill with mean of the column
df['age'].fillna(df['age'].mean(), inplace=True)

# Fill with median
df['age'].fillna(df['age'].median(), inplace=True)

# Forward fill (carry forward last valid observation)
df_filled = df.fillna(method='ffill')

# Backward fill (carry backward next valid observation)
df_filled = df.fillna(method='bfill')
```

### 3. Advanced Imputation Techniques

For more sophisticated approaches, you can use:
- Interpolation methods
- Predictive models to estimate missing values
- Using domain knowledge to make informed guesses

### Decisions Around Missing Values

There's no universal rule for handling missing data. The decision depends on:
- Amount of missing data (percentage)
- Pattern of missingness (random vs systematic)
- Importance of the variable
- Available computational resources
- Business context and requirements

## Data Joining Concepts

### What Is Data Joining?

Data joining is the process of combining information from multiple datasets based on a common column or key. This technique allows you to create comprehensive views of your data by merging tables that contain related but separate pieces of information.

### Types of Joins

#### 1. Inner Join

An inner join returns only rows where matching records exist in both tables:

```python
import pandas as pd

# Sample customer data
customers = pd.DataFrame({
    'customer_id': [1, 2, 3, 4],
    'name': ['Alice', 'Bob', 'Charlie', 'David'],
    'city': ['New York', 'London', 'Tokyo', 'Paris']
})

# Sample order data
orders = pd.DataFrame({
    'order_id': [101, 102, 103, 104],
    'customer_id': [1, 2, 3, 5],  # Note: customer_id 5 doesn't exist in customers table
    'product': ['Laptop', 'Phone', 'Tablet', 'Watch']
})

# Inner join on customer_id
result = pd.merge(customers, orders, on='customer_id', how='inner')
print(result)
```

#### 2. Left Join

A left join returns all rows from the left table and matching rows from the right table:

```python
# Left join - keeps all customers even if they have no orders
result = pd.merge(customers, orders, on='customer_id', how='left')
print(result)
```

#### 3. Right Join

A right join returns all rows from the right table and matching rows from the left table:

```python
# Right join - keeps all orders even if customer doesn't exist in customers table
result = pd.merge(customers, orders, on='customer_id', how='right')
print(result)
```

#### 4. Outer Join

An outer join returns all rows from both tables:

```python
# Outer join - keeps all records from both tables
result = pd.merge(customers, orders, on='customer_id', how='outer')
print(result)
```

### Key Considerations for Joins

#### Matching Keys Must Be Identical

The columns used to join must have the same data types and values. If they don't match exactly (including case sensitivity), the join will fail or produce unexpected results.

```python
# Example of problematic join due to mismatched data types
customers['customer_id'] = customers['customer_id'].astype(str)
orders['customer_id'] = orders['customer_id'].astype(int)

# This might cause issues with the join
result = pd.merge(customers, orders, on='customer_id', how='left')
```

#### Handling Duplicate Keys

When joining tables with duplicate keys, you may get multiple rows in the result:

```python
# Customers table with duplicate customer_ids (if that were possible)
customers_dup = pd.DataFrame({
    'customer_id': [1, 1, 2],
    'name': ['Alice', 'Alice', 'Bob'],
    'city': ['New York', 'NYC', 'London']
})

orders_dup = pd.DataFrame({
    'order_id': [101, 102],
    'customer_id': [1, 2],
    'product': ['Laptop', 'Phone']
})

# This creates a Cartesian product for matching keys
result = pd.merge(customers_dup, orders_dup, on='customer_id', how='left')
print(result)
```

### Best Practices for Joins

1. **Always verify data types** before joining to ensure compatibility
2. **Check for duplicate keys** that might lead to unexpected result sizes
3. **Use appropriate join types** based on your analytical needs
4. **Handle missing values appropriately** in both tables before joining
5. **Consider performance implications** of large joins

### Practical Example: Combining Orders and Customer Data

```python
# Assuming we have two CSV files:
# orders_clean.csv - contains order information with customer_id
# customers.csv - contains customer details including city and age

# Load the data
orders_df = pd.read_csv('orders_clean.csv')
customers_df = pd.read_csv('customers.csv')

# Join the tables on customer_id
combined_df = pd.merge(orders_df, customers_df, on='customer_id', how='left')

# Now we can analyze questions like:
# - Which city buys the most?
# - What's the average age of customers who bought each product?

city_sales = combined_df.groupby('city')['amount'].sum()
age_analysis = combined_df.groupby('age')['amount'].mean()

print(city_sales)
print(age_analysis)
```

### Performance Considerations

For large datasets, joining operations can be computationally expensive. Some strategies to optimize:
- Ensure indexes are properly set on join columns
- Filter data before joining when possible
- Consider using database tools for very large joins
- Use appropriate data types (integers instead of strings where possible)