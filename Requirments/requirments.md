# 📈 [Vendor Performance Analysis Business Requirement](Vendor_Performance_Analysis.ipynb)

## 📌 Business Requirements

### `1. Identify Brands that needs Promotiorlal or Pricing Adjustments which exhibit lower sales performance but higher profit margins.`

```python
# =========================
# BUSINESS REQUIREMENT 1
# Identify Underperforming Brands with High Profit Margins
# =========================
#
# Goal
# ----
# Identify brands that exhibit low sales performance but high average profit margins,
# indicating potential for promotional or pricing adjustments to boost volume.


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
plt.axhline(
    y=low_sales_threshold, 
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
![Requirment 1](https://github.com/Shivs0147/Vendor-Performance-Analysis-in-Python/blob/main/Snapshot_of_Requirment1.png?raw=true)
