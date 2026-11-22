# Data Cleaning in Python

## Introduction to Data Cleaning

Data cleaning is a crucial part of data analysis that involves identifying and correcting errors or inconsistencies in datasets. Every real dataset requires cleaning before it can be trusted for analysis.

### Why Data Cleaning Matters

- **Trust in Results**: Clean data produces reliable answers to questions
- **Decision Making**: The decisions made during cleaning determine what your numbers mean
- **Data Quality**: Real-world data is rarely perfect and contains various types of problems

## Setting Up the Environment

We'll be working with a sample dataset called `orders.csv` that simulates an online shop's transaction data.

### Loading Data

```python
import pandas as pd

# Load original data
orders_raw = pd.read_csv('data/orders.csv')

# Create a copy for cleaning
orders = orders_raw.copy()

# Check the shape of the data
print(orders.shape)  # Output: (825, 10)
```

The dataset contains 825 rows (individual orders) and 10 columns. The key columns include:
- `order_id`: Unique identifier for each order
- `customer_id`: Customer identification number
- `order_date`: Date when the order was placed
- `product`: Name of the product ordered
- `category`: Product category (clothing, shoes, etc.)
- `unit_price`: Price per unit
- `quantity`: Number of items purchased
- `express`: Whether express delivery was used
- `coupon_code`: Discount code applied
- `gift_message`: Gift message for the order

## Understanding Data Problems

Before cleaning, we need to identify problems in our dataset:

### Key Issues Identified:
1. **Text columns that should be numbers**: `unit_price` is stored as text with currency symbols and formatting
2. **Text inconsistency**: `category` and `express` columns have inconsistent capitalization and spacing
3. **Missing values**: Some dates are missing, and there are 15 empty columns in the Airbnb dataset (though this one has fewer issues)
4. **Duplicate entries**: Some orders appear twice due to formatting differences

## Cleaning Text Data

### Problem 1: Inconsistent Category Values

Categories like "clothing" appear in different formats:
- "Clothing"
- "clothing" 
- "Clothing "
- "clothing "

#### Steps to Fix:

```python
# Check unique values before cleaning
print(orders['category'].nunique())  # Output: 38

# Count occurrences of each value
print(orders['category'].value_counts())

# Clean the category column step by step:
orders['category'] = orders['category'].str.strip()  # Remove leading/trailing spaces
orders['category'] = orders['category'].str.lower()  # Convert to lowercase
orders['category'] = orders['category'].replace('home and garden', 'home and garden')  # Fix spelling
orders['category'] = orders['category'].str.title()  # Capitalize first letter of each word

# Verify results
print(orders['category'].nunique())  # Output: 6 (correct number of categories)
```

### Problem 2: Inconsistent Express Delivery Values

The `express` column has inconsistent values:
- "Yes"
- "yes" 
- "YES"
- "y"
- "No"
- "no"

#### Steps to Fix:

```python
# Check unique values before cleaning
print(orders['express'].value_counts())

# Clean the express column using map function
orders['express'] = orders['express'].str.lower()  # Convert to lowercase

# Map values to boolean (True/False)
express_mapping = {
    'yes': True,
    'y': True,
    'no': False
}

orders['express'] = orders['express'].map(express_mapping)

# Check results - missing values should be zero
print(orders['express'].isnull().sum())  # Should output: 0

# Verify the cleaned data
print(orders['express'].value_counts())
```

## Cleaning Numeric Data

### Problem 3: Price Data as Text

Unit prices are stored as text with currency symbols and inconsistent formatting:
- "1199,00 kroner" 
- "1299 kroner"
- "349,00"

#### Steps to Fix:

```python
# Check current price format
print(orders['unit_price'].head(8))

# Clean step by step:
price = orders['unit_price'].str.strip()  # Remove leading/trailing spaces
price = price.str.replace(' kroner', '')  # Remove currency symbol
price = price.str.replace(' ', '')  # Remove internal spaces
price = price.str.replace(',', '.')  # Convert comma to decimal point

# Convert to numeric type
orders['unit_price'] = pd.to_numeric(price, errors='coerce')

# Verify results
print(orders['unit_price'].dtype)  # Should show float64
print(orders['unit_price'].describe())
```

## Cleaning Date Data

### Problem 4: Date Values as Text

Order dates are stored as text and need to be converted to proper datetime objects.

#### Steps to Fix:

```python
# Check date format issues
print(orders['order_date'].head())

# Convert text to datetime, handling errors gracefully
orders['order_date'] = pd.to_datetime(orders['order_date'], errors='coerce')

# Count missing dates before and after
missing_before = orders['order_date'].isnull().sum()
print(f"Missing dates before: {missing_before}")

# Check which specific dates failed to convert
failed_dates = orders[orders['order_date'].isnull()]['order_date']
print(failed_dates)

# Verify results
print(orders['order_date'].dtype)  # Should show datetime64[ns]
```

## Finding and Removing Duplicates

Duplicates can skew analysis results by inflating counts and totals.

### Steps to Find Duplicates:

```python
# Check for duplicates using all columns
duplicate_count = orders.duplicated().sum()
print(f"Number of duplicate rows: {duplicate_count}")

# Show actual duplicate rows
duplicate_rows = orders[orders.duplicated(keep=False)]
print(duplicate_rows.head())

# Check duplicates based on specific column (order_id)
duplicate_order_ids = orders.duplicated(subset=['order_id']).sum()
print(f"Duplicate order IDs: {duplicate_order_ids}")
```

### Removing Duplicates:

```python
# Remove duplicate rows, keeping first occurrence
orders_cleaned = orders.drop_duplicates(keep='first').reset_index(drop=True)

# Verify result
print(orders_cleaned.shape)  # Should be 800 rows (825 - 25 duplicates)
```

## Removing Empty Columns

Empty columns provide no value and should be removed.

### Steps to Remove:

```python
# Check column statistics
print(orders.describe())

# Remove empty gift_message column
orders_final = orders.drop(columns=['gift_message'])

# Verify result
print(orders_final.shape)  # Should be 9 columns (10 - 1)
```

## Creating New Calculated Columns

After cleaning, we can create new meaningful columns for analysis.

### Example: Total Order Value:

```python
# Calculate total value per order line
orders_final['total'] = orders_final['unit_price'] * orders_final['quantity']

# Verify the calculation
print(orders_final[['unit_price', 'quantity', 'total']].head())
```

## Grouping and Aggregating Data

Now that data is clean, we can answer meaningful questions.

### Example: Revenue by Category:

```python
# Group by category and sum total revenue
category_revenue = orders_final.groupby('category')['total'].sum().sort_values(ascending=False)
print(category_revenue)

# Example output:
# shoes        207382.0
# clothing     154639.0
# books         16680.0
```

### Example: Monthly Revenue:

```python
# Group by month and sum revenue
orders_final['month'] = orders_final['order_date'].dt.to_period('M')
monthly_revenue = orders_final.groupby('month')['total'].sum().sort_values(ascending=False)
print(monthly_revenue)
```

## Saving Cleaned Data

Always save your cleaned data to avoid reprocessing.

```python
# Save cleaned dataset
orders_final.to_csv('data/orders_clean.csv', index=False)
```

## Key Principles of Data Cleaning

### 1. **Never Modify Original Data**
Always work on a copy of the original dataset to preserve the raw data for reference.

### 2. **Document Your Process**
Each step should be written as code so others can reproduce your cleaning process exactly.

### 3. **Check Results After Each Step**
Verify that each cleaning operation produced the expected result before proceeding.

### 4. **Handle Missing Values Thoughtfully**
Missing values often have reasons (customers didn't use coupons, orders lack dates) and should be treated appropriately.

## Common Gotchas and Edge Cases

1. **Regex for Multiple Replacements**: You can combine multiple string operations into one step using regex patterns
2. **Data Type Conversion Errors**: Always clean text before converting to numeric types
3. **Duplicate Detection Timing**: Clean text first, then look for duplicates to catch formatting variations
4. **Missing Values in Grouping**: Groupby operations silently exclude missing values - always verify totals match parts

## Summary of Cleaning Steps

1. **Text Standardization**:
   - Remove leading/trailing spaces
   - Convert to consistent case (lowercase)
   - Handle special characters and spelling variations
   - Apply title case where appropriate

2. **Numeric Data Conversion**:
   - Clean formatting (remove currency symbols, spaces)
   - Convert to proper numeric types
   - Handle decimal separators correctly

3. **Date Conversion**:
   - Convert text dates to datetime objects
   - Handle conversion errors gracefully
   - Identify and document problematic dates

4. **Duplicate Management**:
   - Identify duplicate rows
   - Remove duplicates while preserving meaningful data
   - Verify that removal doesn't change analysis results significantly

5. **Column Management**:
   - Remove completely empty columns
   - Keep columns with meaningful missing values (like coupon codes)
   - Create calculated columns for analysis

## Best Practices

1. **Start Small**: Work with small datasets or samples to test cleaning logic
2. **Version Control**: Keep track of changes and document why decisions were made
3. **Validation**: Always validate that your cleaned data makes sense
4. **Reproducibility**: Write all steps as code that can be run repeatedly
5. **Documentation**: Include comments explaining each cleaning step for future reference

The key takeaway is that data cleaning is both a technical skill and a judgment call. Every decision you make affects the final analysis, so it's important to document your reasoning and validate that your results are reasonable.