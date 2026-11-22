# Python for Data Analysis: Handling Missing Values and Merging Tables

## Understanding Missing Values

### What are missing values?

Missing values in datasets represent data that is absent or not recorded. They can occur for various reasons such as user non-response, system errors, or incomplete data collection. In Python with pandas, missing values are typically represented as `NaN` (Not a Number) or `None`.

### Code example

```python
import pandas as pd
import numpy as np

# Create sample DataFrame with missing values
data = {
    'customer_id': [1, 2, 3, 4, 5],
    'coupon_code': ['ABC123', None, 'XYZ789', None, 'DEF456'],
    'rating': [4.5, np.nan, 3.0, 5.0, np.nan],
    'date': ['2023-01-01', '2023-01-02', None, '2023-01-04', '2023-01-05']
}
df = pd.DataFrame(data)
print(df)
```

### Key points about missing values

- Missing values in pandas are represented as `NaN` (Not a Number) or `None`
- `NaN` is not equal to itself (`np.nan != np.nan`)
- When you have mixed data types, pandas often converts everything to object dtype
- Different functions handle missing values differently:
  - `sum()` and `mean()` ignore NaN by default
  - `count()` counts non-null values
  - `isnull()` and `notnull()` are used for detection

### Why this matters

Missing values can significantly affect analysis results. They may introduce bias, reduce statistical power, or lead to incorrect conclusions if not properly handled. Understanding how to detect and decide what to do with missing data is crucial for robust data analysis.

## Detecting Missing Values

### How to identify missing values in your dataset

The most common way to detect missing values is using the `isnull()` method which returns a boolean DataFrame where True indicates a missing value, or using `isna()` which is an alias for `isnull()`.

### Code example

```python
import pandas as pd
import numpy as np

# Sample dataset with missing values
data = {
    'order_id': [1001, 1002, 1003, 1004],
    'customer_id': [1, 2, None, 4],
    'product_name': ['Laptop', None, 'Phone', 'Tablet'],
    'price': [1000.0, np.nan, 500.0, 300.0]
}
df = pd.DataFrame(data)

# Detect missing values
print("Missing values per column:")
print(df.isnull().sum())

print("\nBoolean mask of missing values:")
print(df.isnull())

print("\nColumns with any missing values:")
print(df.columns[df.isnull().any()].tolist())
```

### Gotchas and edge cases

- `isnull()` and `notnull()` work on the entire DataFrame at once
- Empty strings ('') are not considered NaN by default in pandas, but may be treated as missing in certain contexts
- The distinction between `NaN`, `None`, and empty strings can be important depending on your data source
- When using `fillna()`, you must consider whether to fill with a specific value or use forward/backward fill strategies

## Strategies for Handling Missing Values

### Various approaches to deal with missing values

There are several strategies for handling missing values:
1. **Remove rows/columns** containing missing data (using `dropna()`)
2. **Fill missing values** with statistical measures (mean, median, mode)
3. **Forward fill or backward fill** (using `fillna()` with method parameter)
4. **Use indicator variables** to mark missing values as a separate category
5. **Model-based imputation** (advanced technique)

### Code example

```python
import pandas as pd
import numpy as np

# Sample dataset with missing values
data = {
    'order_id': [1001, 1002, 1003, 1004, 1005],
    'customer_id': [1, 2, None, 4, 5],
    'product_name': ['Laptop', None, 'Phone', 'Tablet', None],
    'price': [1000.0, np.nan, 500.0, 300.0, 800.0]
}
df = pd.DataFrame(data)

print("Original DataFrame:")
print(df)

# Strategy 1: Drop rows with any missing values
df_dropped = df.dropna()
print("\nAfter dropping rows with missing values:")
print(df_dropped)

# Strategy 2: Fill missing values with mean (for numeric columns only)
df_filled_mean = df.copy()
df_filled_mean['price'].fillna(df_filled_mean['price'].mean(), inplace=True)
print("\nAfter filling price column with mean:")
print(df_filled_mean)

# Strategy 3: Fill missing values with a specific value
df_filled_value = df.copy()
df_filled_value['customer_id'].fillna(0, inplace=True)
print("\nAfter filling customer_id with 0:")
print(df_filled_value)
```

### Why this matters

Choosing the right strategy for handling missing data is crucial because:
- Removing too many rows can lead to loss of valuable information
- Filling with inappropriate values can skew your results
- Different strategies may be appropriate depending on whether data is missing completely at random, missing at random, or missing not at random
- The decision impacts the validity and reliability of downstream analysis

## Merging DataFrames

### What is merging in pandas?

Merging in pandas combines two or more DataFrames based on common columns (keys) to create a single DataFrame. This operation allows you to combine related information from different sources into one cohesive dataset for analysis.

### Code example

```python
import pandas as pd

# Sample orders data
orders = pd.DataFrame({
    'order_id': [1001, 1002, 1003],
    'customer_id': [1, 2, 3],
    'product_name': ['Laptop', 'Phone', 'Tablet'],
    'quantity': [1, 2, 1]
})

# Sample customer data
customers = pd.DataFrame({
    'customer_id': [1, 2, 3, 4],
    'city': ['New York', 'Los Angeles', 'Chicago', 'Miami'],
    'age': [25, 30, 35, 28]
})

print("Orders DataFrame:")
print(orders)
print("\nCustomers DataFrame:")
print(customers)

# Merge the two DataFrames on customer_id
merged_df = pd.merge(orders, customers, on='customer_id', how='inner')
print("\nMerged DataFrame (inner join):")
print(merged_df)
```

### Types of joins in merging

- **Inner Join**: Returns only rows that have matching values in both DataFrames
- **Left Join**: Returns all rows from the left DataFrame and matching rows from the right
- **Right Join**: Returns all rows from the right DataFrame and matching rows from the left
- **Outer Join**: Returns all rows from both DataFrames

### Code example with different join types

```python
import pandas as pd

# Sample data for demonstration
orders = pd.DataFrame({
    'order_id': [1001, 1002, 1003],
    'customer_id': [1, 2, 3],
    'product_name': ['Laptop', 'Phone', 'Tablet']
})

customers = pd.DataFrame({
    'customer_id': [1, 2, 4],  # Note: customer_id=4 is in customers but not orders
    'city': ['New York', 'Los Angeles', 'Miami'],
    'age': [25, 30, 28]
})

# Inner join (default)
inner_join = pd.merge(orders, customers, on='customer_id', how='inner')
print("Inner Join:")
print(inner_join)

# Left join
left_join = pd.merge(orders, customers, on='customer_id', how='left')
print("\nLeft Join:")
print(left_join)

# Right join
right_join = pd.merge(orders, customers, on='customer_id', how='right')
print("\nRight Join:")
print(right_join)
```

### Key considerations for merging

- **Matching column names**: The columns used for merging must exist in both DataFrames
- **Data types**: Matching columns should have compatible data types
- **Index preservation**: Merging can change the index of the resulting DataFrame
- **Performance**: Large datasets may require optimization techniques like using `suffixes` parameter when column names conflict
- **Memory usage**: Merging operations can be memory-intensive with large datasets

### Why this matters

Merging is essential in real-world data analysis because:
- Real-world data often lives in multiple related tables
- You need to combine information from different sources for comprehensive analysis
- It enables you to answer complex questions that require data from multiple sources
- Proper merging ensures data integrity and avoids duplication or loss of information

## Combining Cleaning Steps with Merging

### How cleaning and merging work together

In practice, you'll often need to clean both DataFrames before merging them. This includes:
1. Handling missing values in both datasets
2. Standardizing column names and data types
3. Removing duplicates
4. Ensuring consistent formatting (dates, currencies, etc.)

### Code example

```python
import pandas as pd
import numpy as np

# Sample raw data with inconsistencies
orders_raw = pd.DataFrame({
    'order_id': [1001, 1002, 1003, 1004],
    'customer_id': [1, 2, None, 4],  # Missing customer ID
    'product_name': ['Laptop', 'Phone', 'Tablet', 'Monitor'],
    'quantity': [1, 2, 1, 1]
})

customers_raw = pd.DataFrame({
    'customer_id': [1, 2, 3, 4],
    'city': ['New York', 'Los Angeles', 'Chicago', 'Miami'],
    'age': [25, 30, 35, 28]
})

print("Raw Orders DataFrame:")
print(orders_raw)
print("\nRaw Customers DataFrame:")
print(customers_raw)

# Step 1: Clean the orders data (handle missing values)
orders_clean = orders_raw.copy()
orders_clean = orders_clean.dropna(subset=['customer_id'])  # Remove rows without customer_id
orders_clean['customer_id'] = orders_clean['customer_id'].astype(int)  # Ensure integer type

print("\nCleaned Orders DataFrame:")
print(orders_clean)

# Step 2: Merge cleaned data
final_df = pd.merge(orders_clean, customers_raw, on='customer_id', how='inner')
print("\nFinal Merged DataFrame:")
print(final_df)
```

### Why this matters

This combined approach reflects real-world workflows:
- Data quality issues are common in raw datasets
- You need to clean each source before combining
- The merging step ensures all relevant information is brought together
- This process creates a unified view of your data for meaningful analysis

## Best Practices for Handling Missing Values and Merging

### Decision framework for missing values

When deciding how to handle missing values, consider:
1. **Amount of missing data**: If >50%, consider whether the variable is essential
2. **Pattern of missingness**: Are values missing at random or systematically?
3. **Impact on analysis**: Does removing missing data affect your conclusions?
4. **Domain knowledge**: What does it mean if a value is missing in your context?

### Code example with decision making

```python
import pandas as pd
import numpy as np

# Sample dataset for demonstration
data = {
    'order_id': [1001, 1002, 1003, 1004, 1005],
    'customer_id': [1, 2, None, 4, 5],
    'product_name': ['Laptop', None, 'Phone', 'Tablet', None],
    'price': [1000.0, np.nan, 500.0, 300.0, 800.0],
    'rating': [4.5, 3.0, np.nan, 5.0, 4.0]
}
df = pd.DataFrame(data)

print("Original DataFrame:")
print(df)
print("\nMissing value summary:")
print(df.isnull().sum())

# Strategy: For customer_id, remove rows with missing values (critical for merging)
df_cleaned = df.dropna(subset=['customer_id'])

# Strategy: For product_name, fill with 'Unknown' (categorical variable)
df_cleaned['product_name'].fillna('Unknown', inplace=True)

# Strategy: For price, fill with median value
median_price = df_cleaned['price'].median()
df_cleaned['price'].fillna(median_price, inplace=True)

# Strategy: For rating, remove rows with missing values (important for analysis)
df_cleaned = df_cleaned.dropna(subset=['rating'])

print("\nCleaned DataFrame:")
print(df_cleaned)
```

### Performance considerations

When working with large datasets:
- Use `dropna()` with `subset` parameter to avoid unnecessary computations
- Consider using `how='any'` vs `how='all'` for dropping missing values
- For merging, ensure matching columns are indexed appropriately
- Use appropriate data types to minimize memory usage
- Be aware that merging large datasets can be computationally expensive

### Why this matters

These practices ensure:
- Data quality is maintained throughout the analysis process
- Your results are reliable and reproducible
- You make informed decisions about data handling
- Your code performs efficiently with large datasets
- You avoid common pitfalls in data integration and analysis