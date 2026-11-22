# Python Data Cleaning - Airbnb Dataset

## Overview and Setup

### Loading and Preparing Data
When working with data, we always start by loading the dataset and creating a copy of the original for safekeeping. This ensures we can always revert back to the clean version if needed.

```python
import pandas as pd

# Load the original data
listings_raw = pd.read_csv('listings.csv')

# Create a working copy
listings = listings_raw.copy()

# Check the shape of the original dataset
print(listings.shape)  # Output: (4574, 84)
```

### Why This Approach Matters
Creating a backup copy is crucial because data cleaning involves making irreversible changes to datasets. Having the original allows us to restart if we make mistakes or want to try different approaches.

## Empty Columns Analysis and Removal

### Identifying Empty Columns
Empty columns contain no meaningful data and should be removed to improve data quality and readability. We use pandas methods to identify which columns have all missing values.

```python
# Identify completely empty columns
empty_columns = listings.columns[listings.isnull().all()]
print(len(empty_columns))  # Output: 15

# View the names of empty columns
print(empty_columns.tolist())
# Output: ['host_id', 'host_url', 'host_name', 'host_since', 'host_response_time',
#          'host_response_rate', 'host_acceptance_rate', 'host_is_superhost',
#          'host_has_profile_pic', 'host_identity_verified', 'calendar_updated',
#          'instant_bookable', 'require_guest_profile_picture', 'require_guest_phone_verification',
#          'license']
```

### Understanding the Problem
Empty columns often appear due to data collection issues or missing information. While they might seem harmless, they:
- Increase file size unnecessarily
- Make data exploration more difficult
- Can cause confusion during analysis

```python
# Check that we've correctly identified empty columns
print(listings[empty_columns].isnull().all())  # All True values confirm empty columns
```

### Decision Making for Empty Columns
The standard approach is to remove completely empty columns because they contain no useful information and removing them doesn't lose any data.

```python
# Remove all empty columns at once
listings = listings.drop(columns=empty_columns, axis=1)

# Verify the removal
print(listings.shape)  # Output: (4574, 69)
print(listings.isnull().sum().sum())  # Should be zero if no missing values remain
```

### Gotchas and Edge Cases
- Empty columns may contain only whitespace or special characters that appear non-empty but are functionally empty
- Some columns might have a pattern of missing data that isn't completely empty (partial missingness)
- Always double-check column names after dropping to ensure you're removing the correct ones

## Price Data Cleaning

### Initial Price Analysis
The price column in our Airbnb dataset contains text values with dollar signs and commas, making mathematical operations impossible without cleaning.

```python
# Examine price column structure
print(listings['price'].dtype)  # Output: object (text)
print(listings['price'].head(8))  # Shows example values like '$120.00', '$179.00'

# Show the problem clearly
print(listings['price'].iloc[0])  # Output: '$120.00'
```

### Step-by-Step Price Cleaning Process

#### Step 1: Remove Dollar Sign
The first step removes the dollar sign that makes price text instead of numeric.

```python
# Remove dollar sign from prices
listings['price'] = listings['price'].str.replace('$', '', regex=False)
print(listings['price'].head(3))  # Output: '120.00', '179.00', '83.00'
```

#### Step 2: Remove Commas
Commas in price values must be removed to allow proper numeric conversion.

```python
# Remove commas from prices
listings['price'] = listings['price'].str.replace(',', '', regex=False)
print(listings['price'].head(3))  # Output: '120.00', '179.00', '83.00'
```

#### Step 3: Convert to Numeric Type
Finally, convert the cleaned text to float type for mathematical operations.

```python
# Convert price column to numeric type
listings['price'] = pd.to_numeric(listings['price'], errors='coerce')
print(listings['price'].dtype)  # Output: float64
```

### Why This Method Matters
This systematic approach ensures we handle all text formatting issues that could cause conversion errors. Each step addresses a specific character or pattern, making the process transparent and reproducible.

### Quality Check with Descriptive Statistics
After cleaning, we verify our results using descriptive statistics:

```python
# Check the cleaned price data
price_stats = listings['price'].describe()
print(price_stats.round(1))
```

### Key Results from Price Cleaning
The cleaning revealed important insights about the dataset:
- 2957 out of 4574 listings have prices (64.6%)
- Median price: $179.10 per night
- Mean price: $232.70 per night
- Maximum price: $40,000 per night

### Currency Considerations and Real-World Implications
The dataset shows prices in US dollars, but these values are likely Swedish kronor (SEK) based on location context:

```python
# Analyze the most expensive listings
expensive_listings = listings.nlargest(3, 'price')[['name', 'room_type', 'price']]
print(expensive_listings)
```

## Missing Price Analysis

### Identifying Missing Values
We analyze why prices are missing by examining correlations with other variables:

```python
# Count missing prices
missing_prices = listings['price'].isnull().sum()
total_listings = len(listings)
percentage_missing = (missing_prices / total_listings) * 100

print(f"Missing prices: {missing_prices}")  # Output: 1617
print(f"Percentage missing: {percentage_missing:.1f}%")  # Output: 35.4%
```

### Correlation with Availability Data
The most significant pattern discovered was that missing prices correlate strongly with unavailable listings:

```python
# Create crosstab to show relationship between missing prices and availability
crosstab = pd.crosstab(
    listings['price'].isnull(),
    listings['availability_365'] == 0,
    margins=True
)
print(crosstab)
```

### Analysis Results
The analysis revealed that:
- 1490 listings have no price AND cannot be booked (92% of missing prices)
- 127 listings have no price but can still be booked
- 4 listings have a price but are closed
- 2953 listings have both a price and availability information

### Decision Making for Missing Prices
Based on our analysis, we decided to **leave the missing prices as they are** rather than removing or filling them because:
1. Removing would eliminate over a third of the data
2. Filling with estimates (median) would introduce artificial data that could skew results
3. Statistical functions like mean and median automatically ignore missing values
4. The missingness pattern shows these are systematically unavailable listings

## Neighborhood Analysis

### Price by Neighborhood Grouping
We examine price differences across neighborhoods to answer the question "which neighborhood is most expensive?"

```python
# Group by neighborhood and calculate statistics
by_area = listings.groupby('neighbourhood').agg({
    'price': ['count', 'mean', 'median']
}).round(0)

# Flatten column names for better readability
by_area.columns = ['count', 'mean_price', 'median_price']
by_area = by_area.sort_values('median_price', ascending=False)
print(by_area.head(10))
```

### Interpreting Results
The analysis shows:
- **Most expensive neighborhoods**: Farsta (2214 SEK), Malm (2119 SEK), Broma (2089 SEK)
- **Least expensive neighborhoods**: Södermalm (850 SEK), Norrmalm (945 SEK), Svalöv (1026 SEK)

### Important Statistical Insight
The difference between mean and median prices indicates outliers:
- Mean > Median in all neighborhoods due to a few very expensive listings
- This "penthouse effect" shows the presence of extreme values that skew averages

## Room Type Analysis

### Room Type Impact on Pricing
We examine how different room types affect pricing:

```python
# Analyze price by room type
room_type_analysis = listings.groupby('room_type').agg({
    'price': ['count', 'median']
}).round(0)
print(room_type_analysis)
```

### Key Findings
- Entire home/apartment: Median price 2168 SEK
- Private room: Median price 808 SEK
- Shared room: Median price 500 SEK

### Neighborhood Mix Analysis
Different neighborhoods have different proportions of room types:

```python
# Calculate share of entire homes per neighborhood
is_entire = listings['room_type'] == 'Entire home/apt'
entire_share = listings.groupby('neighbourhood')[is_entire].mean() * 100
entire_share = entire_share.sort_values(ascending=False)
print(entire_share.round(1))
```

### Results and Implications
- **Bromma**: 92% entire homes (most expensive)
- **Skärholmen**: 58% entire homes (less expensive)
- **Bongatan**: 61% entire homes

This shows that the neighborhood itself matters for pricing because it affects the mix of room types available.

## Data Quality Considerations

### Why This Matters
Understanding data quality issues is crucial for:
- Making appropriate analytical decisions
- Avoiding misleading conclusions
- Ensuring reproducible results
- Building trust in analysis findings

### Key Takeaways
1. **Data cleaning is systematic**: Each issue should be identified, addressed, and verified
2. **Missing data patterns matter**: Understanding why data is missing helps inform decisions about handling it
3. **Context matters**: Currency units must be understood based on data source documentation
4. **Statistical functions handle missing data automatically**: Most pandas operations skip missing values by default
5. **Multiple approaches are valid**: Sometimes leaving data as-is is the best approach

### Final Data Summary
After cleaning, our dataset:
- Has 69 columns (reduced from 84)
- Contains 4574 listings
- Price column is now numeric with 2957 complete values
- All empty columns have been removed
- Price data is ready for statistical analysis and visualization

This comprehensive approach to data cleaning ensures our analysis will be accurate and meaningful while maintaining the integrity of the original dataset.