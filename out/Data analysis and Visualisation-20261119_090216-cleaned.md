# Data Quality and Bias in Python

## Understanding Data Quality Problems

### What is Data Quality?
Data quality refers to whether a dataset contains what it claims to contain. It addresses three key aspects:
- **Completeness**: Are all required data points present?
- **Correctness**: Do the values accurately represent what they claim to be?
- **Consistency**: Is the data uniform and reliable across all entries?

### Why Data Quality Matters
Poor data quality leads to misleading conclusions. As mentioned in the lecture, if data is messy or biased, then the analysis results will also be messy or biased. Understanding data quality issues is crucial for making valid interpretations and decisions based on your data.

### Example of Data Quality Assessment
```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load the dataset with date parsing
listings = pd.read_csv('data/listings.csv', parse_dates=['first_review', 'last_review'])

# Check for missing values in specific columns
columns = ['price', 'reviews_per_month', 'number_of_reviews', 'availability_365']
missing_percentages = (listings[columns].isna().mean() * 100).round(1)
print(missing_percentages)
```

### Key Findings from Missing Data Analysis
- **Beds and bathrooms**: Only 2% and 0.2% missing respectively - normal and acceptable levels
- **Prices**: 35% missing (1,617 out of 4,574 listings) - significant issue requiring attention
- **Ratings**: 14% missing (644 out of 4,574 listings) - also substantial

## Understanding Bias in Data

### What is Bias?
Bias refers to systematic deviations from the truth in data collection or analysis. Unlike random errors (mistakes), bias consistently pushes results in one direction.

### Key Characteristics of Bias
- **Systematic**: Always goes in the same direction
- **Consistent**: Repeats with each new sample
- **Different from mistakes**: Random errors can cancel out, while bias accumulates

### Example: Price vs Availability Bias
```python
# Check if missing prices are related to availability
crosstab_result = pd.crosstab(
    listings['price'].isna(), 
    listings['availability_365'] == 0
)
print(crosstab_result)

# Results show:
# - 1,490 out of 1,617 missing prices (92%) are for listings with 0 available days
# - Only 4 out of 1,617 missing prices (0.25%) are for listings with available days
```

### Bias Implications
The data reveals that listings with no available days don't have prices because they're not actively listed on the website. This creates a systematic bias where:
- Our price calculations only represent active listings
- The median price of 1,791 applies only to 2,957 listings that actually have prices
- This is not wrong per se, but it's incomplete information

### Room Type Bias Analysis
```python
# Compare missing price rates by room type
price_missing_by_type = (listings['price'].isna().groupby(listings['room_type']).mean() * 100).round(0)
print(price_missing_by_type)

# Results show:
# - Entire homes: 38% missing prices
# - Private rooms: 25% missing prices
```

## Strategies for Handling Missing Data

### Three Approaches to Missing Values
1. **Remove rows**: Pandas automatically skips missing values in calculations (mean, median, describe)
2. **Fill with values**: Imputation using statistics like median or mean
3. **Keep as missing**: Maintain the original data state and document the issue

### Decision-Making Framework for Missing Data
The lecture emphasizes that the choice of approach matters less than making a deliberate decision based on understanding:
- What you choose to do
- Why you chose it
- How you explain it to readers

### Recommendation for Price Data
For price data with 35% missing values, the decision was to keep them as missing because:
- Imputing would create false data (one-third of the column)
- It's better to acknowledge and report this limitation
- The analysis should specify that results apply only to listings with prices

## Identifying Hidden Data Problems

### Types of Data Quality Issues Beyond Missing Values

#### 1. Duplicates Detection
```python
# Check for duplicate listings using ID column
duplicate_count = listings['id'].duplicated().sum()
print(f"Number of duplicate listings: {duplicate_count}")

# Result: Zero duplicates found - good news!
```

#### 2. Values That Look Correct But Mean Something Different
The lecture identified several examples where values appear correct but have misleading meanings:

**Zero Bathroom Issue:**
```python
# Investigate zero bathroom listings
zero_bath_listings = listings[listings['bathrooms'] == 0]
print(zero_bath_listings['bathroom_text'].value_counts())

# Results show:
# - "Zero shared baths" in 24 listings (guests may have private bathrooms)
# - "Zero baths" in 18 listings (unclear if they mean no bathroom or not recorded)
```

**Minimum Nights Misleading Values:**
```python
# Identify long stay listings
long_stay_listings = listings[listings['minimum_nights'] >= 28]
print(f"Long stay listings: {long_stay_listings['minimum_nights'].sum()}")
print(f"Percentage of total: {(long_stay_listings['minimum_nights'].count() / len(listings)) * 100:.1f}%")

# Results show:
# - 188 listings (4.1%) require minimum stays of 28 nights or more
# - These are typically monthly rentals, not suitable for short-term tourists
```

### Key Insight: Values Can Be Correct But Misleading
A value that is technically correct can still be misleading when the column name doesn't fully explain what the data represents. The example shows how "zero shared baths" could mean:
- No shared bathroom (but guest may have private one)
- Not recorded properly
- Different meaning than intended

## Comprehensive Data Quality Check Framework

### Four Essential Questions to Ask Before Analysis

1. **How much is missing and where?**
   - Identify missing data patterns across columns
   - Determine if missing values are random or systematic

2. **Are there duplicates?**
   - Check for repeated entries that could skew results
   - Ensure unique identification of each record

3. **Are there values that cannot be true?**
   - Look for impossible values (price = 0, future dates)
   - Identify logical inconsistencies in the data

4. **Are values true but mean something else than the column name says?**
   - Examine if column names accurately represent what's stored
   - Look for semantic mismatches between data and labels

### Why This Framework Matters
These questions help you:
- **Avoid false conclusions**: Understanding data limitations prevents overconfidence in results
- **Ensure proper interpretation**: Recognizing bias helps make appropriate comparisons
- **Communicate findings effectively**: Clear documentation of data issues builds trust with readers
- **Design better analysis**: Awareness of problems leads to more thoughtful approaches

## Addressing Rating Data Issues

### The Missing Rating Problem
```python
# Investigate relationship between reviews and ratings
rating_missing = listings['review_scores_rating'].isna()
no_reviews = listings['number_of_reviews'] == 0

# Cross-tabulate to see if missing ratings correspond to no reviews
crosstab_ratings = pd.crosstab(rating_missing, no_reviews)
print(crosstab_ratings)

# Results show perfect correlation:
# - All listings with 0 reviews also have missing ratings (644 listings)
# - All listings with reviews have ratings
```

### Implications for Rating Analysis
This discovery reveals that:
- Ratings only describe reviews that were actually given
- The rating column excludes listings without any reviews
- When analyzing ratings, we must specify whether we're looking at all reviewed listings or all listings

## Practical Recommendations for Data Analysis

### Documentation and Transparency
The lecture emphasizes the importance of documenting data quality issues:
- Always state how many records you're working with in your analysis
- Clearly explain what's included/excluded from calculations
- Justify your choices for handling missing or problematic data

### Example of Proper Reporting
Instead of saying "the median price is 1,791," it's better to say:
> "The median price is 1,791 for the 2,957 listings that have a price, representing 65% of all listings"

This transparency ensures readers understand the scope and limitations of your analysis.

### Final Thoughts on Data Quality
The lecture concludes that data quality issues are fundamental to understanding what data can and cannot tell us. The goal isn't to eliminate all problems, but to:
- Identify them systematically
- Understand their implications
- Make informed decisions about how to proceed with analysis
- Communicate limitations clearly to stakeholders

These skills are essential for any data analyst because they determine the validity of conclusions drawn from data and help prevent misleading interpretations that could affect important decisions.