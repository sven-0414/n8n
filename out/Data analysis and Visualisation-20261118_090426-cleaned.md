# Python Data Visualization

## Overview of Data Visualization Tools in Python

### Introduction to Visualization Libraries

Data visualization is an essential part of data analysis because it helps us understand patterns, trends, and outliers in our datasets more quickly than looking at raw numbers alone. In this lesson, we'll explore three key Python libraries for creating visualizations:

1. **Pandas** - For handling and organizing data
2. **Matplotlib** - The foundational library for drawing plots (like drawing by hand)
3. **Seaborn** - Built on top of matplotlib, designed for quick, attractive statistical graphics

These tools work together to create professional-looking charts that clearly communicate your findings.

### Why Use Multiple Libraries?

- **Matplotlib**: Provides fine-grained control over every aspect of the plot but requires more code
- **Seaborn**: Offers ready-made chart types with beautiful defaults and easy-to-use syntax
- **Pandas**: Has its own plotting capabilities but is less flexible than the other two

We'll use Seaborn for creating the actual charts because it's easier to make them look good, and Matplotlib for customizing elements like titles, labels, and saving images.

## Setting Up Your Environment

Before starting any visualization work, ensure you have all necessary libraries installed:

```bash
pip install matplotlib seaborn pandas
```

### Importing Libraries

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Set a clean theme for better-looking charts
sns.set_theme(style="whitegrid")
```

## Understanding Chart Components

Before diving into creating visualizations, let's define key terms:

- **Figure**: The entire picture or canvas (like a sheet of paper)
- **Axes**: One chart on the figure with its own x-axis and y-axis
- **Histogram**: A type of chart that divides values into intervals called bins and counts how many fall in each interval
- **Bins**: Intervals used to group data in histograms

## Loading and Preparing Data

We begin by loading our dataset containing date information:

```python
# Load the CSV file with proper date parsing
listings = pd.read_csv('listings_clean.csv', parse_dates=['last_scraped', 'first_review', 'last_review'])

# Check that data loaded correctly
print(listings.shape)
```

This approach ensures that date columns are properly interpreted as datetime objects rather than strings.

## Creating Histograms with Matplotlib

A histogram shows the distribution of continuous numerical data by dividing values into bins and counting occurrences in each bin.

### Basic Histogram with Matplotlib

```python
import matplotlib.pyplot as plt

# Create a new figure with one axis
fig, ax = plt.subplots()

# Draw histogram using matplotlib
ax.hist(listings['price'].dropna(), bins=50)

# Display the plot
plt.show()
```

This code creates a basic histogram showing price distribution. The `dropna()` method removes missing values before plotting, ensuring only valid data points are included.

### Issues with Basic Matplotlib Histograms

Basic matplotlib histograms lack important features:
- No title
- No axis labels
- No meaningful context for readers

## Creating Better Histograms with Seaborn

Seaborn simplifies creating publication-ready charts:

```python
import seaborn as sns

# Create figure and axis
fig, ax = plt.subplots(figsize=(10, 6))

# Draw histogram using seaborn
sns.histplot(data=listings, x='price', bins=50, ax=ax)

# Add labels and title
ax.set_title('Distribution of Listing Prices')
ax.set_xlabel('Price (£)')
ax.set_ylabel('Number of Listings')

# Display the plot
plt.show()
```

### Advantages of Seaborn Over Matplotlib

1. **Automatic missing value handling**: Seaborn automatically skips NaN values without requiring explicit `dropna()`
2. **Better defaults**: Cleaner appearance with built-in styling
3. **Easier syntax**: Fewer lines of code needed to achieve the same result
4. **Integration with pandas**: Works directly with DataFrame objects

## Adding Statistical Information to Histograms

We often want to show summary statistics (mean, median) along with our distribution:

### Calculating Key Statistics

```python
# Calculate quartiles for outlier detection
q1 = listings['price'].quantile(0.25)
q3 = listings['price'].quantile(0.75)

# Calculate upper fence using IQR rule
upper_fence = q3 + 1.5 * (q3 - q1)

# Calculate median and mean
median_price = listings['price'].median()
mean_price = listings['price'].mean()

print(f"Median Price: £{median_price:.0f}")
print(f"Mean Price: £{mean_price:.0f}")
print(f"Upper Fence: £{upper_fence:.0f}")
```

### Adding Reference Lines to Histograms

```python
import matplotlib.pyplot as plt
import seaborn as sns

# Create figure and axis
fig, ax = plt.subplots(figsize=(10, 6))

# Draw histogram
sns.histplot(data=listings, x='price', bins=50, ax=ax)

# Add reference lines for statistics
ax.axvline(median_price, color='black', linestyle='--', linewidth=2, label=f'Median: £{median_price:.0f}')
ax.axvline(mean_price, color='red', linestyle='-', linewidth=2, label=f'Mean: £{mean_price:.0f}')
ax.axvline(upper_fence, color='gray', linestyle=':', linewidth=2, label=f'Upper Fence: £{upper_fence:.0f}')

# Add title and labels
ax.set_title('Distribution of Listing Prices with Summary Statistics')
ax.set_xlabel('Price (£)')
ax.set_ylabel('Number of Listings')

# Show legend
ax.legend()

# Display the plot
plt.show()
```

### Understanding Line Styles

- **Solid line (`-`)**: Standard continuous line
- **Dashed line (`--`)**: Broken line pattern
- **Dotted line (`:`)**: Points separated by gaps
- **Dash-dot line (`-.`)**: Alternating dashes and dots

## Code Patterns for Consistent Visualizations

Every chart follows a consistent pattern:

```python
# Pattern for creating charts:
fig, ax = plt.subplots(figsize=(width, height))  # Create figure with axis
# Seaborn command here (e.g., sns.histplot())
ax.set_title('Chart Title')                      # Add title
ax.set_xlabel('X-axis Label')                    # Add x-axis label
ax.set_ylabel('Y-axis Label')                    # Add y-axis label
plt.show()                                       # Display chart
```

## Advanced Concepts in Histograms

### Bins and Detail Levels

The number of bins affects how detailed your histogram appears:

- **Fewer bins**: Shows general shape but loses detail
- **More bins**: Reveals finer details but may create noise
- **Optimal bin count**: Usually between 10-50 depending on data size

```python
# Compare different bin counts
fig, axes = plt.subplots(1, 3, figsize=(15, 5))

sns.histplot(data=listings, x='price', bins=10, ax=axes[0])
axes[0].set_title('Fewer Bins (10)')

sns.histplot(data=listings, x='price', bins=50, ax=axes[1])
axes[1].set_title('Medium Bins (50)')

sns.histplot(data=listings, x='price', bins=100, ax=axes[2])
axes[2].set_title('More Bins (100)')

plt.tight_layout()
plt.show()
```

### Interpreting Long Tails

The histogram reveals the long tail that we discussed earlier - most listings are priced low while a few are extremely expensive. This skewness pulls the mean away from the median, which is important for understanding the true central tendency of the data.

## Common Gotchas and Best Practices

1. **Always check data integrity**: Use `dropna()` or handle missing values explicitly
2. **Choose appropriate bin sizes**: Too few bins lose detail; too many create noise
3. **Add meaningful labels**: Titles and axis labels help readers understand context
4. **Use consistent styling**: Maintain uniform appearance across multiple charts
5. **Include reference lines strategically**: Only add lines that provide useful insight

## Why This Matters for Data Analysis

Understanding how to visualize data distributions is crucial because:

1. **Pattern Recognition**: Visuals make it easier to spot trends and anomalies
2. **Communication**: Charts tell stories more effectively than numbers alone
3. **Decision Making**: Clear visualizations help stakeholders understand complex information quickly
4. **Statistical Insight**: Histograms reveal skewness, outliers, and overall data shape

The ability to create clear, informative visualizations will be essential throughout your data analysis journey and helps transform raw data into actionable insights.