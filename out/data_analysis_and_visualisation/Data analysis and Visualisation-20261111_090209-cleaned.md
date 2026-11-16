# Data Cleaning in Python

## Overview of Data Cleaning Process

Data cleaning is a crucial part of data analysis that involves identifying and correcting errors, inconsistencies, and missing values in datasets. The process follows a routine approach where we examine the data, identify problems, fix them systematically, and document our steps.

### Why Data Cleaning Matters
- Almost every real table has to be cleaned before it can be trusted
- What you decide during cleaning actually determines what your numbers mean
- Clean data ensures accurate analysis and reliable results
- The tools learned today will be applied to more complex datasets next week

## Loading and Setting Up Data

### Importing Libraries and Loading Data

```python
import pandas as pd

# Load the original data
orders_raw = pd.read_csv('data/orders.csv')

# Create a copy for cleaning
orders = orders_raw.copy()
```

### Understanding the Dataset Structure

The orders dataset contains:
- 825 rows (one row per order line)
- 10 columns including: order_id, customer_id, order_date, product, category, unit_price, quantity, express, coupon_code, gift_message
- The data is entirely synthetic and designed to mirror real-world problems

## Identifying Initial Problems

### Key Issues Found:
1. **Text format dates** - Dates are stored as strings instead of datetime objects
2. **Text format prices** - Prices contain currency symbols, spaces, and commas instead of decimal points
3. **Empty gift_message column** - Completely empty column with no values
4. **Inconsistent text formatting** - Categories and express columns have inconsistent capitalization and spacing

## Text Data Cleaning

### Problem 1: Inconsistent Category Values

#### Understanding the Issue
Categories in the dataset appear in multiple formats:
- Capitalized words
- Lowercase words  
- Words with leading/trailing spaces
- Different spellings for the same category (e.g., "home and garden" vs "home&garden")

#### Solution Approach
We'll use pandas string methods to standardize the text format:

```python
# Before cleaning - check unique values
print(orders['category'].unique())
# Output shows 38 different values instead of expected 6

# Count how many times each category appears
print(orders['category'].value_counts())

# Clean the categories step by step
orders['category'] = orders['category'].str.strip().str.lower()
orders['category'] = orders['category'].replace('home and garden', 'home&garden')
orders['category'] = orders['category'].str.title()

# Verify the cleaning worked
print(orders['category'].value_counts())
```

#### Key String Methods Used:
- `str.strip()` - Removes spaces at beginning and end of strings
- `str.lower()` - Converts all characters to lowercase
- `str.replace()` - Replaces entire values with new values
- `str.title()` - Capitalizes first letter of each word

### Problem 2: Inconsistent Express Values

#### Understanding the Issue
The express column contains multiple variations of "yes" and "no":
- "Yes", "YES", "yes"
- "No", "NO", "no"
- "Y", "y"
- Missing values

#### Solution Approach
We'll use a combination of string methods and mapping:

```python
# Check current values
print(orders['express'].value_counts())

# Clean the express column
orders['express'] = orders['express'].str.lower()

# Create a mapping dictionary for standardization
express_mapping = {
    'yes': True,
    'y': True,
    'no': False,
    None: False  # Missing values become False
}

# Apply mapping
orders['express'] = orders['express'].map(express_mapping)

# Verify the cleaning worked
print(orders['express'].value_counts())
```

### Problem 3: Text Format Prices

#### Understanding the Issue
Prices are stored as text with:
- Currency symbols (kroner)
- Spaces within numbers  
- Commas for decimal points
- Inconsistent formatting

#### Solution Approach - Step-by-Step Cleaning:

```python
# First, examine the raw prices
print(orders['unit_price'].head(8))

# Clean step by step
price = orders['unit_price'].str.strip()  # Remove leading/trailing spaces
price = price.str.replace('kroner', '')   # Remove currency symbol
price = price.str.replace(' ', '')        # Remove internal spaces
price = price.str.replace(',', '.')       # Convert comma to decimal point

# Convert to numeric type
orders['unit_price'] = pd.to_numeric(price, errors='coerce')

# Verify the results
print(orders['unit_price'].describe())
```

#### Key Methods Used:
- `str.strip()` - Remove whitespace
- `str.replace()` - Replace parts of strings (not `replace()` which replaces entire values)
- `pd.to_numeric()` - Convert to numeric type with error handling

## Date Cleaning

### Problem: Text Format Dates

#### Understanding the Issue
Dates are stored as text strings instead of datetime objects, making date-based analysis impossible.

#### Solution Approach:

```python
# Try direct conversion (will fail)
try:
    pd.to_datetime(orders['order_date'])
except Exception as e:
    print(f"Error: {e}")

# Clean with error handling
orders['order_date'] = pd.to_datetime(orders['order_date'], 
                                     errors='coerce')

# Check for missing values introduced by conversion
print("Missing dates before:", orders['order_date'].isnull().sum())
print("Missing dates after:", orders['order_date'].isnull().sum())

# Show rows with missing dates
missing_dates = orders[orders['order_date'].isnull()]
print(missing_dates[['order_id', 'order_date']])
```

#### Key Concepts:
- `pd.to_datetime()` converts text to datetime objects
- `errors='coerce'` converts invalid dates to NaN (Not a Time) instead of raising errors
- Always check for missing values before and after cleaning

## Duplicate Detection and Removal

### Problem: Duplicate Order Records

#### Understanding the Issue
Some orders appear twice in the dataset, which would inflate totals.

#### Solution Approach:

```python
# Check for duplicates using all columns
duplicates = orders.duplicated()
print("Total duplicates:", duplicates.sum())

# Show specific duplicate rows (both copies)
duplicate_rows = orders[orders.duplicated(keep=False)]
print(duplicate_rows.head(6))

# Remove duplicates, keeping first occurrence
orders_cleaned = orders.drop_duplicates()

# Alternative: Check only by order_id (unique identifier)
order_id_duplicates = orders.duplicated(subset=['order_id'])
print("Duplicates by order_id:", order_id_duplicates.sum())
```

#### Key Concepts:
- `duplicated()` identifies duplicate rows (marks second and subsequent occurrences)
- `drop_duplicates()` removes duplicate rows
- Always clean text data before checking for duplicates to ensure proper matching
- `keep=False` shows all copies of duplicates instead of just the second occurrence

## Handling Missing Values

### Problem: Empty Gift Message Column

#### Understanding the Issue
The gift_message column is completely empty (825 out of 825 rows are null).

#### Solution:

```python
# Check column statistics
print(orders.describe(include='all'))

# Remove the empty column
orders = orders.drop(columns=['gift_message'])

# Verify removal
print(orders.shape)
```

### Coupon Code Column

The coupon_code column has 635 missing values (79% of rows). These are not errors but represent legitimate data - most customers didn't use coupons.

## Final Analysis and Validation

### Creating New Calculated Columns

```python
# Calculate total amount for each order line
orders['total'] = orders['unit_price'] * orders['quantity']

# Group by category to see revenue by product type
category_revenue = orders.groupby('category')['total'].sum().sort_values(ascending=False)
print(category_revenue)

# Group by month to see revenue over time
orders['month'] = orders['order_date'].dt.to_period('M')
monthly_revenue = orders.groupby('month')['total'].sum()
print(monthly_revenue)
```

### Validation Techniques

1. **Cross-check totals**: Ensure parts add up to the whole
2. **Verify consistency**: Check that cleaned values make sense
3. **Document changes**: Record all cleaning decisions for transparency

## Complete Cleaning Workflow

### Best Practices Implemented:

```python
# Complete cleaning workflow in one cell
orders_clean = orders_raw.copy()

# Clean categories
orders_clean['category'] = (orders_clean['category']
                           .str.strip()
                           .str.lower()
                           .replace('home and garden', 'home&garden')
                           .str.title())

# Clean express column  
orders_clean['express'] = orders_clean['express'].str.lower()
orders_clean['express'] = orders_clean['express'].map({
    'yes': True, 'y': True, 'no': False, None: False
})

# Clean prices
price = (orders_clean['unit_price']
         .str.strip()
         .str.replace('kroner', '')
         .str.replace(' ', '')
         .str.replace(',', '.'))
orders_clean['unit_price'] = pd.to_numeric(price, errors='coerce')

# Clean dates
orders_clean['order_date'] = pd.to_datetime(orders_clean['order_date'], 
                                           errors='coerce')

# Remove duplicates
orders_clean = orders_clean.drop_duplicates()

# Remove empty column
orders_clean = orders_clean.drop(columns=['gift_message'])

# Save cleaned data
orders_clean.to_csv('data/orders_clean.csv', index=False)
```

## Important Concepts and Gotchas

### String Methods vs Replace Methods
- **`.str.replace()`**: Replaces parts of text within values (like removing currency symbols)
- **`.replace()`**: Replaces entire values (like changing 'home and garden' to 'home&garden')

### Error Handling in Data Conversion
- Always use `errors='coerce'` when converting data types
- Check for missing values before and after conversion
- Invalid conversions become NaN, not errors

### Duplicate Detection Timing
- Clean text data first before checking for duplicates
- Text differences (capitalization, spacing) can cause false negatives in duplicate detection
- Use `subset` parameter to check duplicates in specific columns only

### Data Validation
- Never assume your cleaned data is perfect
- Always validate that results make sense
- Cross-check totals and verify calculations
- Document all decisions made during cleaning for reproducibility

## Why This Matters

Data cleaning is not just about fixing errors - it's about making informed decisions about what constitutes valid data. Every decision affects the final numbers, and understanding these decisions is crucial for accurate analysis. The tools learned here will be applied to more complex datasets in future work, but the fundamental approach remains the same: identify problems systematically, fix them methodically, and document everything clearly.

The cleaned dataset now provides reliable foundation for deeper analysis, including calculating revenue by category, analyzing seasonal trends, and understanding customer purchasing patterns - all of which would have been impossible with the original messy data.