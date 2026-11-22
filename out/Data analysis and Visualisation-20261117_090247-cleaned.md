# Python Data Analysis - Statistical Measures and Correlation

## Understanding Mean vs Median

### What is Mean and Median?
The **mean** is the average of all values in a dataset, calculated by summing all values and dividing by the number of values. The **median** is the middle value when data is arranged in order from smallest to largest.

### Code Example
```python
import pandas as pd

# Small example with 5 listings
prices = [900, 1000, 1100, 1200, 1300]
series = pd.Series(prices)
print("Mean:", series.mean())
print("Median:", series.median())

# Adding one luxury listing
prices_with_villa = [900, 1000, 1100, 1200, 1300, 40000]
series_with_villa = pd.Series(prices_with_villa)
print("Mean with villa:", series_with_villa.mean())
print("Median with villa:", series_with_villa.median())
```

### Key Rules and Gotchas
- **Mean**: Affected by extreme values (outliers) - each value contributes equally to the result
- **Median**: Robust against outliers - only cares about position/order, not actual values
- When data is symmetrically distributed, mean ≈ median
- When data has outliers, mean > median (positive skew) or mean < median (negative skew)

### Why This Matters
Understanding when to use mean vs median is crucial for accurate analysis. The median represents the "typical" value more reliably when extreme values exist, making it better for describing central tendency in skewed distributions.

## Working with Percentiles and Spread

### What are Percentiles?
Percentiles divide data into 100 equal parts. Common percentiles include:
- **25th percentile (Q1)**: First quartile - 25% of data is below this value
- **50th percentile (Q2)**: Median - 50% of data is below this value  
- **75th percentile (Q3)**: Third quartile - 75% of data is below this value

### Code Example
```python
import pandas as pd

# Load the cleaned Airbnb data
listings = pd.read_csv('listings_clean.csv', parse_dates=['last_review', 'calendar_last_review'])

# Get descriptive statistics for price
print(listings['price'].describe())

# Calculate specific percentiles
percentiles = listings['price'].quantile([0.1, 0.25, 0.5, 0.75, 0.9, 0.99])
print("Specific percentiles:")
print(percentiles)
```

### Key Rules and Gotchas
- Percentiles range from 0 to 1 (or 0% to 100%)
- Quantile function accepts values between 0 and 1
- Common percentiles: 25% (Q1), 50% (median), 75% (Q3)
- The **interquartile range (IQR)** = Q3 - Q1, represents the middle 50% of data

### Why This Matters
Percentiles help understand data distribution and identify outliers. They provide a more complete picture than just mean/median by showing how values are spread across the dataset.

## Interquartile Range (IQR) and Outliers

### What is IQR?
The **interquartile range** measures the spread of the middle 50% of data points, calculated as Q3 - Q1. It's a robust measure of variability that's not influenced by outliers.

### Code Example
```python
# Calculate IQR for price data
q1 = listings['price'].quantile(0.25)
q3 = listings['price'].quantile(0.75)
iqr = q3 - q1

print(f"Q1: {q1:.1f}")
print(f"Q3: {q3:.1f}")
print(f"IQR: {iqr:.1f}")

# Calculate outlier fences using IQR rule
lower_fence = q1 - 1.5 * iqr
upper_fence = q3 + 1.5 * iqr

print(f"Lower fence: {lower_fence:.0f}")
print(f"Upper fence: {upper_fence:.0f}")
```

### Finding Outliers with IQR Rule
Outliers are values that fall outside the range:
- Lower bound: Q1 - 1.5 × IQR  
- Upper bound: Q3 + 1.5 × IQR

### Code Example for Outlier Detection
```python
# Find outliers in price data
outliers = listings[listings['price'] > upper_fence]
print(f"Number of outliers: {len(outliers)}")

# Analyze outlier characteristics
print("Outlier room types:")
print(outliers['room_type'].value_counts())

print("Outlier guests:")
print(outliers['accommodates'].describe())
```

### Key Rules and Gotchas
- The 1.5 × IQR multiplier is a convention, not a strict rule
- Lower fence can be negative (which makes sense for price data)
- Outliers may be legitimate extreme values or data entry errors
- When filtering data, remember that missing values will also be excluded from outlier detection

## Handling Outliers in Analysis

### Decision Making Process
When dealing with outliers, you have three main options:
1. **Remove them** - Loses real data points and may bias results
2. **Modify them** - Invents data, which is generally not recommended  
3. **Keep and mark them** - Most honest approach for analysis

### Code Example for Marking Outliers
```python
# Mark outliers in the dataset
listings['price_outlier'] = listings['price'] > upper_fence

# Check how many outliers we have
print(f"Number of outliers: {listings['price_outlier'].sum()}")

# Compare statistics with and without outliers
stats_with_outliers = listings['price'].describe()
stats_without_outliers = listings[listings['price_outlier'] == False]['price'].describe()

print("With outliers:")
print(stats_with_outliers[['count', 'mean', 'median', 'std']])
print("\nWithout outliers:")
print(stats_without_outliers[['count', 'mean', 'median', 'std']])
```

### Why This Matters
Understanding outlier behavior helps make informed decisions about data treatment. The key insight is that outliers can significantly affect mean and standard deviation, but have minimal impact on median.

## Group Analysis by Room Type

### Describing Groups
When analyzing grouped data, you can use `groupby()` with descriptive statistics to understand differences between groups:

```python
# Describe price for each room type
room_type_stats = listings.groupby('room_type')['price'].describe()
print(room_type_stats)

# Compare specific statistics
comparison = listings.groupby('room_type')[['price', 'accommodates']].agg(['count', 'mean', 'median'])
print(comparison)
```

### Key Rules and Gotchas
- Groupby() creates separate summary statistics for each group
- The same descriptive functions work on grouped data
- Always check sample sizes - some groups may have very few entries (like shared rooms with only 19 listings)

## Correlation Analysis

### What is Correlation?
Correlation measures the strength and direction of the linear relationship between two variables. It ranges from -1 to +1:
- **+1**: Perfect positive correlation (variables move together)
- **0**: No correlation (variables are independent)
- **-1**: Perfect negative correlation (variables move in opposite directions)

### Code Example for Correlation
```python
# Calculate correlations between price and other columns
columns_to_check = ['price', 'accommodates', 'bedrooms', 'beds', 'number_of_reviews', 
                   'availability_365', 'review_scores_rating']

correlations = listings[columns_to_check].corr()
print(correlations['price'].round(2))
```

### Interpreting Correlation Results
```python
# Focus on correlation with price specifically
price_correlations = correlations['price'].drop('price')
print("Correlation with price:")
for col, corr in price_correlations.items():
    print(f"{col}: {corr:.2f}")
```

### Key Rules and Gotchas
- **Direction**: Positive correlation means variables move together; negative means opposite movement
- **Strength**: 
  - < 0.2: Weak correlation
  - 0.2-0.5: Moderate correlation  
  - > 0.8: Strong correlation
- **Correlation ≠ Causation**: Just because two variables are correlated doesn't mean one causes the other

### Understanding Correlation Limitations
```python
# Example showing how correlation can be misleading
# Compare superhosts vs regular hosts by room type
superhost_analysis = listings.groupby(['host_is_superhost', 'room_type'])['price'].mean()
print(superhost_analysis)

# Calculate share of entire homes by host type
entire_home_share = listings.groupby('host_is_superhost')['room_type'].apply(lambda x: (x == 'Entire home/apt').mean())
print("Share of entire homes:")
print(entire_home_share)
```

### Why This Matters
Correlation analysis helps identify relationships between variables, but understanding the context and potential confounding factors is essential. The room type example shows how a third variable can explain differences in correlation.

## Practical Application

### Complete Analysis Example
```python
import pandas as pd
import numpy as np

# Load data
listings = pd.read_csv('listings_clean.csv', parse_dates=['last_review', 'calendar_last_review'])

# Calculate basic statistics
mean_price = listings['price'].mean()
median_price = listings['price'].median()

print(f"Mean price: ${mean_price:.0f}")
print(f"Median price: ${median_price:.0f}")

# Calculate IQR and outliers
q1 = listings['price'].quantile(0.25)
q3 = listings['price'].quantile(0.75)
iqr = q3 - q1
upper_fence = q3 + 1.5 * iqr

# Mark outliers
listings['price_outlier'] = listings['price'] > upper_fence

# Compare with and without outliers
with_outliers = listings['price'].describe()
without_outliers = listings[listings['price_outlier'] == False]['price'].describe()

print("Comparison:")
print("With outliers - Mean:", with_outliers['mean'].round(0))
print("Without outliers - Mean:", without_outliers['mean'].round(0))
print("Change in mean:", (with_outliers['mean'] - without_outliers['mean']).round(0))

# Correlation analysis
numeric_cols = ['price', 'accommodates', 'bedrooms', 'beds', 'number_of_reviews']
correlation_matrix = listings[numeric_cols].corr()
print("\nCorrelation with price:")
print(correlation_matrix['price'].drop('price').round(2))
```

### Key Takeaways
1. **Mean vs Median**: Use median when outliers exist; use mean when data is symmetric
2. **Spread Measures**: IQR is robust against outliers, while standard deviation is sensitive
3. **Outlier Handling**: Mark rather than remove to preserve data integrity
4. **Group Analysis**: Compare statistics across categories (room types) for deeper insights
5. **Correlation Limitations**: Correlation shows relationship but not causation; consider confounding variables

This comprehensive approach ensures robust analysis that accounts for data characteristics and provides meaningful insights for decision-making.