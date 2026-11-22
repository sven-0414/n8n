# Data Quality and Bias in Python

## Understanding Data Quality Problems

### What is Data Quality?
Data quality refers to whether a dataset contains what it claims to contain, is complete, and is correct. It encompasses three key aspects:
- **Completeness**: Are all expected values present?
- **Correctness**: Do the values accurately represent what they're supposed to measure?
- **Consistency**: Are values consistent with each other and with real-world expectations?

### Why Data Quality Matters
Data quality directly impacts the validity of any analysis or conclusions drawn from the data. As mentioned in the lecture, if data is messy or biased, the results will be equally problematic. Understanding data quality helps analysts make informed decisions about how to handle their datasets and interpret their findings.

### Identifying Missing Values

#### The Rule for Finding Missing Values
To determine what's missing in a dataset, we use pandas' `isna()` function combined with statistical operations. The mean of boolean values (True/False) gives us the proportion of missing values, which can be converted to percentages by multiplying by 100.

```python
import pandas as pd

# Example showing how to calculate missing value percentages
columns = ['price', 'reviews', 'beds', 'bathrooms']
missing_percentages = (listings[columns].isna().mean() * 100).round(1)
print(missing_percentages)
```

#### Gotchas and Edge Cases
- **Random vs. Systematic Missingness**: The difference between missing values that occur randomly versus those that follow a pattern is crucial for interpretation.
- **Missing Data Patterns**: When examining missing data, it's important to look at why values are missing rather than just how many are missing.

#### Key Example: Price Missing Values
The lecture demonstrated that 35% of prices were missing (1,617 out of 4,574 listings). This is significant because it represents a third of the most important column. 

```python
# Analysis of missing price values
price_missing = listings['price'].isna().sum()
print(f"Missing prices: {price_missing} ({(price_missing/len(listings)*100):.1f}%)")

# Check correlation with availability
availability_analysis = pd.crosstab(
    listings['price'].isna(), 
    listings['availability_365'] == 0
)
print(availability_analysis)
```

#### Why This Matters for Analysis
Missing prices are not random - they're correlated with availability. Listings with zero available days don't have prices because the website doesn't display them when there's no availability. This means any analysis of price should specify it only applies to listings that actually have prices.

### Handling Missing Values

#### The Three Approaches
When dealing with missing values, analysts have three main approaches:
1. **Remove rows** - Pandas automatically skips missing values in calculations (mean, median, describe)
2. **Fill with values (imputation)** - Replace missing values with calculated statistics like median
3. **Keep as missing and report** - Maintain the missing data and clearly state which data was used

#### Choosing the Right Approach
The lecture emphasized that the choice matters less than making a conscious decision based on understanding the data and purpose of analysis.

```python
# Example approach for handling missing values
# Instead of saying "median price is $1,791"
# Say "median price is $1,791 for 2,957 listings that have a price"

# Counting valid vs missing values
valid_prices = listings['price'].notna().sum()
print(f"Valid prices: {valid_prices}")
print(f"Missing prices: {len(listings) - valid_prices}")

# Grouping by room type to see differences in missing data
room_type_missing = (listings.groupby('room_type')['price'].isna().mean() * 100).round(0)
print(room_type_missing)
```

### Bias in Data

#### Understanding Bias vs. Mistakes
Bias is not random error - it's systematic skewing of results in a particular direction. Unlike random mistakes, bias pushes results consistently in the same way.

#### Example: Rating Bias
The lecture showed that 14% of ratings were missing, and this was not random - these were exactly the listings without any reviews. This isn't a mistake but a logical consequence of how the data was collected.

### Data Validation Techniques

#### Checking for Duplicates
Duplicates can skew results by inflating counts. Every listing should have a unique ID.

```python
# Check for duplicates
duplicate_count = listings['id'].duplicated().sum()
print(f"Duplicate IDs: {duplicate_count}")

# If duplicates exist, remove them
listings_clean = listings.drop_duplicates(subset=['id'])
```

#### Validating Values Match Column Names
Values should make sense given what the column name implies.

#### Example: Zero Bathroom Values
The lecture demonstrated how "zero bathrooms" could be misleading. Hosts might write "0 shared baths" when they actually have private bathrooms, or "0 baths" when they haven't filled in information properly.

```python
# Analysis of zero bathroom values
zero_bath_listings = listings[listings['bathrooms'] == 0]
print("Zero bathroom listings:")
print(zero_bath_listings['bathroom_text'].value_counts())

# Check shared bath column
shared_bath_zero = listings[listings['shared_bath'] == 0]
print("Zero shared bath listings:")
print(shared_bath_zero['bathroom_text'].value_counts())
```

#### Identifying Unreasonable Values
Values can be technically correct but unreasonable given the context.

```python
# Check for extreme values in minimum nights
long_stay_listings = listings[listings['minimum_nights'] >= 28]
print(f"Long stay listings (≥28 nights): {len(long_stay_listings)} ({(len(long_stay_listings)/len(listings)*100):.1f}%)")

# This shows that some listings are only available for months, not suitable for typical tourists
```

## Data Quality Questions Framework

### The Three Core Questions About Any Dataset
1. **What is missing?** - Identify what data is absent or incomplete
2. **Who or what is not in the table?** - Determine what's excluded from the dataset
3. **Who made the data and why?** - Understand the source, collection method, and purpose

### The Four-Step Validation Process
Before starting any analysis, analysts should ask:
1. **How much is missing and where?** - Quantify and locate missing values
2. **Are there duplicates?** - Check for redundant entries
3. **Are there values that cannot be true?** - Identify impossible or unreasonable values
4. **Are there values that are true but mean something else than the column name says?** - Find misleading information

### Why This Matters for Data Analysis
The lecture emphasized that data quality issues are fundamental to understanding what data can and cannot tell us. The goal is not just to clean data, but to understand its limitations and communicate those clearly.

## Practical Implementation

### Complete Example Workflow
```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load data with date parsing
listings = pd.read_csv('data/listings.csv', 
                      parse_dates=['first_review', 'last_review'])

# Check basic structure
print("Dataset shape:", listings.shape)

# Analyze missing values
missing_values = (listings.isna().mean() * 100).round(1)
print("Missing value percentages:")
print(missing_values)

# Check for duplicates
duplicates = listings['id'].duplicated().sum()
print(f"Duplicate IDs: {duplicates}")

# Analyze specific issues
# Price and availability correlation
availability_price = pd.crosstab(
    listings['price'].isna(), 
    listings['availability_365'] == 0
)
print("Price vs Availability correlation:")
print(availability_price)

# Check room type differences in missing prices
room_type_missing = (listings.groupby('room_type')['price'].isna().mean() * 100).round(0)
print("Missing price by room type:")
print(room_type_missing)

# Analyze ratings specifically
rating_analysis = pd.crosstab(
    listings['review_scores_rating'].isna(),
    listings['number_of_reviews'] == 0
)
print("Rating vs review correlation:")
print(rating_analysis)

# Check for extreme values
long_stay = (listings['minimum_nights'] >= 28).sum()
print(f"Listings requiring ≥28 nights: {long_stay} ({(long_stay/len(listings)*100):.1f}%)")
```

### Key Takeaways
- Data quality issues are systematic problems that affect the validity of analysis
- Missing values can be random or systematic - understanding the difference is crucial
- Values that appear correct might still be misleading due to unclear data collection methods
- Always document your data validation process and decisions about handling missing or questionable data
- The goal is not just to clean data but to understand its limitations and communicate them clearly

This comprehensive approach to data quality ensures that analyses are both technically sound and transparent about their assumptions and limitations.