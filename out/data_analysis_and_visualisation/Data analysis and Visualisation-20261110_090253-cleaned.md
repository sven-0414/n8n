# Python Data Analysis with Pandas

## Understanding Grouping and Aggregation

### Value Counts
Value counts is a method used to count how many times each unique value appears in a column. It's particularly useful for categorical data where you want to understand the distribution of categories.

**Rule**: The `value_counts()` method returns a Series with the counts of each unique value in descending order (highest count first).

```python
import pandas as pd
listings = pd.read_csv('data/listings.csv')
room_type_counts = listings['room_type'].value_counts()
print(room_type_counts)
```

**Key Points**:
- Value counts automatically sorts results by frequency in descending order
- Missing values are excluded by default (NaN values are not counted)
- Returns a Series with the unique values as the index and their counts as values

**Why it matters**: This is fundamental for understanding data distribution and identifying dominant categories in your dataset.

### Normalized Value Counts
When you want to understand proportions rather than absolute numbers, use normalized value counts.

```python
room_type_shares = listings['room_type'].value_counts(normalize=True).round(3)
print(room_type_shares)
```

**Key Points**:
- `normalize=True` converts counts to proportions (0.0 to 1.0)
- The sum of all shares equals 1.0
- Useful for understanding relative distribution

### Handling Missing Values in Value Counts
By default, value_counts excludes missing values. You can include them by setting `dropna=False`.

```python
bedroom_counts = listings['bedrooms'].value_counts(dropna=False, sort_index=True)
print(bedroom_counts)
```

**Key Points**:
- Missing values appear as 'nan' in the result
- Setting `sort_index=True` sorts by the actual index values rather than counts
- This helps identify data quality issues and missing data patterns

## Group By Operations

### Basic Group By Concept
Group by operations split data into groups, perform calculations on each group, and combine results. This is fundamental for analyzing data across categories.

**Rule**: The three-step process of group by involves splitting the dataset into groups, applying an aggregation function to each group, and combining the results into a new structure.

```python
guests_by_room_type = listings.groupby('room_type')['accommodates'].mean().round(2)
print(guests_by_room_type)
```

### Multiple Aggregations with Aggregate Function
When you need multiple calculations for each group, use the `aggregate()` method with a list of functions.

```python
room_summary = listings.groupby('room_type').agg(['count', 'mean', 'median', 'max']).round(2)
print(room_summary)
```

**Key Points**:
- `agg()` accepts a list of function names as strings
- Each function is applied to all numeric columns in the group
- Results show multiple statistics for each group

### Advanced Group By with Multiple Columns
You can perform calculations on multiple columns simultaneously using group by.

```python
room_features = listings.groupby('room_type')[['bedrooms', 'beds']].mean().round(2)
print(room_features)
```

**Key Points**:
- Double brackets `[['column1', 'column2']]` select multiple columns
- The result is a DataFrame with groups as rows and selected columns as columns
- Each cell contains the calculated statistic for that group and column combination

### Group By with Custom Aggregations
You can create custom summaries by naming each aggregation function explicitly.

```python
summary_table = listings.groupby('room_type').agg({
    'accommodates': ['count', 'mean'],
    'bedrooms': 'median'
}).round(2)
print(summary_table)
```

**Key Points**:
- `agg()` with a dictionary allows you to specify different functions for different columns
- Column names in the result reflect the original column names and aggregation types
- This approach gives you control over which statistics are calculated and how they're named

## Understanding Data Quality Issues

### Price Data Type Problem
Price data often contains non-numeric characters like currency symbols and commas, making it unusable for mathematical operations.

```python
# This will cause an error because price is stored as text
try:
    avg_price_by_room = listings.groupby('room_type')['price'].mean()
except Exception as e:
    print(f"Error: {e}")
```

**Key Points**:
- Text columns cannot be used in mathematical operations like mean, sum, etc.
- Price data needs cleaning before analysis (removing $ signs, commas, converting to numeric)
- This is a common data quality issue that requires preprocessing

### Data Cleaning Preparation
Before performing mathematical operations on data, you must ensure columns are properly formatted.

**Rule**: Always verify data types before performing calculations. Non-numeric text data must be cleaned before numerical analysis.

```python
# Example of what needs to be done for price:
# 1. Remove dollar signs and commas
# 2. Convert string to numeric
# listings['price'] = listings['price'].str.replace('$', '').str.replace(',', '')
# listings['price'] = pd.to_numeric(listings['price'])
```

## Practical Applications

### Creating Summary Tables
You can create clean, readable summary tables that answer business questions.

```python
summary = listings.groupby('room_type').agg({
    'accommodates': ['count', 'mean'],
    'bedrooms': 'median'
}).round(2)
print(summary)
```

**Key Points**:
- Summary tables help communicate findings effectively
- Multiple statistics provide comprehensive insights
- Proper rounding makes results more readable

### Interpreting Group Statistics
Always consider sample size when interpreting group averages.

```python
# Example of why sample size matters
group_sizes = listings.groupby('room_type')['accommodates'].count()
print(group_sizes)

# The shared room group has only 24 listings
# Averages from small groups are less reliable
```

**Key Points**:
- Small sample sizes can lead to misleading averages
- Median is often more reliable than mean for small groups
- Always examine the count column when interpreting grouped statistics

### Data Quality Indicators
Value counts and group by operations help identify data quality issues.

```python
# Identify potential outliers or errors
bedroom_analysis = listings['bedrooms'].value_counts(dropna=False).sort_index()
print(bedroom_analysis)

# Look for unusual patterns like 12 bedrooms in listings
# These might indicate data entry errors or legitimate but rare cases
```

**Key Points**:
- Unexpected high values may indicate data quality issues
- Missing value patterns can reveal systematic problems
- Regular analysis helps identify trends and anomalies

This approach to grouping and aggregation provides the foundation for answering complex business questions through data analysis, whether it's understanding distribution patterns or calculating meaningful statistics across different categories.