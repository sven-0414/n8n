# Python Data Analysis Study Notes

## Data Cleaning Review

### Reading CSV Files with Proper Data Types
When reading CSV files, pandas doesn't automatically preserve data types. You must explicitly specify which columns should be parsed as dates.

**Rule**: Use `parse_dates` parameter in `pd.read_csv()` to convert string dates into datetime objects.

```python
import pandas as pd

listings = pd.read_csv('listings_clean.csv', 
                      parse_dates=['calendar_last_review', 'last_review'])
```

**Code Example**:
```python
# Reading the cleaned Airbnb data with proper date parsing
listings = pd.read_csv('listings_clean.csv', 
                      parse_dates=['calendar_last_review', 'last_review'])

print(listings.shape)  # (4574, 73)
print(listings.dtypes)
```

**Gotchas**:
- If you misspell column names in `parse_dates`, pandas will silently ignore them
- Date parsing may fail if the date format is inconsistent across rows
- Always verify that types were correctly parsed after reading

### Data Type Verification
After reading data, always check that your columns have the expected data types.

**Rule**: Verify data types using `.dtypes` attribute and compare with expected values.

```python
# Check that date columns are properly converted
print(listings[['calendar_last_review', 'last_review']].dtypes)
```

## Measures of Central Tendency

### Mean vs Median Comparison

**Rule**: The mean is affected by extreme values while the median represents the middle value in an ordered dataset.

**Mean Definition**: The arithmetic average calculated by summing all values and dividing by the count.

**Median Definition**: The middle value when data points are arranged in ascending order. For even number of observations, it's the average of the two middle values.

```python
import pandas as pd
import numpy as np

# Small example to illustrate difference
prices = [900, 1000, 1100, 1200, 1300]  # Five listings with similar prices
series = pd.Series(prices)
print(f"Mean: {series.mean()}")
print(f"Median: {series.median()}")

# Adding one extreme value
prices_with_extreme = [900, 1000, 1100, 1200, 1300, 40000]  # Luxury villa added
series_with_extreme = pd.Series(prices_with_extreme)
print(f"Mean with extreme: {series_with_extreme.mean()}")
print(f"Median with extreme: {series_with_extreme.median()}")
```

**Why This Matters**: 
- When data has outliers, median provides a better representation of typical values
- Mean gets pulled toward extreme values (as seen with the luxury villa)
- The difference between mean and median signals asymmetry in data distribution

### Real World Application - Airbnb Pricing
```python
# Using actual Airbnb data
print(f"Mean price: {listings['price'].mean():.0f}")
print(f"Median price: {listings['price'].median():.0f}")
```

## Measures of Spread and Distribution

### Percentiles and Quantiles

**Rule**: Percentiles divide data into 100 equal parts, quartiles divide into four equal parts.

**Percentile Definition**: A value below which a given percentage of observations in a group falls.

```python
# Basic description statistics
print(listings['price'].describe())

# Specific percentiles
percentiles = listings['price'].quantile([0.1, 0.25, 0.5, 0.75, 0.9, 0.99])
print(percentiles)
```

**Key Percentiles**:
- 25th percentile (Q1): First quartile
- 50th percentile (Q2): Median  
- 75th percentile (Q3): Third quartile

### Interquartile Range (IQR)

**Rule**: IQR = Q3 - Q1, measures the spread of middle 50% of data.

**Definition**: The width of the middle half of the data points, calculated as the difference between third and first quartiles.

```python
# Calculate IQR
q1 = listings['price'].quantile(0.25)
q3 = listings['price'].quantile(0.75)
iqr = q3 - q1

print(f"Q1: {q1:.1f}")
print(f"Q3: {q3:.1f}")
print(f"IQR: {iqr:.1f}")
```

**Why IQR Matters**: 
- More robust than standard deviation because it ignores outliers
- Provides insight into data spread without being influenced by extreme values

## Outlier Detection and Handling

### IQR Rule for Outliers

**Rule**: Outliers are values beyond 1.5 × IQR from the quartiles.

**Fence Calculation**:
- Lower fence = Q1 - 1.5 × IQR
- Upper fence = Q3 + 1.5 × IQR

```python
# Calculate fences using IQR rule
upper_fence = q3 + 1.5 * iqr
lower_fence = q1 - 1.5 * iqr

print(f"Upper fence: {upper_fence:.0f}")
print(f"Lower fence: {lower_fence:.0f}")

# Find outliers
outliers = listings[listings['price'] > upper_fence]
print(f"Number of outliers: {len(outliers)}")
```

**Analysis of Outliers**: 
- 132 listings (4.5% of total) were identified as outliers
- Most outliers are expensive entire homes/villas with high guest capacity
- These aren't errors but legitimate high-end listings

### Decision Making for Outliers

**Three Options for Handling Outliers**:
1. Remove them (loses 132 real listings)
2. Modify values (invents numbers)  
3. Keep them and mark them (recommended approach)

```python
# Recommended approach: Mark outliers without removing
listings['price_outlier'] = listings['price'] > upper_fence
print(f"Number of outliers marked: {listings['price_outlier'].sum()}")
```

**Why This Matters**: 
- Maintains data integrity by not discarding real observations
- Allows analysis flexibility - can choose to include or exclude outliers
- Transparent reporting of methodology

## Comparing Statistics With and Without Outliers

### Statistical Comparison Table

```python
# Create comparison table
comparison = pd.DataFrame({
    'All prices': [listings['price'].count(), 
                   listings['price'].mean(),
                   listings['price'].median(),
                   listings['price'].std()],
    'Without outliers': [listings[listings['price_outlier'] == False]['price'].count(),
                        listings[listings['price_outlier'] == False]['price'].mean(),
                        listings[listings['price_outlier'] == False]['price'].median(),
                        listings[listings['price_outlier'] == False]['price'].std()]
}, index=['Count', 'Mean', 'Median', 'Standard Deviation'])

print(comparison)
```

**Key Findings**:
- Mean drops from 2328 to 2027 (13% decrease) 
- Median drops from 1791 to 1700 (5% decrease)
- Standard deviation drops from 2160 to 1257 (42% decrease)

**Lesson**: 
- Median is resistant to outliers (hard to push around)
- Mean and standard deviation are sensitive to extreme values
- For reporting typical values, median is more reliable

## Group Analysis

### Describing Price by Room Type

**Rule**: Use `groupby()` with `describe()` to get summary statistics for each group.

```python
# Price analysis by room type
price_by_room = listings.groupby('room_type')['price'].describe()
print(price_by_room)
```

**Analysis Results**:
- Entire homes: Mean 2679, Median 2168, Max 40000
- Private rooms: Mean 1361, Median 808, Max 25998  
- Shared rooms: Only 19 listings (not enough for reliable statistics)

### Handling Missing Values in Group Analysis

**Rule**: Be aware that missing values can affect group membership.

```python
# Example of how missing values affect grouping
listings['price_outlier'] = listings['price'] > upper_fence
print("Number of outliers:", listings['price_outlier'].sum())
print("Missing price records:", listings['price'].isna().sum())
```

## Correlation Analysis

### Correlation Definition and Interpretation

**Rule**: Correlation coefficient ranges from -1 to +1, indicating strength and direction of relationship.

```python
# Calculate correlation matrix
columns = ['price', 'accommodates', 'bedrooms', 'beds', 'number_of_reviews',
           'availability_30', 'review_scores_rating']

correlation_matrix = listings[columns].corr()
print(correlation_matrix['price'])
```

**Interpretation Guide**:
- Close to +1: Strong positive relationship (both variables increase together)
- Close to 0: No meaningful relationship  
- Close to -1: Strong negative relationship (one variable increases while other decreases)

### Correlation Strength Scale
- Below 0.2: Weak correlation
- Around 0.5: Moderate correlation  
- Above 0.8: Strong correlation

**Example Results**:
- Price vs Accommodates: 0.58 (moderate positive)
- Price vs Bedrooms: 0.58 (moderate positive)  
- Price vs Beds: 0.58 (moderate positive)
- Price vs Number of Reviews: -0.09 (weak negative)
- Price vs Availability: 0.00 (no relationship)

### Visualizing Relationships

**Rule**: Correlation shows relationships but doesn't prove causation.

```python
# Group by guests to see price trends
guest_price = listings.groupby('accommodates')['price'].median().head(8).round(0)
print(guest_price)
```

**Observations**:
- Price increases with number of guests (500-600 more per guest)
- Correlation of 0.58 shows moderate relationship
- Outliers weaken the pattern (as shown by correlation difference)

## Advanced Analysis: Confounding Variables

### Understanding Causation vs Correlation

**Rule**: Just because two variables are correlated doesn't mean one causes the other.

```python
# Example: Superhost pricing comparison
superhost_analysis = listings.groupby('host_is_superhost')['price'].agg(['count', 'median'])
print(superhost_analysis)
```

### Confounding Variables

**Definition**: A third variable that affects both variables being studied, creating a spurious relationship.

**Example Analysis**:
```python
# Compare room type distribution between superhosts and regular hosts
room_type_share = listings.groupby('host_is_superhost')['room_type'].value_counts(normalize=True)
print(room_type_share)

# Compare same room types (entire homes only)
entire_home_comparison = listings[listings['room_type'] == 'Entire home/apt'].groupby('host_is_superhost')['price'].median()
print(entire_home_comparison)
```

**Key Finding**: 
- Superhosts have 74% entire homes vs 84% for regular hosts
- When comparing only entire homes, price difference reduced from 144 to 82
- Room type explains part of the pricing difference, but not all

### Importance of Multiple Variables

**Lesson**: Complex relationships require analyzing multiple factors simultaneously.

**Key Takeaway**: 
- Correlation ≠ causation
- Confounding variables can mask or create false relationships
- Always consider what other factors might influence your results

## Summary of Key Concepts

### When to Use Which Measure

| Measure | Best For | Sensitivity to Outliers |
|---------|----------|------------------------|
| Mean | Summarizing total values | Very sensitive |
| Median | Typical value representation | Resistant |
| Standard Deviation | Overall spread | Very sensitive |
| IQR | Robust spread measurement | Resistant |

### Practical Application

1. **For typical pricing**: Use median
2. **For understanding spread**: Use IQR  
3. **For detecting outliers**: Use IQR rule
4. **For relationships**: Use correlation coefficient
5. **For group comparisons**: Use groupby with describe()

### Data Integrity Practices

- Always verify data types after reading
- Check for missing values that might affect analysis
- Mark outliers rather than removing them
- Consider confounding variables in correlations
- Document methodology clearly for reproducibility

These concepts form the foundation of descriptive statistics and data analysis, providing tools to understand data patterns while being aware of limitations and potential biases.