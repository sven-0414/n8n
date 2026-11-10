# Python Data Analysis with Pandas

## What is Pandas?

Pandas is a Python library designed for working with tabular data. The name comes from "panel data," which refers to tables of observations. It was created around 2008 and has become the standard tool for handling tables in Python.

### Key Features:
- Built on NumPy (which provides array functionality for fast numerical calculations)
- Enables data reading, cleaning, selection, filtering, sorting, grouping, joining, and summarizing
- Works with large datasets efficiently due to its foundation on NumPy arrays

### Why Pandas?
- Unlike Excel, code serves as documentation - anyone can reproduce results
- Unlike SQL, it works on loaded data rather than querying databases
- Enables direct transition from data exploration to visualization and machine learning
- Provides intuitive syntax that mirrors SQL concepts but uses different terminology

## Setting Up Jupyter Notebooks

### File Format
Jupyter notebooks use the `.ipynb` extension, which stands for "I Python Notebook" (original name). Today they're commonly called Jupyter Notebooks or simply notebooks.

### Cell Types
- **Code cells**: Write and execute Python code
- **Markdown cells**: Write text with formatting (headers, lists, etc.)

### Basic Operations
```python
# Creating variables
x = 10

# Running code in a cell - no output visible until you run it
# The left side shows execution order (1, 2, 3...)

# Accessing previous variables
print(x)  # Shows value of x

# Using functions
def add(a, b):
    return a + b

result = add(2, 3)
```

### Execution Shortcuts
- Run cell: Ctrl+Enter or Shift+Enter
- Restart kernel: Menu → Kernel → Restart
- Run all cells in order: Menu → Cell → Run All

## Importing Pandas

```python
import pandas as pd
print(pd.__version__)  # Check version (should be 3.0.5)
```

### Why Version Matters
The current version is 3.0.5, which has behavioral changes from earlier versions (1 and 2). Many online tutorials use older pandas syntax that may not work in newer versions. This can cause confusion when copying code.

## Loading Data with Pandas

### Reading CSV Files
```python
import pandas as pd

# Load data into a DataFrame called 'listings'
listings = pd.read_csv('data/listings.csv')
```

### Understanding the Data Structure
The Airbnb Stockholm listings dataset contains:
- 4,574 rows (listings)
- 84 columns (pieces of information per listing)

### Key Concepts

**DataFrame**: The whole table - a two-dimensional data structure with labeled rows and columns.

**Series**: A single column from a DataFrame - one-dimensional array with axis labels.

```python
# Loading the dataset
listings = pd.read_csv('data/listings.csv')

# Check size of the DataFrame
print(listings.shape)  # Output: (4574, 84)

# View first few rows
print(listings.head())  # Shows first 5 rows

# View column names
print(listings.columns.tolist())
```

## Examining Data Structure

### Getting Basic Information About Data
```python
# Detailed information about the DataFrame
listings.info()

# Count of each data type
listings.dtypes.value_counts()
```

### Understanding Data Types
- **int64**: Whole numbers (integers)
- **float64**: Decimal numbers 
- **object**: Text strings (in older versions, this was called 'string')
- **string**: New in pandas 3.0+ for text columns

### Identifying Missing Values
```python
# Find columns with no values (count = 0)
listings.count().sort_values().head(16)

# Show columns that are completely empty
empty_columns = listings.count()[listings.count() == 0]
print(empty_columns.index.tolist())
```

## Selecting Columns

### Single Column Selection
```python
# Select one column using single brackets
room_type = listings['room_type']

# Check what type of object it is
print(type(room_type))  # Output: pandas.core.series.Series
```

### Multiple Column Selection
```python
# Select multiple columns using double brackets (list inside brackets)
selected_columns = listings[['room_type', 'name', 'price']]

# This creates a DataFrame with the specified columns
```

### Common Mistake to Avoid
```python
# WRONG - single brackets only select one column
wrong_selection = listings['room_type', 'name']  # KeyError

# CORRECT - double brackets for multiple columns
correct_selection = listings[['room_type', 'name']]
```

## Describing Data

### Using describe() Method
```python
# Get statistical summary of numeric columns
listings.describe()

# For specific columns only
specific_columns = ['accommodates', 'bedrooms', 'beds', 'minimum_nights', 'number_of_reviews', 'review_scores_rating']
listings[specific_columns].describe().round(2)
```

### Understanding Statistical Output
- **count**: Number of non-null values
- **mean**: Average value
- **std**: Standard deviation (measure of spread)
- **min**: Smallest value
- **25%**: First quartile (25th percentile)
- **50%**: Median (50th percentile)
- **75%**: Third quartile (75th percentile)
- **max**: Largest value

### Key Insights from Describe
1. **Missing values**: Count shows how many rows have data for each column
2. **Extreme values**: Large differences between mean and median indicate uneven distribution
3. **Data quality issues**: Some columns may contain non-numeric data that shouldn't be described

## Working with Text Data

### Problem with Text Numbers
```python
# Price column appears to be numeric but is actually text
print(listings['price'].dtype)  # Output: object/string

# Example of problematic sorting
listings[['name', 'price']].head(5)
# This will sort by character, not numerical value
```

### Text Column Analysis
```python
# For text columns, describe shows different statistics
listings['room_type'].describe()

# Get unique values
unique_room_types = listings['room_type'].unique()
print(unique_room_types)

# Count how many unique values exist
num_unique_room_types = listings['room_type'].nunique()
print(num_unique_room_types)
```

## Filtering Data (Boolean Masks)

### Creating Boolean Conditions
```python
# Create a mask for entire home apartments
mask = listings['room_type'] == 'Entire home/apt'

# Count results using sum() - True = 1, False = 0
entire_homes_count = mask.sum()
print(entire_homes_count)  # Output: 3760

# Calculate percentage
percentage_entire_homes = mask.mean()
print(percentage_entire_homes)  # Output: ~0.82 (82%)
```

### Using Masks as Filters
```python
# Filter the DataFrame using the mask
entire_homes = listings[mask]
print(entire_homes.shape)  # Output: (3760, 84)

# This is equivalent to:
filtered_listings = listings[listings['room_type'] == 'Entire home/apt']
```

### Combining Multiple Conditions

**Incorrect way (causes error):**
```python
# This will cause a ValueError
listings['neighborhood_cleansed'] == 'Södermalm' and listings['accommodates'] >= 4
```

**Correct way using pandas operators:**
```python
# Using & (and), | (or), ~ (not)
mask = (listings['neighborhood_cleansed'] == 'Södermalm') & (listings['accommodates'] >= 4)
filtered_listings = listings[mask]
print(filtered_listings.shape)  # Output: (454, 84)
```

### Helper Methods for Filtering
```python
# isin() - checks if value is in a list
central_neighborhoods = ['Södermalm', 'Norrmalm', 'Östermalm']
mask = listings['neighborhood_cleansed'].isin(central_neighborhoods)

# between() - checks if value is between two values (inclusive)
mask = listings['accommodates'].between(2, 4)  # Includes 2 and 4

# notna() / notnull() - checks for missing values
mask = listings['review_scores_rating'].notna()

# not() - negates boolean values
mask = ~(listings['room_type'] == 'Entire home/apt')  # Not entire homes
```

## Sorting Data

### Basic Sorting
```python
# Sort by one column (descending order)
sorted_by_reviews = listings.sort_values('number_of_reviews', ascending=False)

# Show top 5 rows
top_5_reviews = sorted_by_reviews.head(5)
```

### Multi-column Sorting
```python
# Sort by multiple columns
multi_sorted = listings.sort_values(['accommodates', 'number_of_reviews'], 
                                    ascending=[False, False])

# First sort by accommodates (largest first)
# Then for ties, sort by number of reviews (largest first)
```

### Getting Top N Rows
```python
# Using nlargest() method
top_5_accommodates = listings.nlargest(5, 'accommodates')

# This is equivalent to:
top_5_accommodates = listings.sort_values('accommodates', ascending=False).head(5)
```

## Indexing and Accessing Specific Rows

### Understanding Indexes
```python
# Every row has an index (like a label/tag)
print(listings.index)  # Shows the index values

# Access specific rows using loc[] or iloc[]
# loc uses labels/index names
row_by_label = listings.loc[825]  # Gets row with index 825

# iloc uses positions (0-based)
row_by_position = listings.iloc[0]  # Gets first row
```

### Resetting Index After Filtering
```python
# After filtering, indexes may be inconsistent
filtered_data = listings[listings['accommodates'] >= 16]
print(filtered_data.index)  # May show 825, 997, etc.

# Reset to sequential numbering (0, 1, 2, ...)
reset_index_data = filtered_data.reset_index(drop=True)
```

## Complete Example: Finding Top Listings

### Step-by-Step Process
```python
# Find top 5 listings in Södermalm with room for 4+ guests by number of reviews
# Step 1: Filter conditions
mask = ((listings['neighborhood_cleansed'] == 'Södermalm') & 
        (listings['room_type'] == 'Entire home/apt') & 
        (listings['accommodates'] >= 4))

# Step 2: Apply filter
filtered_listings = listings[mask]

# Step 3: Sort by reviews (descending)
sorted_listings = filtered_listings.sort_values('number_of_reviews', ascending=False)

# Step 4: Get top 5
top_5 = sorted_listings.head(5)

# Step 5: Select specific columns for display
view_columns = ['name', 'neighborhood_cleansed', 'room_type', 'accommodates', 
                'number_of_reviews', 'review_scores_rating']
result = top_5[view_columns]
```

## Key Takeaways

1. **Pandas is essential** for data analysis in Python - it handles large datasets efficiently
2. **DataFrame vs Series**: Tables are DataFrames, individual columns are Series
3. **Data types matter**: Text representations of numbers (like prices with $ signs) must be converted before numerical operations
4. **Boolean masks**: The foundation for filtering data effectively
5. **Multiple conditions**: Use `&`, `|`, and `~` instead of `and`, `or`, `not`
6. **Index management**: Understand when to use labels vs positions for accessing rows
7. **Data quality**: Always examine missing values, outliers, and data structure before analysis

This foundational knowledge will be built upon in future lessons as we learn data cleaning techniques and more advanced analytical methods.