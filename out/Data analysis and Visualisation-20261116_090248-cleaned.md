# Data Cleaning and Analysis with Pandas

## Understanding the Dataset

### Loading and Preparing Data
The process begins with loading a real Airbnb dataset from Stockholm. The approach involves creating a copy of the original data to work on, keeping the original intact for potential restarts.

```python
import pandas as pd

# Load the original dataset
listings_raw = pd.read_csv('listings.csv')

# Create a working copy
listings = listings_raw.copy()

# Check initial shape
print(listings.shape)
```

The dataset contains 4574 listings with 84 columns. This is the same dataset used in previous lessons, which has been cleaned to some extent but still requires further processing.

### Why Data Cleaning Matters
Data cleaning is essential because:
- Raw data often contains inconsistencies that prevent meaningful analysis
- Missing values and incorrect formats can lead to errors in calculations
- Clean data ensures reliable results for decision-making

## Handling Empty Columns

### Identifying Empty Columns
Empty columns are those where every value is missing (NaN). These columns provide no useful information for analysis.

```python
# Identify empty columns
empty_columns = listings.columns[listings.isnull().all()]
print(f"Number of empty columns: {len(empty_columns)}")
print("Empty column names:", empty_columns.tolist())
```

The process reveals 15 completely empty columns, with many starting with "host_" prefix. These include host_since, host_response_time, and other host-related information that appears to be missing entirely.

### Decision Making for Empty Columns
When encountering empty columns, there are several options:
1. Remove them (recommended approach)
2. Keep them for future reference
3. Fill them with invented values

The decision to remove them is based on:
- They contain no information
- They make the dataset wider and harder to read
- No useful data can be inferred from completely missing columns

```python
# Remove empty columns
listings = listings.drop(columns=empty_columns, axis=1)
print(f"Shape after removing empty columns: {listings.shape}")
```

### Important Considerations
Some empty columns may contain valuable information in different formats. For example:
- The "bathrooms" column was completely empty
- However, there was a "bathroom_text" column containing the same information as text (e.g., "1 bath", "1.5 shared baths")

This demonstrates that data can be stored in multiple formats within the same dataset.

## Price Data Transformation

### Analyzing Price Format Issues
The price column initially contains text with special formatting:
- Dollar signs at the beginning ($)
- Commas for thousands separators (,)
- Decimal points (.)
- Some values have fractional components

```python
# Check price data type and sample values
print("Price column data type:", listings['price'].dtype)
print("Sample price values:")
print(listings['price'].head(8))
```

### Step-by-Step Price Cleaning Process
The cleaning process follows these steps:
1. Remove dollar sign ($)
2. Remove commas (,)
3. Convert to numeric format

```python
# Step 1: Remove dollar sign
listings['price'] = listings['price'].str.replace('$', '', regex=False)

# Step 2: Remove commas
listings['price'] = listings['price'].str.replace(',', '', regex=False)

# Step 3: Convert to numeric format
listings['price'] = pd.to_numeric(listings['price'])

# Verify the results
print("Price column data type after cleaning:", listings['price'].dtype)
print("Summary statistics:")
print(listings['price'].describe().round(1))
```

### Key Technical Points
- Using `regex=False` when replacing special characters prevents regex interpretation issues
- The dollar sign ($) is a regex special character that means "end of text"
- Converting to numeric format allows mathematical operations

### Price Distribution Analysis
After cleaning, the price column shows:
- 2957 listings with valid prices (64.6% of total)
- 1617 listings missing prices (35.4% of total)
- Median price: $1791 per night
- Mean price: $2327 per night
- Maximum price: $40,000 per night

### Understanding Missing Price Values
The missing prices are not random but follow a pattern:
```python
# Check correlation between availability and missing prices
missing_price = listings['price'].isnull()
closed_listings = listings['availability_365'] == 0

crosstab_result = pd.crosstab(missing_price, closed_listings)
print("Crosstab of missing prices vs. closed listings:")
print(crosstab_result)
```

### Pattern Analysis
The analysis reveals that:
- 1490 listings with no price are also closed (92% of all missing prices)
- Only 127 listings have no price but can be booked
- This pattern suggests the missing prices are not random

### Decision on Missing Prices
Three approaches were considered:
1. Remove all listings with missing prices (removes 35% of data)
2. Fill with median values (introduces artificial data)
3. Leave as-is (recommended approach)

The recommended approach leaves missing values as they are because:
- Statistical functions like mean and median automatically skip NaN values
- This preserves the actual dataset size for analysis
- It's honest about what data is available

## Neighborhood Analysis

### Price by Neighborhood Grouping
With cleaned price data, we can analyze pricing patterns across neighborhoods:

```python
# Group by neighborhood to calculate price statistics
by_area = listings.groupby('neighborhood').agg({
    'price': ['count', 'mean', 'median']
}).round(0)

# Flatten column names
by_area.columns = ['count', 'mean_price', 'median_price']

# Sort by median price (descending)
by_area = by_area.sort_values('median_price', ascending=False)

print("Price statistics by neighborhood:")
print(by_area)
```

### Analyzing Neighborhood Results
The results show:
- **Most expensive neighborhoods**: Farsta ($2214), Malm ($2119), Broma ($2089)
- **Least expensive neighborhoods**: Södermalm ($850), Norrmalm ($945), Svalöv ($1026)

### Important Statistical Insight
The mean price is higher than the median in all neighborhoods, indicating:
- Presence of extremely expensive outliers
- This creates a "penthouse effect" where outliers pull the mean upward

### Room Type Analysis
Price varies significantly by room type:
```python
# Compare prices by room type
room_type_analysis = listings.groupby('room_type')['price'].agg(['count', 'median']).round(0)
print("Price comparison by room type:")
print(room_type_analysis)
```

Results show:
- Entire home: $2168 median price
- Private room: $808 median price
- Shared room: Significantly lower

### Neighborhood Room Type Mix
Not all neighborhoods have the same mix of room types:
```python
# Calculate percentage of entire homes in each neighborhood
is_entire = listings['room_type'] == 'Entire home/apt'
entire_home_share = listings.groupby('neighborhood')[is_entire].mean() * 100
entire_home_share = entire_home_share.sort_values(ascending=False)
print("Percentage of entire homes by neighborhood:")
print(entire_home_share.round(1))
```

### Key Insights from Room Type Analysis
- Bromma has the highest percentage of entire homes (92%)
- Skärholmen has the lowest percentage (58%)
- This explains why Bromma has a higher median price even when individual rooms cost similarly

## Advanced Analysis: Like-with-Like Comparison

### Filtering for Specific Room Types
To determine if neighborhood itself matters independently of room type:
```python
# Filter only entire homes and analyze by neighborhood
entire_homes = listings[listings['room_type'] == 'Entire home/apt']
neighborhood_comparison = entire_homes.groupby('neighborhood')['price'].agg(['count', 'mean', 'median']).round(0)
print("Entire homes by neighborhood:")
print(neighborhood_comparison.sort_values('median', ascending=False))
```

### Why This Approach Matters
This technique allows for:
- **Controlled comparison**: Comparing similar properties (entire homes) across neighborhoods
- **Reduced confounding variables**: Eliminates the influence of room type differences
- **More accurate conclusions**: Better understanding of neighborhood-specific pricing factors

## Data Quality and Decision Making

### Importance of Understanding Data Sources
The dataset contains important information about data quality:
- Currency conversion issues (prices appear to be in USD but are likely Swedish kroner)
- Documentation gaps that require cross-referencing with external sources

### Statistical Reliability Considerations
When analyzing datasets with missing values:
1. Always check the count of valid values alongside summary statistics
2. Consider whether missing data is random or systematic
3. Document assumptions made during analysis
4. Report which data was used in calculations

## Summary of Cleaning Process

The complete cleaning workflow includes:
1. **Data loading**: Create working copy of original dataset
2. **Empty column removal**: Eliminate columns with no useful information
3. **Price transformation**: Convert text prices to numeric format
4. **Missing value analysis**: Understand patterns in missing data
5. **Statistical analysis**: Group by neighborhood and room type
6. **Controlled comparisons**: Analyze similar properties across locations

This systematic approach ensures that the final dataset is ready for meaningful analysis while maintaining transparency about data limitations and decisions made during processing.

### Final Verification
```python
# Verify completed cleaning process
print("Final dataset shape:", listings.shape)
print("Missing price values:", listings['price'].isnull().sum())
print("Price column type:", listings['price'].dtype)
```

This comprehensive approach to data cleaning ensures that subsequent analysis produces reliable and interpretable results.