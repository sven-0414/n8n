# Introduction to Pandas

## What is Pandas?

Pandas is a Python library designed for working with tabular data. The name comes from "panel data," which refers to tables of observations. It was first developed around 2008 and has become a standard tool for data analysis in Python.

Key characteristics:
- Built on NumPy (which provides array functionality)
- Designed specifically for handling tables
- Can efficiently manage large datasets
- Provides powerful tools for data manipulation, cleaning, and analysis

```python
import pandas as pd
```

## Why Pandas?

Pandas offers several advantages over other approaches:
- **Excel limitations**: Excel doesn't show the process used to manipulate data
- **Reproducibility**: With Pandas, code serves as documentation that anyone can run again
- **Integration**: Works directly with charts and machine learning tools
- **Performance**: Built on NumPy for fast calculations

## Jupyter Notebooks

Jupyter Notebooks provide an interactive environment where you can mix text and code in the same file. This makes them ideal for data exploration.

### Notebook Components:
1. **Code cells**: Where you write Python code
2. **Markdown cells**: For writing explanations, documentation, and formatted text

### Basic Operations:
```python
# Create a variable
x = 10

# Run the cell to see output
x  # This will show 10

# Multiple cells can reference each other
y = 20
```

### Notebook Features:
- **Cell execution**: Each cell runs independently and maintains state
- **Order tracking**: Cells are numbered to show execution order
- **Restart functionality**: Reset the entire notebook environment
- **Markdown formatting**: Use `##` for headings, `*text*` for italics

## Data Frames and Series

### Data Frame
A data frame is the primary object in Pandas - essentially a whole table with rows and columns.

```python
# Loading data into a data frame
listings = pd.read_csv('data/listings.csv')
```

### Series
A series is a single column from a data frame - just one dimension of data.

```python
# Getting a single column (creates a series)
room_type = listings['room_type']

# Getting multiple columns (creates a data frame)
selected_columns = listings[['room_type', 'name', 'price']]
```

## Loading Data

### CSV Files
CSV (Comma Separated Values) files are plain text files where:
- First line contains column names
- Each subsequent line represents one row
- Values separated by commas
- Empty values appear as consecutive commas
- Text with commas is enclosed in quotation marks

```python
# Loading CSV data into pandas
listings = pd.read_csv('data/listings.csv')
```

### Data Frame Properties:
```python
# Check the size of your data frame
print(listings.shape)  # (4574, 84) - 4574 rows, 84 columns

# View first few rows
listings.head()  # Shows first 5 rows by default
listings.head(10)  # Shows first 10 rows

# View column names
print(listings.columns.tolist())

# Get detailed information about the data frame
listings.info()
```

## Basic Data Exploration

### Understanding Your Data:
```python
# Check what data types you have
print(listings.dtypes)

# Count how many of each data type
print(listings.dtypes.value_counts())
```

### Identifying Missing Data:
```python
# Find columns with missing values
missing_data = listings.count().sort_values()
missing_data.head(16)  # Show first 16 columns with lowest counts

# Check which columns are completely empty
empty_columns = listings.count()[listings.count() == 0]
print(empty_columns)
```

## Selecting Data

### Single Column Selection:
```python
# Select one column (returns a Series)
room_type = listings['room_type']

# This is equivalent to:
room_type = listings.room_type  # but only works if column name has no spaces
```

### Multiple Column Selection:
```python
# Select multiple columns (returns a DataFrame)
selected_data = listings[['room_type', 'name', 'price']]

# Wrong way - will cause KeyError
# selected_data = listings['room_type', 'name', 'price']  # This fails!
```

## Data Description and Statistics

### The describe() Method:
```python
# Get summary statistics for all numeric columns
listings.describe()

# Get summary for specific columns
specific_columns = ['accommodates', 'bedrooms', 'beds', 'minimum_nights', 
                   'number_of_reviews', 'review_scores_rating']
listings[specific_columns].describe().round(2)
```

### Understanding describe() Output:
- **Count**: Number of non-null values in each column
- **Mean**: Average value (only for numeric columns)
- **Std**: Standard deviation (measure of spread)
- **Min/Max**: Minimum and maximum values
- **25%/50%/75%**: Quartiles - 25th, 50th (median), and 75th percentiles

### Important Notes:
1. **Missing Values**: Columns with fewer counts than total rows have missing data
2. **Non-Numeric Data**: Text columns won't show mean, std, min, max in describe()
3. **Data Type Issues**: Some columns may be stored as numbers but aren't meaningful for calculations

## Working with Text Data

### Text Column Analysis:
```python
# Analyze text columns using describe()
listings['room_type'].describe()

# Get unique values
unique_room_types = listings['room_type'].unique()
print(unique_room_types)

# Count unique values
num_unique_room_types = listings['room_type'].nunique()
print(num_unique_room_types)
```

### Common Text Column Problems:
- **Mixed Data Types**: Values may look like numbers but are stored as text
- **Formatting Issues**: Currency symbols, commas, etc. make them appear numeric but they're not
- **Missing Values**: Empty cells are shown as NaN (Not a Number)

## Filtering and Masking

### Boolean Masks:
```python
# Create a boolean mask for entire homes
mask = listings['room_type'] == 'Entire home/apt'
print(mask.head())  # Shows True/False values

# Count how many are entire homes
num_entire_homes = mask.sum()  # True=1, False=0
print(f"Number of entire homes: {num_entire_homes}")

# Get percentage
percentage_entire_homes = mask.mean()
print(f"Percentage of entire homes: {percentage_entire_homes:.2%}")
```

### Filtering Data:
```python
# Filter the data frame using a mask
entire_homes = listings[mask]
print(entire_homes.shape)  # Shows (3760, 84)

# Multiple conditions - use & instead of and
mask_multiple = (listings['neighbourhood_cleansed'] == 'Södermalm') & \
                (listings['accommodates'] >= 4)
filtered_data = listings[mask_multiple]
print(filtered_data.shape)
```

### Common Comparison Operators:
- `==` (equals)
- `!=` (not equals)  
- `>` (greater than)
- `<` (less than)
- `>=` (greater than or equal)
- `<=` (less than or equal)

## Advanced Filtering

### Using Helper Methods:
```python
# isin() - check if value is in a list
neighborhoods = ['Södermalm', 'Östermalm', 'Norrmalm']
mask_isin = listings['neighbourhood_cleansed'].isin(neighborhoods)
print(mask_isin.sum())  # Count of rows

# between() - check if value is between two numbers
mask_between = listings['accommodates'].between(2, 4)
print(mask_between.sum())

# notna() / notnull() - check for non-missing values
mask_notna = listings['review_scores_rating'].notna()
print(mask_notna.sum())

# isna() / isnull() - check for missing values  
mask_isna = listings['review_scores_rating'].isna()
print(mask_isna.sum())
```

## Sorting Data

### Basic Sorting:
```python
# Sort by one column (ascending)
listings_sorted = listings.sort_values('number_of_reviews')

# Sort by one column (descending)
listings_sorted_desc = listings.sort_values('number_of_reviews', ascending=False)

# Sort by multiple columns
listings_sorted_multi = listings.sort_values(['accommodates', 'number_of_reviews'], 
                                             ascending=[False, False])
```

### Getting Top/Bottom Records:
```python
# Get top 5 records by number of reviews
top_5_reviews = listings.nlargest(5, 'number_of_reviews')

# Get bottom 5 records by price (assuming price is numeric)
bottom_5_price = listings.nsmallest(5, 'price')
```

## Accessing Specific Rows

### Using loc and iloc:
```python
# Using loc - label-based access
first_row_name = listings.loc[0, 'name']  # Row 0, column 'name'

# Using iloc - position-based access  
first_row_name_pos = listings.iloc[0, 1]  # First row, second column

# Select multiple rows and columns
subset_data = listings.loc[0:5, ['name', 'room_type', 'price']]
```

## Resetting Index After Filtering

When you filter or sort data, the index numbers may not be sequential. You can reset them:

```python
# Reset index to get 0, 1, 2, 3...
reset_data = listings.reset_index(drop=True)
```

## Complete Example

```python
import pandas as pd

# Load the data
listings = pd.read_csv('data/listings.csv')

# Basic exploration
print("Data shape:", listings.shape)
print("Column names:", listings.columns.tolist())
print("Data types:")
print(listings.dtypes.value_counts())

# Get summary statistics for specific columns
columns_of_interest = ['accommodates', 'bedrooms', 'beds', 'minimum_nights', 
                      'number_of_reviews', 'review_scores_rating']
summary = listings[columns_of_interest].describe().round(2)
print(summary)

# Filter and analyze data
entire_homes_mask = listings['room_type'] == 'Entire home/apt'
num_entire_homes = entire_homes_mask.sum()
print(f"Number of entire homes: {num_entire_homes}")

# Find listings in Södermalm with room for 4+ guests
soder_malm_mask = (listings['neighbourhood_cleansed'] == 'Södermalm') & \
                  (listings['accommodates'] >= 4)
filtered_listings = listings[soder_malm_mask]
print(f"Listings in Södermalm with room for 4+ guests: {len(filtered_listings)}")
```

## Key Takeaways

1. **Data Loading**: Use `pd.read_csv()` to load CSV files into data frames
2. **Data Exploration**: Start with `.shape`, `.info()`, and `.describe()`
3. **Selection**: Single brackets for one column (Series), double brackets for multiple columns (DataFrame)
4. **Filtering**: Use boolean masks with `&` (and), `|` (or), `~` (not) operators
5. **Sorting**: Use `.sort_values()` to organize data by column values
6. **Missing Data**: Check with `.count()` and `.isna()` methods

This foundational knowledge will help you navigate through the rest of your data analysis journey, as these are essential skills for any data manipulation task in Pandas.