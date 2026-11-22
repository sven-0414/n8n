# Python Data Visualization

## Introduction to Data Visualization Tools

### Overview of Visualization Libraries
Python offers several libraries for creating visual representations of data. The three main tools in this ecosystem are:
- **pandas**: Data handling and manipulation (stores the data)
- **matplotlib**: Low-level drawing library that actually creates the visual elements (drawing by hand with fine control)
- **seaborn**: High-level interface built on top of matplotlib that provides ready-made chart types with cleaner defaults

### Why These Tools Matter
Data visualization helps transform complex numerical data into easily understandable visual formats. While tables can show exact values, pictures can reveal patterns, trends, and relationships that are difficult to see in raw numbers. Visualizations make it easier to communicate findings to others.

## Setting Up the Environment

### Importing Required Libraries
```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Set seaborn theme for better-looking default charts
sns.set_theme(style="whitegrid")
```

### Common Import Patterns
- `import pandas as pd` - Standard pandas alias
- `import matplotlib.pyplot as plt` - Standard matplotlib pyplot alias  
- `import seaborn as sns` - Standard seaborn alias

## Working with Date Columns

### Loading Data with Date Parsing
When working with date columns, it's essential to parse them properly during data loading:

```python
# Load CSV with date parsing
listings = pd.read_csv('listings_clean.csv', parse_dates=['last_scraped', 'first_review', 'last_review'])
```

### Understanding the Data Structure
The dataset contains several date-related columns:
- `last_scraped`: Last time the listing was scraped
- `first_review`: First review date for each listing
- `last_review`: Most recent review date

## Basic Histogram Creation

### Creating Histograms with Matplotlib

#### Simple Histogram Example
```python
# Create a figure with one axis
fig, ax = plt.subplots()

# Draw histogram using matplotlib
ax.hist(listings['price'].dropna(), bins=50)

# Display the plot
plt.show()
```

#### Key Concepts:
- **bins**: The number of intervals to divide data into
- **dropna()**: Removes missing values (None/NaN) before plotting
- **hist()**: Matplotlib function that creates histograms

### Creating Histograms with Seaborn

#### Simple Seaborn Histogram Example
```python
# Create a figure with one axis
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

#### Key Concepts:
- **data parameter**: Pass the entire DataFrame instead of just column values
- **x parameter**: Specify which column to plot on x-axis
- **ax parameter**: Specify which axis to draw on
- **Seaborn automatically handles missing values**

## Advanced Histogram Features

### Adding Statistical Lines to Histograms

#### Calculating Key Statistics
```python
# Calculate quartiles and upper fence for outlier detection
q1 = listings['price'].quantile(0.25)
q3 = listings['price'].quantile(0.75)
iqr = q3 - q1
upper_fence = q3 + 1.5 * iqr

# Calculate median and mean
median_price = listings['price'].median()
mean_price = listings['price'].mean()

print(f"Upper fence: £{round(upper_fence)}")
print(f"Median price: £{round(median_price)}")
print(f"Mean price: £{round(mean_price)}")
```

### Adding Vertical Lines to Charts

#### Adding Statistical Reference Lines
```python
# Create figure and axis
fig, ax = plt.subplots(figsize=(10, 6))

# Draw histogram
sns.histplot(data=listings, x='price', bins=50, ax=ax)

# Add statistical lines
ax.axvline(median_price, color='black', linestyle='--', label=f'Median: £{round(median_price)}')
ax.axvline(mean_price, color='red', linestyle='-', label=f'Mean: £{round(mean_price)}')
ax.axvline(upper_fence, color='gray', linestyle=':', label=f'Upper Fence: £{round(upper_fence)}')

# Add legend
ax.legend()

# Set labels and title
ax.set_title('Distribution of Listing Prices with Statistical Measures')
ax.set_xlabel('Price (£)')
ax.set_ylabel('Number of Listings')

plt.show()
```

### Key Parameters for Vertical Lines:
- **axvline()**: Draws vertical line at specified x-value
- **color**: Sets the line color
- **linestyle**: Sets line style ('--', '-', ':')
- **label**: Adds label for legend
- **legend()**: Displays legend explaining line meanings

## Chart Components and Best Practices

### Essential Chart Elements

#### Complete Histogram with Labels and Title
```python
# Create figure with specific size
fig, ax = plt.subplots(figsize=(12, 8))

# Draw histogram
sns.histplot(data=listings, x='price', bins=50, ax=ax)

# Add statistical lines
ax.axvline(median_price, color='black', linestyle='--', label=f'Median: £{round(median_price)}')
ax.axvline(mean_price, color='red', linestyle='-', label=f'Mean: £{round(mean_price)}')

# Add title and axis labels
ax.set_title('Price Distribution of Accommodations')
ax.set_xlabel('Price (£) per Night')
ax.set_ylabel('Number of Listings')

# Add legend
ax.legend()

# Display the plot
plt.show()
```

### Chart Design Principles:
1. **Clear Title**: Should explain what the chart shows
2. **Proper Axis Labels**: Include units and clear descriptions
3. **Legend**: When multiple lines/elements are present
4. **Appropriate Bin Size**: More bins = more detail, fewer bins = broader overview

## Understanding Histogram Bins

### What Are Bins?
Bins are the intervals into which data values are grouped for histogram creation:
- Each bin represents a range of values
- The height of each bar shows how many data points fall within that range
- More bins provide more detailed information but can be cluttered
- Fewer bins give broader overview but less detail

### Bin Selection Strategy:
```python
# Try different bin counts to find optimal visualization
fig, axes = plt.subplots(2, 2, figsize=(15, 10))

# Different bin counts for comparison
bins_list = [20, 50, 100, 200]
titles = ['20 Bins', '50 Bins', '100 Bins', '200 Bins']

for i, (bins, title) in enumerate(zip(bins_list, titles)):
    axes[i//2, i%2].hist(listings['price'].dropna(), bins=bins)
    axes[i//2, i%2].set_title(title)
    axes[i//2, i%2].set_xlabel('Price (£)')
    axes[i//2, i%2].set_ylabel('Number of Listings')

plt.tight_layout()
plt.show()
```

## Complete Visualization Workflow

### Step-by-Step Process:
1. **Import Libraries**: pandas, matplotlib.pyplot, seaborn
2. **Load Data**: With proper date parsing using `parse_dates`
3. **Set Theme**: Use `sns.set_theme()` for better default appearance
4. **Create Figure and Axis**: `plt.subplots()`
5. **Draw Chart**: Using seaborn's plotting functions with appropriate parameters
6. **Add Labels and Titles**: Using axis methods
7. **Display Chart**: `plt.show()`

### Complete Working Example:
```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load data
listings = pd.read_csv('listings_clean.csv', parse_dates=['last_scraped', 'first_review', 'last_review'])

# Set theme for better-looking charts
sns.set_theme(style="whitegrid")

# Calculate statistics
q1 = listings['price'].quantile(0.25)
q3 = listings['price'].quantile(0.75)
iqr = q3 - q1
upper_fence = q3 + 1.5 * iqr
median_price = listings['price'].median()
mean_price = listings['price'].mean()

# Create figure and axis
fig, ax = plt.subplots(figsize=(12, 8))

# Draw histogram with seaborn
sns.histplot(data=listings, x='price', bins=50, ax=ax)

# Add statistical reference lines
ax.axvline(median_price, color='black', linestyle='--', linewidth=2, 
          label=f'Median: £{round(median_price)}')
ax.axvline(mean_price, color='red', linestyle='-', linewidth=2, 
          label=f'Mean: £{round(mean_price)}')
ax.axvline(upper_fence, color='gray', linestyle=':', linewidth=2, 
          label=f'Upper Fence: £{round(upper_fence)}')

# Add labels and title
ax.set_title('Distribution of Accommodation Prices - Showing Statistical Measures', fontsize=16)
ax.set_xlabel('Price (£) per Night', fontsize=12)
ax.set_ylabel('Number of Listings', fontsize=12)

# Add legend
ax.legend()

# Display the plot
plt.show()
```

### Why This Matters:
Understanding these visualization concepts is crucial because:
- Visualizations help identify data patterns that are invisible in raw numbers
- Proper labeling and formatting make charts accessible to others
- Statistical reference lines provide context for understanding distribution shapes
- The combination of matplotlib and seaborn allows both fine control and quick, clean results

## Troubleshooting Common Issues

### Installation Problems:
If you encounter "No module named" errors:
```bash
pip install matplotlib seaborn
```

Sometimes you may need to restart your IDE (like Visual Studio Code) after installation.

### Missing Data Handling:
Both matplotlib and seaborn automatically handle missing values when plotting, but it's good practice to be aware of what happens:
- `dropna()` removes all missing values explicitly
- Seaborn handles missing data internally without requiring explicit removal

### Figure Size Management:
```python
# Set figure size for better visualization
fig, ax = plt.subplots(figsize=(12, 8))  # Width, Height in inches
```

This ensures charts are readable and appropriately sized for presentation purposes.