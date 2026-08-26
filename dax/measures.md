# DAX Measures

Selected DAX measures from the AdventureWorks Power BI dashboard. This file highlights the calculations that best demonstrate KPI development, time intelligence, filter context, target analysis, what-if analysis, and dynamic reporting.

> Project type: Guided Power BI learning project.

## Core KPIs

### Total Revenue

Calculates revenue at row level using order quantity and the related product price.

```DAX
SUMX(
    'Sales Data',
    'Sales Data'[OrderQuantity] * 
    RELATED(
        'Product Lookup'[ProductPrice]
    )
)
```

### Total Profit

Calculates profit as total revenue minus total cost.

```DAX
[Total Revenue] - [Total Cost]
```

### Total Cost

Calculates total cost at row level using order quantity and the related product cost.

```DAX
SUMX(
    'Sales Data',
    'Sales Data'[OrderQuantity] *
    RELATED(
        'Product Lookup'[ProductCost]
    )
)
```

### Total Orders

Counts distinct order numbers.

```DAX
DISTINCTCOUNT(
    'Sales Data'[OrderNumber]
)
```

### Total Customers

Counts distinct customers appearing in sales data.

```DAX
DISTINCTCOUNT(
    'Sales Data'[CustomerKey]
    )
```

### Total Returns

Counts return records.

```DAX
COUNT(
    'Returns Data'[ReturnQuantity]
)
```

### Return Rate

Divides returned quantity by sold quantity.

```DAX
DIVIDE(
    [Quantity Returned],
    [Quantity Sold],
    "No Sales"
 )
```

### Average Revenue Per Customer

Calculates average revenue generated per unique customer.

```DAX
DIVIDE(
    [Total Revenue],
    [Total Customers]
)
```

## Time Intelligence

### Previous Month Revenue

Returns revenue for the previous month using DATEADD.

```DAX
CALCULATE(
    [Total Revenue],
    DATEADD(
        'Calendar Lookup'[Date],
        -1,
        MONTH
    )
)
```

### Previous Month Profit

Returns profit for the previous month using DATEADD.

```DAX
CALCULATE(
    [Total Profit],
    DATEADD(
        'Calendar Lookup'[Date],
        -1,
        MONTH
    )
)
```

### Previous Month Orders

Returns order count for the previous month using DATEADD.

```DAX
CALCULATE(
    [Total Orders],
    DATEADD(
        'Calendar Lookup'[Date],
        -1,
        MONTH
    )
)
```

### Previous Month Returns

Returns return count for the previous month using DATEADD.

```DAX
CALCULATE(
    [Total Returns],
    DATEADD(
        'Calendar Lookup'[Date],
        -1,
        MONTH)
)
```

### YTD Revenue

Calculates year-to-date revenue using DATESYTD.

```DAX
CALCULATE(
    [Total Revenue],
    DATESYTD(
        'Calendar Lookup'[Date])
)
```

### 10-Day Rolling Revenue

Calculates revenue across a rolling 10-day window.

```DAX
CALCULATE(
    [Total Revenue],
    DATESINPERIOD(
        'Calendar Lookup'[Date],
        MAX(
            'Calendar Lookup'[Date]),
            -10,
            DAY)
)
```

### 90-Day Rolling Profit

Calculates profit across a rolling 90-day window.

```DAX
CALCULATE(
    [Total Profit],
    DATESINPERIOD(
        'Calendar Lookup'[Date],
        MAX(
            'Calendar Lookup'[Date]),
            -90,
            DAY
    )
)
```

## Targets & Variance Analysis

### Revenue Target

Sets a revenue target at 110% of previous-month revenue.

```DAX
[Previous Month Revenue] * 1.1
```

### Revenue Target Gap

Measures the difference between current revenue and the revenue target.

```DAX
[Total Revenue] - [Revenue Target]
```

### Profit Target

Sets a profit target at 110% of previous-month profit.

```DAX
[Previous Month Profit] * 1.1
```

### Profit Target Gap

Measures the difference between current profit and the profit target.

```DAX
[Total Profit] - [Profit Target]
```

### Order Target

Sets an order target at 110% of previous-month orders.

```DAX
[Previous Month Orders] * 1.1
```

### Order Target Gap

Measures the difference between current orders and the order target.

```DAX
[Total Orders] - [Order Target]
```

## Filter Context & Business Logic

### All Orders

Removes Sales Data filters to calculate an all-orders baseline.

```DAX
CALCULATE(
    [Total Orders],
    ALL(
        'Sales Data'
    )
)
```

### % of all orders

Calculates the current order count as a percentage of all orders.

```DAX
DIVIDE(
    [Total Orders],
    [All Orders]
)
```

### All Returns

Removes Returns Data filters to calculate an all-returns baseline.

```DAX
CALCULATE(
    [Total Returns],
    ALL(
        'Returns Data'
    )
)
```

### % of all returns

Calculates the current return count as a percentage of all returns.

```DAX
DIVIDE(
    [Total Returns],
    [All Returns]
)
```

### High Ticket Orders

Counts orders for products priced above the overall average retail price.

```DAX
CALCULATE(
    [Total Orders],
    FILTER(
        'Product Lookup',
        'Product Lookup'[ProductPrice] > [Overall Retail Price]
    )
)
```

### Weekend Orders

Counts orders occurring on dates classified as weekends.

```DAX
CALCULATE(
    [Total Orders],
    'Calendar Lookup'[Weekend] = "Weekend"
)
```

### Bike Sales

Calculates quantity sold for the Bikes category.

```DAX
CALCULATE(
    [Quantity Sold],
    'Product Categories Lookup'[CategoryName] = "Bikes"
)
```

### Bike Returns

Calculates total returns for the Bikes category.

```DAX
CALCULATE(
    [Total Returns],
    'Product Categories Lookup'[CategoryName] = "Bikes"
)
```

### Bike Return Rate

Calculates return rate for the Bikes category.

```DAX
CALCULATE(
    [Return Rate],
    'Product Categories Lookup'[CategoryName] = "Bikes"
)
```

## What-if Analysis

### Price Adjustment (%) Value

Retrieves the selected value from the what-if price adjustment parameter.

```DAX
SELECTEDVALUE('Price Adjustment (%)'[Price Adjustment (%)], 0)
```

### Adjusted Price

Applies the selected price adjustment percentage to average retail price.

```DAX
[Average Retail Price] * (1 + 'Price Adjustment (%)'[Price Adjustment (%) Value])
```

### Adjusted Revenue

Recalculates revenue using the adjusted price.

```DAX
SUMX(
    'Sales Data',
    'Sales Data'[OrderQuantity]  
    *
    [Adjusted Price]
)
```

### Adjusted Profit

Recalculates profit after the price adjustment.

```DAX
[Adjusted Revenue] - [Total Cost]
```

## Dynamic Reporting & Customer Detail

### Dynamic Line Color

Uses SWITCH and SELECTEDVALUE to dynamically assign chart colors by selected product metric.

```DAX
SWITCH(
SELECTEDVALUE('Product Metric Selection'[Product Metric Selection Order]),
0, "#333333", // Orders
1, "#333333", // Revenue
2, "#333333", // Profit
3, "#E53E3E", // Returns (Highlights in Red)
4, "#E53E3E", // Return %
"#808080" // Fallback Gray
)
```

### Customer Metric Color

Uses SWITCH and SELECTEDVALUE to dynamically assign chart colors by selected customer metric.

```DAX
SWITCH(
    SELECTEDVALUE(
        'Customer Metric Selection'[Customer Metric Selection Order]
    ),
    0, "#2F2F2F",
    1, "#19D3D3",
    "#2F2F2F"
)
```

### Full Name (Customer Detail)

Displays a single selected customer name or a fallback label when multiple customers are selected.

```DAX
IF(
    HASONEVALUE(
        'Customer Lookup'[CustomerKey]
    ),
    MAX(
        'Customer Lookup'[Full Name]
    ),
    "Multiple Customers"
)
```

### Total Orders (Customer Detail)

Displays total orders only when one customer is selected.

```DAX
IF(
    HASONEVALUE(
        'Customer Lookup'[CustomerKey]
    ),
    [Total Orders],
    "-"
)
```

### Total Revenue (Customer Detail)

Displays total revenue only when one customer is selected.

```DAX
IF(
    HASONEVALUE(
        'Customer Lookup'[CustomerKey]
    ),
    [Total Revenue],
    "-"
)
```

## Notes

- The repository also includes the original CSV export containing all measures from the semantic model.
- Helper measures that add little additional portfolio value can remain in the CSV without being highlighted here.
