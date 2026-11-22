# Data Cleaning in Python

## Introduction to Data Cleaning

Data cleaning is a crucial part of data analysis that involves identifying and correcting errors, inconsistencies, and missing values in datasets. Real-world data is rarely perfect - it often contains problems that need to be addressed before meaningful analysis can occur.

### Why Data Cleaning Matters
- **Trustworthiness**: Clean data produces reliable results
- **Accuracy**: Properly cleaned data ensures correct answers to questions
- **Decision Making**: Clean data provides a solid foundation for business decisions
- **Reproducibility**: Well-documented cleaning processes allow others to replicate your work

### The Cleaning Process
The standard routine for data cleaning involves:
1. **Look at the data** - Identify problems visually
2. **Identify problems** - Document what needs fixing
3. **Fix problems one at a time** - Address each issue systematically
4. **Check results after each fix** - Verify that changes work correctly
5. **Never change the original data** - Work on copies and maintain backups
6. **Write steps as code** - Document everything for reproducibility

## Loading Data and Creating Copies

Before beginning any cleaning process, we need to load our data and create a copy to work with:

```python
import pandas as pd

# Load the original data
orders_raw = pd.read_csv('data/orders.csv')

# Create a copy for cleaning
orders = orders_raw.copy()

# Verify the data was loaded correctly
print(orders.shape)  # Should show (825, 10)
```

## Identifying Data Problems

### Problem 1: Text Columns with Inconsistent Values
When columns should contain consistent values (like categories or yes/no responses), real data often has inconsistencies due to:
- Different capitalization
- Extra spaces at beginning or end
- Different spellings
- Mixed formatting

```python
# Check what we have in the category column
print(orders['category'].unique())  # Shows 38 different values instead of expected 6
print(orders['category'].value_counts())  # Shows actual frequency of each value
```

### Problem 2: Text Representations of Numbers
Data often contains numbers stored as text with formatting that prevents mathematical operations:
- Currency symbols (kr, $)
- Spaces within numbers
- Commas instead of decimal points
- Extra characters like "kroner"

```python
# Check the unit price column
print(orders['unit_price'].head(8))  # Shows values like "1199 kroner", "1299", "349,0"
```

### Problem 3: Date Formatting Issues
Dates stored as text cause problems when trying to perform date-related operations:
- Inconsistent date formats
- Missing or invalid dates
- Text that looks like dates but isn't valid

```python
# Check date column
print(orders['order_date'].head())  # Shows dates as text strings
```

## Text Cleaning Techniques

### Step 1: Standardizing Text Values

#### Removing Extra Spaces and Normalizing Case

The first step in cleaning text data is to remove leading/trailing spaces and normalize capitalization:

```python
# Remove spaces at beginning and end, convert to lowercase
orders['category'] = orders['category'].str.strip().str.lower()

# Check results
print(orders['category'].unique())  # Should show fewer values now
```

#### Handling Special Cases with Replace

When there are specific inconsistencies that need special handling:

```python
# Replace inconsistent spellings
orders['category'] = orders['category'].replace('home and garden', 'home_garden')
orders['category'] = orders['category'].str.title()  # Capitalize first letter of each word

# Verify the result
print(orders['category'].value_counts())
```

### Step 2: Cleaning Yes/No Columns

For binary columns like `express`, we need to standardize responses:

```python
# Standardize express column
orders['express'] = orders['express'].str.lower()
orders['express'] = orders['express'].map({'yes': True, 'y': True, 'no': False})

# Check results
print(orders['express'].value_counts())
```

### Step 3: Converting Text to Numbers

#### The Process of Cleaning Price Data

Price data often contains formatting that prevents mathematical operations. We need to:

1. Remove spaces at beginning and end
2. Remove currency symbols
3. Replace commas with decimal points
4. Convert to numeric type

```python
# Step-by-step cleaning of price data
price = orders['unit_price'].str.strip()  # Remove leading/trailing spaces
price = price.str.replace('kr', '')       # Remove currency symbol
price = price.str.replace(' ', '')        # Remove internal spaces
price = price.str.replace(',', '.')       # Replace comma with decimal point

# Convert to numeric type
orders['unit_price'] = pd.to_numeric(price, errors='coerce')

# Verify the conversion worked
print(orders['unit_price'].describe())
```

### Advanced Text Cleaning with Regex

For more complex cleaning tasks, regular expressions can handle multiple operations in one step:

```python
# Using regex to clean price data in one operation
orders['unit_price'] = orders['unit_price'].str.replace(r'[^\d\.]', '', regex=True)
orders['unit_price'] = pd.to_numeric(orders['unit_price'], errors='coerce')
```

## Date Cleaning

### Converting Text Dates to DateTime Objects

Once text dates are properly formatted, we can convert them to actual date objects:

```python
# Convert text dates to datetime objects
orders['order_date'] = pd.to_datetime(orders['order_date'], errors='coerce')

# Check results - missing dates will show as NaT (Not a Time)
print(orders['order_date'].head())
```

### Handling Invalid Dates

When dates are invalid or malformed, pandas can be configured to treat them as missing rather than failing:

```python
# Convert with error handling
orders['order_date'] = pd.to_datetime(orders['order_date'], errors='coerce')

# Count missing dates before and after
missing_before = orders['order_date'].isna().sum()
print(f"Missing dates: {missing_before}")

# Check which specific values were problematic
problematic_dates = orders[orders['order_date'].isna()]['order_date']
```

## Duplicate Detection and Removal

### Finding Duplicates

Duplicates can occur due to data entry errors or system issues:

```python
# Find duplicate rows (only marks second and subsequent copies)
duplicates = orders.duplicated()
print(f"Number of duplicates: {duplicates.sum()}")

# Show all duplicate rows
duplicate_rows = orders[orders.duplicated(keep=False)]
print(duplicate_rows.head())
```

### Removing Duplicates

Duplicates should be removed carefully, ensuring we preserve meaningful data:

```python
# Remove duplicate rows (keeping the first occurrence)
orders_cleaned = orders.drop_duplicates()

# Verify the removal worked
print(f"Original shape: {orders.shape}")
print(f"Cleaned shape: {orders_cleaned.shape}")
```

### Checking for Duplicates by Specific Columns

Sometimes we want to check duplicates based on specific identifiers:

```python
# Check for duplicate order IDs (should be unique)
duplicate_orders = orders.duplicated(subset=['order_id'])
print(f"Number of duplicate order IDs: {duplicate_orders.sum()}")

# Remove duplicates by order ID only
orders_no_duplicates = orders.drop_duplicates(subset=['order_id'])
```

## Removing Empty Columns

Columns with no meaningful data should be removed:

```python
# Check if column is completely empty
print(orders['gift_message'].isna().sum())  # Should show 825 for all missing

# Remove the empty column
orders = orders.drop(columns=['gift_message'])
print(orders.shape)  # Should now show 9 columns instead of 10
```

## Creating New Calculated Columns

After cleaning, we can create new columns for analysis:

```python
# Create total amount column
orders['total'] = orders['unit_price'] * orders['quantity']

# Verify the calculation worked
print(orders[['unit_price', 'quantity', 'total']].head())
```

## Grouping and Analysis

With cleaned data, we can perform meaningful groupings and analysis:

```python
# Group by category to see which brought in the most money
category_revenue = orders.groupby('category')['total'].sum().sort_values(ascending=False)
print(category_revenue)

# Group by month to analyze seasonal trends
orders['month'] = orders['order_date'].dt.to_period('M')
monthly_revenue = orders.groupby('month')['total'].sum()
print(monthly_revenue)
```

## Saving Cleaned Data

Always save your cleaned data to avoid repeating the process:

```python
# Save cleaned data to CSV
orders.to_csv('data/orders_cleaned.csv', index=False)
```

## Complete Cleaning Process Summary

The complete cleaning process should be documented in a single code cell for reproducibility:

```python
# Full cleaning process in one cell
orders = orders_raw.copy()

# Clean category column
orders['category'] = orders['category'].str.strip().str.lower()
orders['category'] = orders['category'].replace('home and garden', 'home_garden')
orders['category'] = orders['category'].str.title()

# Clean express column
orders['express'] = orders['express'].str.lower()
orders['express'] = orders['express'].map({'yes': True, 'y': True, 'no': False})

# Clean price column
price = orders['unit_price'].str.strip()
price = price.str.replace('kr', '')
price = price.str.replace(' ', '')
price = price.str.replace(',', '.')
orders['unit_price'] = pd.to_numeric(price, errors='coerce')

# Clean date column
orders['order_date'] = pd.to_datetime(orders['order_date'], errors='coerce')

# Remove duplicates
orders = orders.drop_duplicates()

# Remove empty column
orders = orders.drop(columns=['gift_message'])

# Create calculated columns
orders['total'] = orders['unit_price'] * orders['quantity']
```

## Key Takeaways

1. **Never modify original data** - Always work on copies
2. **Document everything** - Write code that others can run to reproduce your results
3. **Check your work** - Verify each step produces expected results
4. **Handle missing values thoughtfully** - Understand why they exist before removing them
5. **Test with real analysis** - Use cleaned data to answer questions about it
6. **Be honest about decisions** - Every cleaning decision affects final numbers

The most important aspect of data cleaning is understanding that each decision you make affects your final results. The goal isn't to make the data perfect, but to make it suitable for your specific analysis needs while being transparent about what was done and why.