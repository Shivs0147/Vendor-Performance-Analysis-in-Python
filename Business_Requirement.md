# 📈 [Vendor Performance Analysis Business Requirement](Vendor_Performance_Analysis.ipynb)

## 📌 Business Requirements

### `1. Identify Brands that needs Promotiorlal or Pricing Adjustments which exhibit lower sales performance but higher profit margins.`

```python
brand_performance = Vendor_Summary.groupby('Description').agg({
    'SalesDollars': 'sum',
    'ProfitMargin': 'mean'
}).reset_index()

brand_performance
```

- Group the DataFrame by 'Description'. Vendor_Summary is a DataFrame containing data about vendors, including their sales and profit margins. The 'Description' column likely represents the brand or product name. By using groupby('Description'), we create groups of rows where each group corresponds to one unique brand.
```python
brand_groups = Vendor_Summary.groupby('Description')
```

- Aggregate data within each group. For each brand group, we want to calculate two things: (A) The total sales dollars (sum of 'SalesDollars'), (B) The average profit margin (mean of 'ProfitMargin'). The agg() function lets us specify which aggregation function to apply to each column.
```python
aggregated_data = brand_groups.agg({
    'SalesDollars': 'sum',     # Sum up all sales for the brand
    'ProfitMargin': 'mean'     # Calculate the average profit margin for the brand
})
```

- Reset the index. After grouping, the brand names become the index of the DataFrame. reset_index() converts the index back into a regular column, which makes the data easier to work with and display.
```python
brand_performance = aggregated_data.reset_index()
```

```python
# =========================
# BUSINESS REQUIREMENT 1
# Identify Underperforming Brands with High Profit Margins
# =========================
#
# Goal
# ----
# Identify brands that exhibit low sales performance but high average profit margins, indicating potential for promotional or pricing adjustments to boost volume.


# Set the size of the figure and use a dark background theme
plt.figure(figsize=(12, 5))
plt.style.use('dark_background')

# Scatter plot of all brands (dim blue points for context)
sns.scatterplot(
    data=brand_performance, 
    x='SalesDollars', 
    y='ProfitMargin', 
    color='blue', 
    label='All Brands', 
    alpha=0.2  # Lower opacity to show density
)

# Scatter plot of the target brands (stand out in red)
sns.scatterplot(
    data=target_brands, 
    x='SalesDollars', 
    y='ProfitMargin', 
    color='red', 
    label='Target Brands'
)

# Horizontal line for high profit margin threshold
plt.axhline(
    y=high_profit_threshold, 
    color='white', 
    linestyle='--', 
    label='High Profit Threshold'
)

# Horizontal line for low sales threshold — This is probably a mistake.
# Probably meant to use `plt.axvline()` (a vertical line) for "Low Sales Threshold"
plt.axvline(
    x=low_sales_threshold, 
    color='white', 
    linestyle='--', 
    label='Low Sales Threshold'
)

# Axis labeling and title
plt.xlabel('Total Sales ($)')
plt.ylabel('Profit Margin (%)')
plt.title('Brands for Promotional or Pricing Adjustments')

# Show legend and plot
plt.legend()
plt.show()

```
![Requirement 1](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/blob/main/Snapshot_of_R1.png?raw=true)

**Summary:**

> This visualization identifies brands that have low sales but high profit margins, highlighting all brands in blue and target brands with low sales and high margins in red; the horizontal and vertical dashed lines represent the high profit and low sales thresholds, clearly showing which brands have potential for promotional or pricing adjustments to boost their sales volume.

### `2. Which vendors and brands are demonstrate the highest sales performance?`

```python
def format_dollars(value):
    if value >= 1_000_000:
        return f"${value/1_000_000:.2f}M"
    elif value >= 1_000:
        return f"${value/1_000:.2f}K"
    else:
        return f"${value:.2f}"

top_vendors = Vendor_Summary.groupby('VendorName')["SalesDollars"].sum().nlargest(10)
top_brands = Vendor_Summary.groupby('Description')["SalesDollars"].sum().nlargest(10)
top_vendors.apply(lambda x: format_dollars(x))
top_brands.apply(lambda x: format_dollars(x))
```

- Function to format large dollar values into readable strings:
     - If the value is 1 million or more, convert it to millions and append "M"
     - If the value is 1 thousand or more, convert it to thousands and append "K"
     - Otherwise, display the full amount with two decimal places

- Calculate total sales by each vendor. Group Vendor_Summary dataframe by 'VendorName', sum 'SalesDollars' per vendor, then select the top 10 vendors with the highest sales
```python
top_vendors = Vendor_Summary.groupby('VendorName')["SalesDollars"].sum().nlargest(10)
```

- Calculate total sales by each brand (description). Similar to above but grouping by 'Description' (which likely represents brands), summing 'SalesDollars' per brand and selecting the top 10
```python
top_brands = Vendor_Summary.groupby('Description')["SalesDollars"].sum().nlargest(10)
```

- (Optional) Apply formatting to sales numbers for readability. These lines apply the format_dollars function to top vendors and brands sales, but results aren't saved or printed here, so this step has no effect on output.
```python
top_vendors.apply(lambda x: format_dollars(x))
top_brands.apply(lambda x: format_dollars(x))
```

```python
# =========================
# BUSINESS REQUIREMENT 2
# Top Vendors and Brands by Sales Performance
# =========================
#
# Goal
# ----
# Identify the vendors and brands with the highest sales revenue contribution. Useful for understanding key revenue drivers.

# Create a new figure with specified size (width=10 inches, height=4 inches)
plt.figure(figsize=(10, 4))

# Apply a dark background style to the plot for better aesthetics
plt.style.use('dark_background')

# Plot the Top Vendors bar chart on the left subplot (1 row, 2 columns, position 1)
plt.subplot(1, 2, 1)

# Create a horizontal bar plot for top vendors using green color palette
ax1 = sns.barplot(y=top_vendors.index, x=top_vendors.values, palette='Greens')

# Set the title for the vendors plot
plt.title('Top 10 Vendors by Sales')

# Add formatted sales value labels centered inside each bar for vendors
for bar in ax1.patches:
    ax1.text(
        bar.get_width() / 2,                      # Position text at half the width of the bar (center horizontally)
        bar.get_y() + bar.get_height() / 2,      # Position text at vertical center of the bar
        format_dollars(bar.get_width()),          # Format the sales value into readable string (e.g. $1.23M)
        va='center', ha='center',                 # Align text vertically and horizontally centered
        fontsize=7, color='Black', weight='bold' # Text style: font size 7, black color, bold
    )

# Plot the Top Brands bar chart on the right subplot (1 row, 2 columns, position 2)
plt.subplot(1, 2, 2)

# Create a horizontal bar plot for top brands using a reversed orange color palette
ax2 = sns.barplot(y=top_brands.index, x=top_brands.values, palette='Oranges_r')

# Set the title for the brands plot
plt.title('Top 10 Brands by Sales')

# Add formatted sales value labels centered inside each bar for brands
for bar in ax2.patches:
    ax2.text(
        bar.get_width() / 2,                      # Position text at half the width of the bar
        bar.get_y() + bar.get_height() / 2,      # Position text at vertical center of the bar
        format_dollars(bar.get_width()),          # Format the sales value
        va='center', ha='center',                 # Center the text vertically and horizontally
        fontsize=7, color='Black', weight='bold' # Text style settings
    )

# Adjust the spacing between subplots to prevent overlap
plt.tight_layout()

# Display the complete plot with both subplots
plt.show()

```

![Requirement 2](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/blob/main/Snapshot_of_Requirment2.png?raw=true)

**Summary**

> This code shows the top ten vendors and brands by Sales Revenue, enabling you to see the significant elements of the business's revenue. It produces a side-by-side, dark theme bar chart with green bars for vendors and orange bars for brands and shows sales in compact dollar format directly on the bar. This straightforward easy to use visualisation assists to rapidly grasp which vendors and brands make the most sales and influence decisions on revenue focus.

### `3. Which vendors contribute the most to total purchase dollors?`

```python
Vendor_Performance = Vendor_Summary.groupby('VendorName').agg({
    'SalesDollars': 'sum',
    'TotalPurchaseDollors': 'sum',
    'GrossProfit': 'sum'
    
}).reset_index()

Vendor_Performance['PurchaseContribution%'] = Vendor_Performance['TotalPurchaseDollors'] / Vendor_Performance['TotalPurchaseDollors'].sum() * 100
Vendor_Performance = round(Vendor_Performance.sort_values('PurchaseContribution%', ascending=False), 2)

# Display top 10 vendors

top_vendors = Vendor_Performance.head(10)
top_vendors['SalesDollars'] = top_vendors['SalesDollars'].apply(format_dollars)
top_vendors['TotalPurchaseDollors'] = top_vendors['TotalPurchaseDollors'].apply(format_dollars)
top_vendors['GrossProfit'] = top_vendors['GrossProfit'].apply(format_dollars)
top_vendors

# Cummulative contribution of top vendors
top_vendors['CumulativeContribution%'] = top_vendors['PurchaseContribution%'].cumsum()
top_vendors
```

- Group data by 'VendorName' and aggregate important metrics. Sum total 'SalesDollars' for each vendor. Sum total 'TotalPurchaseDollors' (assumed to be purchase amounts) for each vendor. Sum total 'GrossProfit' for each vendor
      
```python
Vendor_Performance = Vendor_Summary.groupby('VendorName').agg({
    'SalesDollars': 'sum',
    'TotalPurchaseDollors': 'sum',
    'GrossProfit': 'sum'
}).reset_index()
```

- Calculate each vendor’s contribution percentage to total purchases. This shows what percent of total purchases each vendor represents
      
```python
Vendor_Performance['PurchaseContribution%'] = (
    Vendor_Performance['TotalPurchaseDollors'] / Vendor_Performance['TotalPurchaseDollors'].sum() * 100
)
```

- Sort vendors by their purchase contribution percentage in descending order. Then round all numeric values to 2 decimal places for cleaner display
      
```python
Vendor_Performance = round(Vendor_Performance.sort_values('PurchaseContribution%', ascending=False), 2)
```

- Select the top 10 vendors based on purchase contribution
      
```python
top_vendors = Vendor_Performance.head(10)
```

- Format sales, purchase, and profit numbers into readable dollar strings (e.g., $1.23M)
      
```python
top_vendors['SalesDollars'] = top_vendors['SalesDollars'].apply(format_dollars)
top_vendors['TotalPurchaseDollors'] = top_vendors['TotalPurchaseDollors'].apply(format_dollars)
top_vendors['GrossProfit'] = top_vendors['GrossProfit'].apply(format_dollars)
```

- Calculate cumulative purchase contribution percentage for top vendors. This shows the running total % contribution as vendors are added from highest to lower contributor

```python
top_vendors['CumulativeContribution%'] = top_vendors['PurchaseContribution%'].cumsum()
```

```python
# =========================
# BUSINESS REQUIREMENT 3
# Vendor Contribution to Total Purchases
# =========================
#
# Goal
# ----
# Determine which vendors contribute most to total procurement value and their cumulative contribution percentage.

# Create a figure and axis object with size 15x10 inches
fig, ax1 = plt.subplots(figsize=(15, 10))

# Apply dark background style to the plot for better visual appeal
plt.style.use('dark_background')

# Plot a bar chart on ax1 showing Purchase Contribution % for top vendors
# x-axis: vendor names, y-axis: purchase contribution percentage, with green color palette
sns.barplot(x=top_vendors['VendorName'], y=top_vendors['PurchaseContribution%'], palette='Greens', ax=ax1)

# Annotate each bar with the exact purchase contribution percentage value slightly below the top of the bar
for i, value in enumerate(top_vendors['PurchaseContribution%']):
    ax1.text(
        i,                      # x-position corresponding to the bar index
        value - 1,              # y-position slightly below the bar top (value minus 1)
        f"{value:.2%}",         # Text showing percentage formatted as percent with 2 decimals (e.g. 25.00%)
        ha='center',            # Horizontal alignment center
        va='bottom',            # Vertical alignment bottom (so text sits just below this position)
        fontsize=10,            # Font size of text
        color='Black',          # Text color black
        weight='bold'           # Bold text
    )

# Create a secondary y-axis sharing the same x-axis for plotting cumulative contribution
ax2 = ax1.twinx()

# Plot cumulative purchase contribution % as an orange dashed line with circle markers
sns.lineplot(
    x=top_vendors['VendorName'], 
    y=top_vendors['CumulativeContribution%'], 
    color='orange', marker='o', linestyle='dashed', label='Cumulative Contribution %', ax=ax2
)

# Rotate vendor names on x-axis by 90 degrees for better readability
ax1.set_xticklabels(top_vendors['VendorName'], rotation=90)

# Set y-axis label for primary axis (purchase contribution) in blue color
ax1.set_ylabel('Purchase Contribution %', color='blue')

# Set y-axis label for secondary axis (cumulative contribution) in orange color
ax2.set_ylabel('Cumulative Contribution %', color='orange')

# Set x-axis label
ax1.set_xlabel('Vendor Name')

# Set title for the plot
ax1.set_title('Pareto chart of Top Vendors by Purchase Contribution %')

# Draw a horizontal dashed green line at 100% cumulative contribution to indicate total coverage
ax2.axhline(y=100, color='green', linestyle='dashed', alpha=1)

# Display legend for the line plot on the upper center of the plot
ax2.legend(loc='upper center')

# Automatically adjust subplot parameters to give specified padding and prevent overlap
plt.tight_layout()

# Show the final plot
plt.show()

```

![Requirement 3](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/blob/main/Snapshot_of_Requirment3.png?raw=true)

**Summary**

> This visualization satisfies the business need of determining the key vendors making up procurement spend by illustrating a Pareto chart of percentages contributed by top vendors to the purchases. The individual contribution of each vendor is presented in the bar chart with percentages annotated, with a dashed orange line overlaid demonstrating the combined contribution of different vendors, in which high vendors tend to cover much of the total purchases combined. These dual y-axis, concise labelling, and percentage level line of 100% give easy interpretation of vendor concentration and indentify the most influential of vendors to focus the procurement strategies on.

### `4. How much of total procurement is dependent on the top vendors?`

```python
vendors = list(top_vendors['VendorName'].values)
purchase_contributions = list(top_vendors['PurchaseContribution%'].values)
total_purchase_contribution = sum(purchase_contributions)
remaining_contribution = 100 - total_purchase_contribution

vendors.append('Other Vendors')
purchase_contributions.append(remaining_contribution)
```

- Extract the list of vendor names from the 'top_vendors' DataFrame.
```python
vendors = list(top_vendors['VendorName'].values)
```

- Extract the list of purchase contribution percentages corresponding to each vendor.
```python
purchase_contributions = list(top_vendors['PurchaseContribution%'].values)
```

- Calculate the total purchase contribution percentage from the top vendors.
```python
total_purchase_contribution = sum(purchase_contributions)
```

- Calculate the remaining contribution percentage by subtracting from 100%.
```python
remaining_contribution = 100 - total_purchase_contribution
```

- Add a new vendor category 'Other Vendors' to account for all vendors outside the top list
```python
vendors.append('Other Vendors')
```

- Append the remaining contribution percentage to the purchase contributions list
```python
purchase_contributions.append(remaining_contribution)
```

```python
# =========================
# BUSINESS REQUIREMENT 4
# Purchase Dependency on Top Vendors
# =========================
#
# Goal
# ----
# Quantify how much of total procurement is accounted for by the top 10 vendors. Useful for dependency risk assessment.

# Create a figure and a set of subplots with a fixed size
fig, ax = plt.subplots(figsize=(6, 6))

# Use a dark background for the plot
plt.style.use('dark_background')

# Create the pie chart
wedges, texts, autotexts = ax.pie(
    purchase_contributions,               # Data for each wedge
    labels=vendors,                       # Labels for each wedge
    autopct='%1.1f%%',                    # Format to display percentage values
    startangle=90,                        # Rotate the start of the pie chart to 90 degrees
    colors=sns.color_palette("Greens_r", len(vendors)),  # Color palette using seaborn
    pctdistance=0.82                      # Distance of percentage labels from the center
)

# Customize the appearance of the percentage texts inside the pie chart
for autotext in autotexts:
    autotext.set_color('black')          # Set text color (can be changed to any color like 'white')
    autotext.set_fontweight('bold')      # Make text bold
    autotext.set_fontsize(7)             # Set font size

# Add a circle at the center to transform the pie chart into a donut chart
centre_circle = plt.Circle((0, 0), 0.65, fc='black')  # Radius 0.65 and black fill color
fig.gca().add_artist(centre_circle)                   # Add circle to the current axes

# Display the total contribution value at the center of the donut
plt.text(
    0, 0, f'Total Contribution: \n{total_purchase_contribution:.2f}%',  # Center text
    fontsize=12, ha='center', va='center', color='white', weight='bold'  # Styling for the text
)

# Set the title of the chart
plt.title("Top 10 Vendor's Purchase Contribution")

# Optional: Uncomment to ensure pie is drawn as a perfect circle
# plt.axis('equal')

# Show the final donut chart
plt.show()

```

![Requirement 4](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/blob/main/Snapshot_of_Requirment4.png?raw=true)

**Summary**

> Purchase Dependency on Top Vendors visualizes the percentage contribution of the top 10 vendors to the organization's total procurement spend using a donut chart. This graphical representation highlights procurement dependency by showing how much purchasing is concentrated among a few vendors. The chart includes a central annotation displaying the combined contribution of the top vendors, while the remaining portion is grouped under "Other Vendors." Such insight is critical for assessing supply chain risk, helping stakeholders identify overreliance on a limited number of suppliers and prompting diversification strategies if necessary.

### `5. Does purchasing in bulk reduce the unit price, and what is the optimal purchase volume for cost savings?`

```python
Vendor_Summary['UnitPurchasePrice'] = Vendor_Summary['TotalPurchaseDollors'] / Vendor_Summary['TotalPurchaseQty']

Vendor_Summary['OrderSize'] = pd.qcut(Vendor_Summary['TotalPurchaseQty'], 
    q=3,
    labels=['Small', 'Medium', 'Large']
)

Vendor_Summary[['OrderSize', 'TotalPurchaseQty']].sort_values(by='TotalPurchaseQty', ascending=False)

Vendor_Summary.groupby('OrderSize')[['UnitPurchasePrice']].mean().reset_index()
```

- Calculate Unit Purchase Price for each vendor. Creates a new column `UnitPurchasePrice`, which represents the average price paid per unit for each vendor.
```python
Vendor_Summary['UnitPurchasePrice'] = Vendor_Summary['TotalPurchaseDollors'] / Vendor_Summary['TotalPurchaseQty']
```

- Categorize vendors into order size groups based on purchase quantity. Using pd.qcut to split vendors into 3 quantile-based bins. Labels are assigned as 'Small', 'Medium', 'Large'
```python
Vendor_Summary['OrderSize'] = pd.qcut(Vendor_Summary['TotalPurchaseQty'], 
    q=3,
    labels=['Small', 'Medium', 'Large']
)
```

- View vendors sorted by purchase quantity in descending order
```python
Vendor_Summary[['OrderSize', 'TotalPurchaseQty']].sort_values(by='TotalPurchaseQty', ascending=False)
```

- Compute average unit purchase price for each order size category
```python
Vendor_Summary.groupby('OrderSize')[['UnitPurchasePrice']].mean().reset_index()
```

```python
# =========================
# BUSINESS REQUIREMENT 5
# Bulk Purchasing and Unit Price
# =========================
#
# Goal
# ----
# Assess whether purchasing larger volumes results in lower per-unit purchase prices, indicating bulk discount benefits.

# Create a new figure with a specified size (width=10, height=6 inches)
plt.figure(figsize=(10, 6))

# Use a dark background style for better contrast
plt.style.use('dark_background')

# Create a boxplot using seaborn
# x-axis: Order size category (Small, Medium, Large)
# y-axis: Unit purchase price
# data: Vendor_Summary DataFrame
# palette: 'Set2' is a pastel-style color palette
sns.boxplot(x='OrderSize', y='UnitPurchasePrice', data=Vendor_Summary, palette='Set2')

# Set the title of the plot
plt.title('Unit Purchase Price by Order Size')

# Label the x-axis
plt.xlabel('Order Size')

# Label the y-axis
plt.ylabel('Unit Purchase Price ($)')

# Adjust layout to prevent label cutoff
plt.tight_layout()

# Display the plot
plt.show()
```

![Requirement 5](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/blob/main/Snapshot_of_Requirment5.png?raw=true)

**Summary**

> Bulk Purchasing and Unit Price explores the connection between volume orders and unit price value by identifying vendors in the categories of Small, Medium, and Large order size and presenting unit costs as a boxplot depiction. The objective of this analysis is to identify the existence of bulk discount benefits by ascertaining whether increased purchase volumes translate into lower final prices on a per-unit basis. The visualization will allow procurement teams to understand pricing trends, variability, and outliers of the various sized orders allowing them to make intelligent decisions when considering using bulk purchasing to save money.

### `6. Which vendors have low inventory turnover, indicating excess stock and slow-moving products?`

```python
Vendor_Summary[Vendor_Summary['StcakTurnOver'] < 1]  # Filter vendors with Stack Turnover less than 1
.groupby('VendorName')                               # Group the filtered vendors by their names
[['StcakTurnOver']]                                  # Select only the 'StcakTurnOver' column
.mean()                                              # Calculate the mean Stack Turnover per vendor
.reset_index()                                       # Reset index to make 'VendorName' a column
.sort_values(by='StcakTurnOver', ascending=True)     # Sort vendors by Stack Turnover in ascending order
.head(10)                                            # Select the top 10 vendors with the lowest turnover
.round(2)                                            # Round the results to 2 decimal places

```
- This code identifies the top 10 vendors with the poorest inventory turnover, helping your team spot underperforming suppliers or products that may be tying up capital in slow-moving stock.

```python
Vendor_Summary[Vendor_Summary['StcakTurnOver']<1].groupby('VendorName')[['StcakTurnOver']].mean().reset_index().sort_values(by='StcakTurnOver', ascending=True).head(10).round(2)
```

```python
# =========================
# BUSINESS REQUIREMENT 6
# Excess Inventory & Inventory Turnover
# =========================
#
# Goal
# ----
# Identify vendors with low inventory turnover and high unsold stock value, which can tie up capital and increase storage costs.

# Create a new figure with specified size (width=12, height=6 inches)
plt.figure(figsize=(12, 6))

# Apply dark background style for better contrast
plt.style.use('dark_background')

# Plot a horizontal barplot showing average stack turnover by vendor
sns.barplot(
    x='StcakTurnOver',          # X-axis: average stack turnover values
    y='VendorName',             # Y-axis: vendor names
    data=low_turnover_vendors,  # Data source: filtered vendors with low turnover
    palette='Reds_r'            # Color palette: reversed Reds to highlight low turnover
)

# Add title to the plot with font size and weight
plt.title('Top 10 Vendors with Lowest Stack Turnover (< 1)', fontsize=14, weight='bold')

# Label the x-axis
plt.xlabel('Average Stack Turnover')

# Label the y-axis
plt.ylabel('Vendor Name')

# Adjust layout so labels and titles fit without overlap
plt.tight_layout()

# Render and display the plot
plt.show()

```

![Requirement 6](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/blob/main/Snapshot_of_Requirment6.png?raw=true)

**Summary**

> The top 10 vendors with an inventory turnover less than 1, indicating that their stock is moving very slowly. Such slow-moving inventory ties up capital and increases storage costs. This analysis helps identify vendors contributing to excess inventory, enabling better inventory management and cost control through targeted actions.

### `7. How much capital is locked in unsold inventory per vendor, and which vendors are contribute the most to it?`

```python
# =========================
# BUSINESS REQUIREMENT 7
# Vendor Classification by Profit Margins
# =========================
#
# Goal
# ----
# Classify vendors into Top-Performing and Low-Performing groups based on the upper and lower quartiles of SalesDollars.

Vendor_Summary['UnsoldedInventory'] = Vendor_Summary['TotalPurchaseQty'] - Vendor_Summary['SalesQuantity'] * Vendor_Summary['PurchasePrice']
print("Capital locked in unsold inventory per vendor:", format_dollars(Vendor_Summary['UnsoldedInventory'].sum()))

inventory_value_per_vendor = Vendor_Summary.groupby('VendorName')['UnsoldedInventory'].sum().reset_index()

inventory_value_per_vendor = inventory_value_per_vendor.sort_values(by='UnsoldedInventory', ascending=False)
inventory_value_per_vendor['UnsoldedInventory'] = inventory_value_per_vendor['UnsoldedInventory'].apply(format_dollars)
inventory_value_per_vendor.head(10)
```

- Calculate the unsold inventory value for each vendor.
```python
Vendor_Summary['UnsoldedInventory'] = Vendor_Summary['TotalPurchaseQty'] - Vendor_Summary['SalesQuantity'] * Vendor_Summary['PurchasePrice']
```

- Print the total capital locked in unsold inventory (summed across all vendors)
```python
print("Capital locked in unsold inventory per vendor:", format_dollars(Vendor_Summary['UnsoldedInventory'].sum()))
```

- Group by vendor name and calculate total unsold inventory value per vendor
```python
inventory_value_per_vendor = Vendor_Summary.groupby('VendorName')['UnsoldedInventory'].sum().reset_index()
```

- Sort vendors in descending order by their unsold inventory value
```python
inventory_value_per_vendor = inventory_value_per_vendor.sort_values(by='UnsoldedInventory', ascending=False)
```

- Format the unsold inventory values as dollar strings for better readability
```python
inventory_value_per_vendor['UnsoldedInventory'] = inventory_value_per_vendor['UnsoldedInventory'].apply(format_dollars)
```

- Display the top 10 vendors with the highest unsold inventory value
```python
inventory_value_per_vendor.head(10)
```

**Summary**

> Keeping it on the performance based vendor classification on basis of unsold stock and profit margin. It determines how much unsold stock is on hand per vendor indicating a capital invested in stock that could not move. The data is filtered by the vendor and arranged in descending order to show us the suppliers that have the highest unsold inventory value. This enables companies to appreciate poor-performing suppliers who could be impacting on overall profitability as a result of having surplus stocks. The final list of 10 provides an input to strategic decisions regarding vendor, clearing plans or renegotiations to maximize profitability.

### `8. What is the 95% confidence intervals for profit margins of top-performing and low-performing vendors?`

```python
top_threshold = Vendor_Summary['SalesDollars'].quantile(0.75)
low_threshold = Vendor_Summary['SalesDollars'].quantile(0.25)

top_vendors = Vendor_Summary[Vendor_Summary['SalesDollars']>= top_threshold]['ProfitMargin'].dropna().round(2)
low_vendors = Vendor_Summary[Vendor_Summary['SalesDollars']<= low_threshold]['ProfitMargin'].dropna().round(2)

# Find 95% confidence intervals for profit margins of top-performing and low-performing vendors

def confidence_interval(data, confidence=0.95):
    mean_val = np.mean(data)
    std_err = np.std(data, ddof=1) / np.sqrt(len(data)) # Calculate standard error
    t_critical = stats.t.ppf((1 + confidence) / 2, df=len(data) - 1)  # t critical value for two-tailed test
    margin_of_error = t_critical * std_err
    return mean_val, mean_val - margin_of_error, mean_val + margin_of_error
```

- Calculate SalesDollars Quartiles. We're dividing vendors based on their sales performance: (A) Vendors at or above the 75th percentile → Top-performing. (B) Vendors at or below the 25th percentile → Low-performing.

```python
top_threshold = Vendor_Summary['SalesDollars'].quantile(0.75)
low_threshold = Vendor_Summary['SalesDollars'].quantile(0.25)
```

- Filter Profit Margins for Top and Low Vendors. We filter only the ProfitMargin values for both groups. `dropna()` removes missing (NaN) values. `round(2)` formats the margins to 2 decimal places.

```python
top_vendors = Vendor_Summary[Vendor_Summary['SalesDollars'] >= top_threshold]['ProfitMargin'].dropna().round(2)
low_vendors = Vendor_Summary[Vendor_Summary['SalesDollars'] <= low_threshold]['ProfitMargin'].dropna().round(2)

```

- Define Confidence Interval Function. To compute the 95% confidence interval of the mean profit margin.
    - np.mean(data): Calculates the average.
    - std_err: Standard error of the mean.
    - t.ppf(...): Gets the t-critical value for the desired confidence level
    - margin_of_error: t-value × standard error.
    - Returns: Mean profit margin and the lower & upper bounds of its confidence interval.
 
```python
def confidence_interval(data, confidence=0.95):
    mean_val = np.mean(data)
    std_err = np.std(data, ddof=1) / np.sqrt(len(data))
    t_critical = stats.t.ppf((1 + confidence) / 2, df=len(data) - 1)
    margin_of_error = t_critical * std_err
    return mean_val, mean_val - margin_of_error, mean_val + margin_of_error
```

```python
# Calculate 95% confidence intervals for top and low-performing vendors
top_mean, top_lower, top_upper = confidence_interval(top_vendors)
low_mean, low_lower, low_upper = confidence_interval(low_vendors)

# Print confidence interval and mean for both vendor groups
print(f"Top Vendors 95% Confidence Interval: ({top_lower:.2f}, {top_upper:.2f}), Mean: {top_mean:.2f}")
print(f"Low Vendors 95% Confidence Interval: ({low_lower:.2f}, {low_upper:.2f}), Mean: {low_mean:.2f}")

# Create a new figure with specified size
plt.figure(figsize=(12, 6))

# Apply a dark background style for better contrast
plt.style.use('dark_background')

# -------------------------
# Plot Histogram for Top Vendors
# -------------------------

# Histogram with KDE (smoothed curve) for top vendors' profit margins
sns.histplot(top_vendors, kde=True, color="blue", bins=30, alpha=0.5, label="Top Vendors")

# Add vertical line for lower bound of CI
plt.axvline(top_lower, color="blue", linestyle="--", label=f"Top Lower: {top_lower:.2f}")

# Add vertical line for upper bound of CI
plt.axvline(top_upper, color="blue", linestyle="--", label=f"Top Upper: {top_upper:.2f}")

# Add vertical line for mean profit margin
plt.axvline(top_mean, color="blue", linestyle="-", label=f"Top Mean: {top_mean:.2f}")

# -------------------------
# Plot Histogram for Low Vendors
# -------------------------

# Histogram with KDE for low vendors' profit margins
sns.histplot(low_vendors, kde=True, color="red", bins=30, alpha=0.5, label="Low Vendors")

# Add vertical line for lower bound of CI
plt.axvline(low_lower, color="red", linestyle="--", label=f"Low Lower: {low_lower:.2f}")

# Add vertical line for upper bound of CI
plt.axvline(low_upper, color="red", linestyle="--", label=f"Low Upper: {low_upper:.2f}")

# Add vertical line for mean profit margin
plt.axvline(low_mean, color="red", linestyle="-", label=f"Low Mean: {low_mean:.2f}")

# Add plot title and axis labels
plt.title('95% Confidence Intervals for Profit Margins of Top and Low Performing Vendors')
plt.xlabel('Profit Margin')
plt.ylabel('Density')

# Add legend to explain lines/colors
plt.legend()

# Adjust layout to prevent overlap
plt.tight_layout()

# Display the plot
plt.show()

```

![Requirement 8](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/blob/main/Snapshot_of_Requirment8.png?raw=true)

**Summary**

> This is the one that compares the profit margin of high-performance and poor-performance vendors regarding their sales. Plotted as histograms with KDE (Kernel Density Estimation), the chart demonstrates that by calculating and visualizing 95 percent of the confidence interval of both groups, one will see the distribution and central tendency of profit margin. The upper histogram is the top vendors whereas the low vendors are the lower histogram and both are shown with vertical lines that are representative of the mean and the confidences of these vendors. Such analysis assists in defining whether increased sales are also accompanied by a higher profit margin every time, and it is highly useful when making the choice in terms of selecting and establishing a partnership with various vendors.

### `9. Is there a significant difference in profit margins between top-performing and low-performing vendors?`

> Hypothesis:
- **H₀(Null Hypothesis):** There is no significant difference between in the mean profit margins of top-performing and low-performing vendors.
- **H₁(Alternative Hypothesis):** There is a significant difference in the mean profit margins between top-performing and low-performing vendors.

```python
# =========================
# BUSINESS REQUIREMENT 9
# Correlation Analysis – Key Metrics
# =========================
#
# Goal
# ----
# Analyze how key numerical variables such as Purchase Quantity, Purchase Price, Sales Quantity, and Gross Profit are correlated with each other.
#
# Use a correlation heatmap to identify strong, weak, or negative relationships that may influence business decisions.

top_threshold = Vendor_Summary['SalesDollars'].quantile(0.75)
low_threshold = Vendor_Summary['SalesDollars'].quantile(0.25)

top_vendors = Vendor_Summary[Vendor_Summary['SalesDollars']>= top_threshold]['ProfitMargin'].dropna().round(2)
low_vendors = Vendor_Summary[Vendor_Summary['SalesDollars']<= low_threshold]['ProfitMargin'].dropna().round(2)

# Perform Two-Sample T-Test
t_stat, p_value = ttest_ind(top_vendors, low_vendors, equal_var=False)

# Display the results
print(f"T-statistic: {t_stat:.4f}, P-value: {p_value:.4f}")
if p_value < 0.05:
    print("Reject H₀: There is a significant difference in profit margins between top-performing and low-performing vendors.")
else:
    print("Fail to reject H₀: There is no significant difference in profit margins between top-performing and low-performing vendors.")
```

- Define threshold values to classify vendors by sales performance.

```python
top_threshold = Vendor_Summary['SalesDollars'].quantile(0.75)  # Top 25% vendors
low_threshold = Vendor_Summary['SalesDollars'].quantile(0.25)  # Bottom 25% vendors
```

- Filter profit margins for top-performing and low-performing vendors

```python
top_vendors = Vendor_Summary[Vendor_Summary['SalesDollars'] >= top_threshold]['ProfitMargin'].dropna().round(2)
low_vendors = Vendor_Summary[Vendor_Summary['SalesDollars'] <= low_threshold]['ProfitMargin'].dropna().round(2)
```

- Perform two-sample independent t-test (Welch’s t-test, unequal variances)

```python
t_stat, p_value = ttest_ind(top_vendors, low_vendors, equal_var=False)
```

- Print the t-statistic and p-value

```python
print(f"T-statistic: {t_stat:.4f}, P-value: {p_value:.4f}")
```

- Interpret the test result using a 0.05 significance level
  
```python
if p_value < 0.05:
    print("Reject H₀: There is a significant difference in profit margins between top-performing and low-performing vendors.")
else:
    print("Fail to reject H₀: There is no significant difference in profit margins between top-performing and low-performing vendors.")
```

**Summary**

> comparing the profit margins between high performers and low performers vendors based on their level of sales by a statistical approach. The values of SalesDollars were sorted to generate the top and bottom quartile as well as a two-sample t-test was done to compare their ProfitMargin value, where the question asked is whether the mean difference of profitability is significant. The tests give the t-statistic and the p-value that directs the null hypothesis rejection. When the p-value is less than 0.05, then that indicates that the profit margin between the two groups is significantly higher, which would give useful information in terms of the correlation of vendor performance with profitability which is one of the key factors in making decisions related to procurement and the vendor management process.
