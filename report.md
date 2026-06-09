# E-Commerce Customer Experience Analytics

## Complete Power BI Consulting Report & Implementation Guide

---

> **Deliverables Package:**
> | File | Purpose |
> |------|---------|
> | `report.md` | This complete consulting report |
> | `PowerBI_Build_Guide.md` | Step-by-step Power BI Desktop instructions (15 min setup) |
> | `DAX_Measures.txt` | 80+ copy-paste ready DAX measures |
> | `Dim_User.csv` | Cleaned user dimension (49,999 rows) |
> | `Dim_Product.csv` | Cleaned product dimension (2,999 rows) |
> | `Dim_Date.csv` | Auto-generated date dimension (1,461 rows) |
> | `Fact_Session.csv` | Cleaned session fact table (299,999 rows) |
>
> **Note on .pbix files:** The `.pbix` format is Microsoft's proprietary closed format that requires the VertiPaq/Analysis Services engine. It cannot be generated outside Power BI Desktop. Instead, this package provides **cleaned data + complete build instructions** that will get you to a working dashboard in ~15 minutes of copy-paste work.

---

## 1. Executive Summary

An online retail brand with **2M+ monthly active users** across **6 geographic markets** is experiencing declining retention, suboptimal conversion rates, and decreasing Customer Lifetime Value (CLV) despite growing web traffic. This report delivers a complete **Power BI consulting solution** including data transformation specifications, star-schema data warehouse design, KPI definitions, and a **step-by-step Power BI build guide** with 80+ ready-to-paste DAX measures.

**Key Findings:**

| Metric | Current Value | Industry Benchmark | Gap |
|--------|-------------|-------------------|-----|
| Conversion Rate | 17.95% | 2-3% (typical) | **Strong** |
| Cart Abandonment Rate | 49.94% | ~70% | **Better than avg** |
| Bounce Rate | 50.00% | 40-50% | **At threshold** |
| Average Order Value | $2,004.67 | $100-150 | **Premium positioning** |
| Repeat Purchase Rate | ~35% (Returning+Loyal) | 20-30% | **Strong loyalty** |

---

## 2. Business Problem Understanding

### 2.1 Problem Statement

The business faces three core challenges:

1. **Declining User Retention** -- Despite 2M+ MAU, retention curves show users churn after initial purchases. Session-level data reveals a 50% bounce rate, indicating landing page and funnel friction.

2. **Suboptimal Conversion Rates** -- While the 17.95% overall conversion appears strong, cart abandonment sits at 49.94% and checkout initiation at 49.95%. The conversion funnel leaks at critical decision points.

3. **Decreasing CLV** -- Customer lifetime value tiers show no correlation with actual lifetime value spend, suggesting the segmentation model is broken. Acquisition channels may be attracting low-value users.

### 2.2 Key Business Questions to Answer

| Priority | Question | Dashboard |
|----------|----------|-----------|
| P0 | What is the true end-to-end conversion funnel performance? | Conversion Dashboard |
| P0 | Which customer segments are most valuable and why? | Customer 360 |
| P0 | How do acquisition channels compare on quality vs. quantity? | Acquisition Analysis |
| P1 | Is the A/B testing program showing statistically significant results? | Experimentation |
| P1 | Which products drive revenue and which are inventory risks? | Product Performance |
| P1 | What is the revenue trend and forecast trajectory? | Revenue Intelligence |
| P2 | How does device and geographic behavior vary? | Behavioral Insights |
| P2 | What is the discounting impact on margin? | Promo Deep-Dive |

---

## 3. Data Source Overview

### 3.1 Dataset Inventory

| Table | Rows | Columns | Grain | Type |
|-------|------|---------|-------|------|
| `users.csv` | 50,001 | 16 | One row per user | **Dimension** |
| `products.csv` | 3,001 | 17 | One row per product | **Dimension** |
| `sessions.csv` | 300,001 | 30 | One row per session | **Fact** |

### 3.2 Date Coverage

| Table | Start Date | End Date | Span |
|-------|-----------|---------|------|
| users | 2023-01-01 | 2025-12-31 | 3 years |
| products | N/A (static) | N/A (static) | Snapshot |
| sessions | 2023-01-01 | 2026-05-08 | 3+ years |

### 3.3 Column Reference Map

**Users Table (User Dimension)**

| Column | Type | Business Meaning | Quality |
|--------|------|-----------------|---------|
| User_ID | Text | Unique customer identifier | 1 null, 1 duplicate |
| Signup_Date | Date | Account creation date | 2,500 nulls (5%) |
| Customer_Segment | Text | Lifecycle: New/Returning/Loyal/Inactive | Clean |
| CLV_Tier | Text | Tier: Low/Medium/High/Premium | **Not correlated with LTV** |
| Preferred_Device | Text | Desktop/Mobile/Tablet | Clean |
| Primary_Location | Text | Geographic region | Clean (6 regions) |
| Acquisition_Source | Text | How user found the brand | Clean (5 sources) |
| Browser_Preference | Text | Preferred browser | Clean |
| A/B_Test_Assignment | Text | Test group: Control/Variant A/Variant B | Clean |
| Email_Subscribed | Text | Opt-in status | 16,784 nulls; dirty values |
| First_Purchase_Date | Date | First transaction date | 7,500 nulls (15%) |
| Total_Visits_Count | Number | Historical visit count | 1 null |
| Avg_Session_Duration | Number | Average session length (seconds) | Clean |
| Avg_Scroll_Depth | Number | Average page scroll percentage | Clean |
| Lifetime_Orders | Number | Total historical orders | Clean |
| Lifetime_Value | Currency | Total revenue contributed | Clean, $0.15-$25K |

**Products Table (Product Dimension)**

| Column | Type | Business Meaning | Quality |
|--------|------|-----------------|---------|
| Product_ID | Text | Unique product identifier | 1 duplicate |
| Product_Category | Text | Top-level category | 1 null |
| Product_Subcategory | Text | Sub-category | Mixed casing issues |
| Base_Price | Currency | Retail price | **Has $ prefix on some rows** |
| Cost_Price | Currency | Manufacturing cost | Clean |
| Profit_Margin | Decimal | Margin percentage | Clean |
| Seasonal_Flag | Text | Season tag | 559 nulls (18.6%) |
| Inventory_Level | Number | Units in stock | Clean |
| Reorder_Threshold | Number | Reorder trigger level | Clean |
| Lead_Time | Number | Days to restock | Clean |
| Product_Rating | Decimal | Customer rating | Clean |
| Review_Count | Number | Number of reviews | Clean |
| Return_Rate | Decimal | Return percentage | Clean |
| Shipping_Weight | Decimal | Weight in kg | Clean |
| Size_Dimensions | Text | Physical dimensions | 1,002 nulls (33.4%) |
| Color_Variants | Text | Available colors | Single value only |
| Size_Variants | Text | Available sizes | Single value only |

**Sessions Table (Session Fact)**

| Column | Type | Business Meaning | Quality |
|--------|------|-----------------|---------|
| Session_ID | Text | Unique session identifier | 1 duplicate |
| User_ID | Text | Foreign key to Users | 1 null |
| Timestamp | DateTime | Session timestamp | Clean |
| Session_Duration | Number | Length in seconds | Clean (20-3600s) |
| Device_Type | Text | Device used | Clean |
| Browser_Version | Text | Browser + version | Clean |
| Geographic_Location | Text | User location at session | Clean |
| Traffic_Source | Text | Session referral source | Clean |
| Marketing_Campaign_ID | Text | Campaign attribution | 75,340 nulls (25.1%) |
| Entry_Page | Text | Landing page type | Clean |
| Exit_Page | Text | Exit page type | Clean |
| Bounce_Flag | Text | YES/No single-page session | Clean |
| Pages_Viewed | Number | Pages in session | Clean (1-24) |
| Total_Scroll_Depth | Number | Scroll percentage | Clean |
| Products_Viewed | Text | Product ID browsed | Clean |
| Primary_Product_Category | Text | Category browsed | Clean |
| Add_to_Cart_Count | Number | Cart additions | Clean (0-7) |
| Cart_Abandonment_Flag | Text | Left without buying | Clean |
| Checkout_Initiated_Flag | Text | Started checkout | Clean |
| Purchase_Flag | Text | Completed purchase | Clean |
| Purchase_Amount | Currency | Transaction total | 246,162 nulls (82% non-buyers) |
| Items_Purchased | Number | Units bought | 246,162 nulls |
| Discount_Applied | Currency | Discount used | 59,886 nulls (20%) |
| Payment_Method | Text | Payment type | 246,162 nulls |
| Previous_Visits_Count | Number | Visits before this session | Clean |
| Days_Since_Last_Visit | Number | Recency metric | Clean (0-120) |
| Customer_Segment | Text | Segment at session time | **NOT consistent with Users table** |
| CLV_Tier | Text | Tier at session time | **NOT consistent with Users table** |
| Product_Category_Viewed | Text | Category browsed (redundant) | Clean |
| A/B_Test_Group | Text | Experiment group at session | Clean |

---

## 4. Data Quality Assessment

### 4.1 Critical Issues (Must Fix Before Analysis)

| # | Issue | Table | Column | Impact | Fix Strategy |
|---|-------|-------|--------|--------|-------------|
| 1 | **CLV_Tier in sessions is random** -- shows no correlation with users.Lifetime_Value | Sessions | CLV_Tier | KPIs using session-level CLV will be completely wrong | Recalculate CLV_Tier from Users table using DAX `RELATED()` |
| 2 | **Customer_Segment in sessions is random** -- does not match users.Customer_Segment | Sessions | Customer_Segment | Segment-level conversion analysis is meaningless | Join to Users dimension to get true segment |
| 3 | **Base_Price has $ prefix** | Products | Base_Price | Cannot aggregate; treated as text | Remove `$` and convert to decimal |
| 4 | **1 duplicate Product_ID** | Products | Product_ID | Join ambiguity | Deduplicate on Product_ID, keep first |
| 5 | **1 duplicate Session_ID** | Sessions | Session_ID | Fan-out on join | Deduplicate on Session_ID, keep first |
| 6 | **1 duplicate User_ID in users** | Users | User_ID | Many-to-one relationship fails | Deduplicate on User_ID, keep first |
| 7 | **2,500 missing Signup_Dates** | Users | Signup_Date | Cannot calculate user tenure | Create `Is_Signup_Date_Missing` flag; fill with `First_Purchase_Date` where available |
| 8 | **7,500 missing First_Purchase_Dates** | Users | First_Purchase_Date | Cannot calculate time-to-first-purchase | Leave as null; create `Has_Purchased` flag |
| 9 | **16,784 dirty Email_Subscribed values** | Users | Email_Subscribed | Contains `"  YES  "` and `"invalid_email.com"` | Normalize to `Yes/No/Unknown` |
| 10 | **Product_Subcategory mixed casing** | Products | Product_Subcategory | Filtering breaks (`SubCat-1` vs `SUBCAT-4`) | Standardize to consistent casing |

### 4.2 Medium Issues (Should Fix)

| # | Issue | Table | Column | Impact | Fix Strategy |
|---|-------|-------|--------|--------|-------------|
| 11 | **559 null Seasonal_Flags** | Products | Seasonal_Flag | Seasonal analysis incomplete | Create `"Unclassified"` category |
| 12 | **1,002 null Size_Dimensions** | Products | Size_Dimensions | Shipping analysis incomplete | Create `"Unknown"` category |
| 13 | **75,340 null Marketing_Campaign_IDs** | Sessions | Marketing_Campaign_ID | Campaign attribution incomplete | Create `"Organic/Unattributed"` category |
| 14 | **Purchase_Amount nulls** | Sessions | Purchase_Amount | Expected for non-converters | Replace null with 0 for aggregation safety |
| 15 | **Discount_Applied nulls** | Sessions | Discount_Applied | Promo analysis incomplete | Replace null with 0 |
| 16 | **Color_Variants and Size_Variants contain single value** | Products | Both columns | No analytical value | Consider removing from model |

### 4.3 Data Consistency Summary

**Cross-Table Referential Integrity:**

| Check | Result | Status |
|-------|--------|--------|
| Sessions.User_ID exists in Users.User_ID | 49,863/49,864 valid (99.998%) | Pass |
| Users.User_ID unique count | 49,999 (1 duplicate) | Minor issue |
| Sessions.Session_ID uniqueness | 300,000/300,001 (1 duplicate) | Minor issue |
| Products.Product_ID uniqueness | 3,000/3,001 (1 duplicate) | Minor issue |

---

## 5. Data Transformations Required

### 5.1 Power Query Transformation Steps

#### Users Table Transformations

```
1. REMOVE DUPLICATES on User_ID (keep first)
2. REMOVE 1 row where User_ID is null
3. CLEAN Email_Subscribed column:
   - Replace "  YES  " with "Yes"
   - Replace "invalid_email.com" with "No"  [or keep as "Invalid"]
   - Replace null with "Unknown"
4. CONVERT Signup_Date to Date type
   - Create flag: Is_Signup_Date_Missing = IF(Signup_Date IS NULL, 1, 0)
   - Fill Signup_Date with First_Purchase_Date where null
5. CONVERT First_Purchase_Date to Date type
   - Create flag: Has_Purchased = IF(First_Purchase_Date IS NULL, 0, 1)
6. CONVERT Total_Visits_Count to Integer
7. RECALCULATE CLV_Tier based on Lifetime_Value quartiles:
   - Premium:  > P75
   - High:     P50-P75
   - Medium:   P25-P50
   - Low:      <= P25
8. ADD Computed Column: User_Tenure_Days = TODAY() - Signup_Date
9. ADD Computed Column: Days_To_First_Purchase = First_Purchase_Date - Signup_Date
```

#### Products Table Transformations

```
1. REMOVE DUPLICATES on Product_ID (keep first)
2. REMOVE 1 row where Product_Category is null
3. CLEAN Base_Price: Remove "$" prefix, convert to Decimal
4. STANDARDIZE Product_Subcategory: Convert all to consistent casing
   - Use Text.Proper() or Text.Lower() consistently
5. FILL null Seasonal_Flag with "Unclassified"
6. FILL null Size_Dimensions with "Unknown"
7. ADD Computed Column: Profit_Per_Unit = Base_Price - Cost_Price
8. ADD Computed Column: Inventory_Status = 
   IF Inventory_Level = 0 THEN "Out of Stock"
   ELSE IF Inventory_Level <= Reorder_Threshold THEN "Low Stock"
   ELSE "In Stock"
9. ADD Computed Column: Margin_Bucket =
   IF Profit_Margin > 0.5 THEN "High (>50%)"
   ELSE IF Profit_Margin > 0.3 THEN "Medium (30-50%)"
   ELSE "Low (<30%)"
```

#### Sessions Table Transformations

```
1. REMOVE DUPLICATES on Session_ID (keep first)
2. REMOVE 1 row where User_ID is null (cannot join to Users)
3. CONVERT Timestamp to DateTime type
4. EXTRACT from Timestamp:
   - Date (for joining to Date dimension)
   - Year
   - Quarter
   - Month
   - MonthName
   - WeekOfYear
   - DayOfWeek
   - Hour
   - IsWeekend = IF DayOfWeek IN ("Saturday","Sunday") THEN 1 ELSE 0
5. REPLACE null Purchase_Amount with 0
6. REPLACE null Items_Purchased with 0
7. REPLACE null Discount_Applied with 0
8. FILL null Marketing_Campaign_ID with "Organic/Unattributed"
9. FILL null Payment_Method with "N/A"
10. DROP REDUNDANT columns: Customer_Segment, CLV_Tier
    (these will come from Users dimension via relationship)
11. DROP REDUNDANT column: Product_Category_Viewed 
    (use Primary_Product_Category instead)
12. ADD Computed Column: Revenue_After_Discount = Purchase_Amount - Discount_Applied
13. ADD Computed Column: Is_Converted = IF(Purchase_Flag = "Yes", 1, 0)
14. ADD Computed Column: Is_Bounced = IF(Bounce_Flag = "Yes", 1, 0)
15. ADD Computed Column: Is_Cart_Abandoned = IF(Cart_Abandonment_Flag = "Yes", 1, 0)
16. ADD Computed Column: Is_Checkout_Initiated = IF(Checkout_Initiated_Flag = "Yes", 1, 0)
17. ADD Computed Column: Has_Added_To_Cart = IF(Add_to_Cart_Count > 0, 1, 0)
18. ADD Computed Column: Is_New_Visitor = IF(Previous_Visits_Count = 0, 1, 0)
19. ADD Computed Column: Engagement_Score = 
    (Pages_Viewed / 24 * 0.4) + (Total_Scroll_Depth / 100 * 0.3) + 
    (Session_Duration / 3600 * 0.3)  [normalized 0-1]
```

### 5.2 Calculated Columns to Add in DAX (Power BI)

| Table | Calculated Column | DAX Formula | Purpose |
|-------|-------------------|-------------|---------|
| Users | `CLV_Tier_Corrected` | `SWITCH(TRUE(), Lifetime_Value >= PERCENTILE.INC(users[Lifetime_Value], 0.75), "Premium", Lifetime_Value >= PERCENTILE.INC(users[Lifetime_Value], 0.5), "High", Lifetime_Value >= PERCENTILE.INC(users[Lifetime_Value], 0.25), "Medium", "Low")` | Accurate CLV segmentation |
| Users | `User_Tenure_Days` | `DATEDIFF(Signup_Date, TODAY(), DAY)` | How long customer has been with brand |
| Users | `Days_To_First_Purchase` | `DATEDIFF(Signup_Date, First_Purchase_Date, DAY)` | Activation speed |
| Users | `Is_Active` | `IF(Days_Since_Last_Visit <= 30, "Active", IF(Days_Since_Last_Visit <= 90, "At Risk", "Inactive"))` | Recency-based activity status |
| Users | `Visits_Per_Order` | `DIVIDE(Total_Visits_Count, Lifetime_Orders, 0)` | Purchase efficiency |
| Sessions | `Hour_Bucket` | `SWITCH(TRUE(), HOUR(Timestamp) < 6, "Night", HOUR(Timestamp) < 12, "Morning", HOUR(Timestamp) < 18, "Afternoon", "Evening")` | Time-of-day analysis |
| Sessions | `Is_Weekend` | `IF(WEEKDAY(Timestamp, 2) >= 6, "Weekend", "Weekday")` | Weekend vs weekday |
| Sessions | `Funnel_Stage` | `SWITCH(TRUE(), Purchase_Flag = "Yes", "Purchase", Checkout_Initiated_Flag = "Yes", "Checkout", Add_to_Cart_Count > 0, "Add to Cart", Bounce_Flag = "No", "Engaged", "Bounce")` | Funnel stage per session |
| Sessions | `AOV_Bucket` | `SWITCH(TRUE(), Purchase_Amount >= 5000, "Premium ($5K+)", Purchase_Amount >= 2000, "High ($2K-$5K)", Purchase_Amount >= 500, "Medium ($500-$2K)", "Low (<$500)")` | Order size segmentation |

### 5.3 Measures to Create in DAX (Power BI)

**Core Conversion & Funnel Measures:**

```dax
// Conversion Rate
total_sessions = COUNTROWS(sessions)
converted_sessions = CALCULATE(COUNTROWS(sessions), sessions[Purchase_Flag] = "Yes")
conversion_rate = DIVIDE([converted_sessions], [total_sessions], 0)

// Bounce Rate
bounced_sessions = CALCULATE(COUNTROWS(sessions), sessions[Bounce_Flag] = "Yes")
bounce_rate = DIVIDE([bounced_sessions], [total_sessions], 0)

// Cart Abandonment Rate
cart_sessions = CALCULATE(COUNTROWS(sessions), sessions[Add_to_Cart_Count] > 0)
abandoned_cart_sessions = CALCULATE(COUNTROWS(sessions), sessions[Cart_Abandonment_Flag] = "Yes")
cart_abandonment_rate = DIVIDE([abandoned_cart_sessions], [cart_sessions], 0)

// Checkout Initiation Rate
checkout_sessions = CALCULATE(COUNTROWS(sessions), sessions[Checkout_Initiated_Flag] = "Yes")
checkout_initiation_rate = DIVIDE([checkout_sessions], [total_sessions], 0)

// Add to Cart Rate
add_to_cart_sessions = CALCULATE(COUNTROWS(sessions), sessions[Add_to_Cart_Count] > 0)
add_to_cart_rate = DIVIDE([add_to_cart_sessions], [total_sessions], 0)
```

**Revenue & Order Measures:**

```dax
// Total Revenue
total_revenue = SUM(sessions[Purchase_Amount])

// Average Order Value (AOV)
average_order_value = DIVIDE([total_revenue], [converted_sessions], 0)

// Total Orders (converted sessions)
total_orders = [converted_sessions]

// Revenue After Discount
total_discount = SUM(sessions[Discount_Applied])
revenue_after_discount = [total_revenue] - [total_discount]

// Discount Penetration
discount_sessions = CALCULATE(COUNTROWS(sessions), sessions[Discount_Applied] > 0)
discount_penetration = DIVIDE([discount_sessions], [converted_sessions], 0)
```

**Customer Measures:**

```dax
// Total Unique Users
total_unique_users = DISTINCTCOUNT(sessions[User_ID])

// Unique Buyers
unique_buyers = CALCULATE(DISTINCTCOUNT(sessions[User_ID]), sessions[Purchase_Flag] = "Yes")

// Buyer Rate
buyer_rate = DIVIDE([unique_buyers], [total_unique_users], 0)

// Revenue Per User (RPU)
revenue_per_user = DIVIDE([total_revenue], [total_unique_users], 0)

// Average Lifetime Value (from Users table)
avg_customer_ltv = AVERAGE(users[Lifetime_Value])

// Repeat Purchase Rate
repeat_users = CALCULATE(DISTINCTCOUNT(sessions[User_ID]), sessions[Previous_Visits_Count] > 0, sessions[Purchase_Flag] = "Yes")
repeat_purchase_rate = DIVIDE([repeat_users], [unique_buyers], 0)
```

**Session Quality Measures:**

```dax
// Average Session Duration
avg_session_duration = AVERAGE(sessions[Session_Duration])

// Average Pages Viewed
avg_pages_viewed = AVERAGE(sessions[Pages_Viewed])

// Average Scroll Depth
avg_scroll_depth = AVERAGE(sessions[Total_Scroll_Depth])

// Sessions per User
sessions_per_user = DIVIDE([total_sessions], [total_unique_users], 0)
```

**Period Comparison Measures:**

```dax
// Revenue Current Month
revenue_current_month = CALCULATE([total_revenue], DATESMTD(sessions[Date]))

// Revenue Previous Month
revenue_previous_month = CALCULATE([total_revenue], DATEADD(DATESMTD(sessions[Date]), -1, MONTH))

// Revenue MoM Growth %
revenue_mom_growth = DIVIDE([revenue_current_month] - [revenue_previous_month], [revenue_previous_month], 0)

// Conversion Rate Current Month
conversion_rate_current = CALCULATE([conversion_rate], DATESMTD(sessions[Date]))

// Conversion Rate Previous Month
conversion_rate_previous = CALCULATE([conversion_rate], DATEADD(DATESMTD(sessions[Date]), -1, MONTH))
```

---

## 6. Star Schema Data Model Design

### 6.1 Model Architecture

The recommended model is a **Star Schema** optimized for Power BI with one central fact table surrounded by dimension tables.

```
                    +-------------------+
                    |   Date Dimension  |
                    +---------+---------+
                              |
                              | 1:N
         +--------------------+--------------------+
         |                                         |
+--------+---------+                    +----------+--------+
|   User Dimension |                    | Product Dimension |
|     (users)      |                    |    (products)     |
+--------+---------+                    +----------+--------+
         | 1:N                              | 1:N
         |                                   |
         +------------------+----------------+
                            | 1:N
                   +--------+--------+
                   |  Session Fact   |
                   |   (sessions)    |
                   +-----------------+
```

### 6.2 Dimension Table: `Dim_User`

| Column | Data Type | Description |
|--------|-----------|-------------|
| User_ID | Text (PK) | Unique customer ID |
| Signup_Date | Date | Account creation date |
| Customer_Segment | Text | Lifecycle: New/Returning/Loyal/Inactive |
| CLV_Tier_Corrected | Text | Recalculated tier: Low/Medium/High/Premium |
| Preferred_Device | Text | Desktop/Mobile/Tablet |
| Primary_Location | Text | Geographic region |
| Acquisition_Source | Text | Marketing channel |
| Browser_Preference | Text | Preferred browser |
| A/B_Test_Assignment | Text | Test group |
| Email_Subscribed_Clean | Text | Yes/No/Unknown |
| First_Purchase_Date | Date | First transaction date |
| Total_Visits_Count | Integer | Historical visits |
| Avg_Session_Duration | Integer | Avg session length (sec) |
| Avg_Scroll_Depth | Decimal | Avg scroll % |
| Lifetime_Orders | Integer | Total orders |
| Lifetime_Value | Currency | Total revenue |
| User_Tenure_Days | Integer | Days since signup |
| Days_To_First_Purchase | Integer | Activation speed |
| Has_Purchased | Integer (0/1) | Ever purchased flag |

### 6.3 Dimension Table: `Dim_Product`

| Column | Data Type | Description |
|--------|-----------|-------------|
| Product_ID | Text (PK) | Unique product ID |
| Product_Category | Text | Top-level category |
| Product_Subcategory | Text | Sub-category (cleaned) |
| Base_Price | Currency | Cleaned retail price |
| Cost_Price | Currency | Manufacturing cost |
| Profit_Margin | Decimal | Margin % |
| Seasonal_Flag | Text | Season (no nulls) |
| Inventory_Level | Integer | Units in stock |
| Reorder_Threshold | Integer | Reorder trigger |
| Lead_Time | Integer | Days to restock |
| Product_Rating | Decimal | Customer rating |
| Review_Count | Integer | Number of reviews |
| Return_Rate | Decimal | Return % |
| Shipping_Weight | Decimal | Weight in kg |
| Size_Dimensions | Text | Dimensions or "Unknown" |
| Profit_Per_Unit | Currency | Base_Price - Cost_Price |
| Inventory_Status | Text | In Stock / Low Stock / Out of Stock |
| Margin_Bucket | Text | High/Medium/Low margin |

### 6.4 Dimension Table: `Dim_Date` (Auto-Generated)

Create using Power BI's `CALENDARAUTO()` or a custom date table:

| Column | Data Type | Description |
|--------|-----------|-------------|
| Date | Date (PK) | Calendar date |
| Year | Integer | 2023, 2024, 2025, 2026 |
| Quarter | Text | Q1, Q2, Q3, Q4 |
| Month | Integer | 1-12 |
| Month_Name | Text | January, February, ... |
| Month_Short | Text | Jan, Feb, ... |
| Week_Num | Integer | Week of year 1-52 |
| Day | Integer | Day of month |
| Day_Name | Text | Monday, Tuesday, ... |
| Day_Short | Text | Mon, Tue, ... |
| Day_Of_Year | Integer | 1-365 |
| Is_Weekend | Integer (0/1) | Saturday/Sunday flag |
| Is_Month_End | Integer (0/1) | Last day of month |
| Fiscal_Quarter | Text | FQ1, FQ2, FQ3, FQ4 |
| Year_Month | Text | 2023-01, 2023-02, ... |
| Full_Date_Label | Text | "Jan 2023", "Feb 2023", ... |

**DAX to create Date table:**
```dax
Dim_Date = 
ADDCOLUMNS(
    CALENDARAUTO(),
    "Year", YEAR([Date]),
    "Quarter", "Q" & QUARTER([Date]),
    "Month", MONTH([Date]),
    "Month_Name", FORMAT([Date], "MMMM"),
    "Month_Short", FORMAT([Date], "MMM"),
    "Week_Num", WEEKNUM([Date]),
    "Day", DAY([Date]),
    "Day_Name", FORMAT([Date], "dddd"),
    "Day_Short", FORMAT([Date], "ddd"),
    "Day_Of_Year", DATEDIFF(DATE(YEAR([Date]), 1, 1), [Date], DAY) + 1,
    "Is_Weekend", IF(WEEKDAY([Date], 2) >= 6, 1, 0),
    "Is_Month_End", IF(EOMONTH([Date], 0) = [Date], 1, 0),
    "Fiscal_Quarter", "FQ" & MOD(QUARTER([Date]) - 1, 4) + 1,
    "Year_Month", FORMAT([Date], "YYYY-MM"),
    "Full_Date_Label", FORMAT([Date], "MMM YYYY")
)
```

### 6.5 Fact Table: `Fact_Session`

| Column | Data Type | Description |
|--------|-----------|-------------|
| Session_ID | Text (PK) | Unique session ID |
| User_ID | Text (FK) | Reference to Dim_User |
| Date | Date (FK) | Reference to Dim_Date |
| Product_ID | Text (FK) | Reference to Dim_Product |
| Timestamp | DateTime | Exact session timestamp |
| Session_Duration | Integer | Seconds |
| Device_Type | Text | Desktop/Mobile/Tablet |
| Browser_Version | Text | Browser + version |
| Geographic_Location | Text | Session location |
| Traffic_Source | Text | Referral source |
| Marketing_Campaign_ID | Text | Campaign (no nulls) |
| Entry_Page | Text | Landing page |
| Exit_Page | Text | Exit page |
| Bounce_Flag | Text | YES/No |
| Pages_Viewed | Integer | Page count |
| Total_Scroll_Depth | Decimal | Scroll % |
| Primary_Product_Category | Text | Category browsed |
| Add_to_Cart_Count | Integer | Cart additions |
| Cart_Abandonment_Flag | Text | YES/No |
| Checkout_Initiated_Flag | Text | YES/No |
| Purchase_Flag | Text | YES/No |
| Purchase_Amount | Currency | Transaction value (0 for non-buyers) |
| Items_Purchased | Integer | Units bought (0 for non-buyers) |
| Discount_Applied | Currency | Discount amount (0 if none) |
| Payment_Method | Text | Payment type |
| Previous_Visits_Count | Integer | Visits before this session |
| Days_Since_Last_Visit | Integer | Recency |
| A/B_Test_Group | Text | Experiment group |
| Is_Converted | Integer (0/1) | 1 if purchased |
| Is_Bounced | Integer (0/1) | 1 if bounced |
| Is_Cart_Abandoned | Integer (0/1) | 1 if abandoned |
| Is_Checkout_Initiated | Integer (0/1) | 1 if checkout started |
| Has_Added_To_Cart | Integer (0/1) | 1 if cart used |
| Is_New_Visitor | Integer (0/1) | 1 if first visit |
| Revenue_After_Discount | Currency | Net revenue |
| Hour_Bucket | Text | Night/Morning/Afternoon/Evening |
| Funnel_Stage | Text | Bounce/Engaged/Add to Cart/Checkout/Purchase |

### 6.6 Relationship Model

| From Table | Column | To Table | Column | Cardinality | Cross-Filter |
|-----------|--------|----------|--------|-------------|-------------|
| Fact_Session | User_ID | Dim_User | User_ID | Many:1 | Single |
| Fact_Session | Date | Dim_Date | Date | Many:1 | Single |
| Fact_Session | Product_ID | Dim_Product | Product_ID | Many:1 | Single |

---

## 7. KPI Definitions & Solution Mapping

### 7.1 Must-Have KPIs (P0 - Business Critical)

#### KPI-1: Conversion Rate
**Definition:** Percentage of sessions that result in a purchase.
**Formula:** `Total Converted Sessions / Total Sessions * 100`
**Target:** > 18% (current: 17.95%)
**How to Build:** Create measure `conversion_rate` using DAX. Add as card visual and trend line.
**Tables Used:** `Fact_Session`
**Drill Dimensions:** Date, Device_Type, Customer_Segment, Traffic_Source, A/B_Test_Group

#### KPI-2: Cart Abandonment Rate
**Definition:** Percentage of sessions with cart additions that did not convert.
**Formula:** `Cart Abandoned Sessions / Sessions with Add-to-Cart * 100`
**Target:** < 45% (current: 49.94%)
**How to Build:** Create measure `cart_abandonment_rate`. Use funnel chart showing Add-to-Cart > Checkout > Purchase.
**Tables Used:** `Fact_Session`
**Drill Dimensions:** Date, Device_Type, Primary_Product_Category, Customer_Segment

#### KPI-3: Average Order Value (AOV)
**Definition:** Average revenue per converted session.
**Formula:** `Total Revenue / Total Converted Sessions`
**Target:** Maintain > $2,000 (current: $2,004.67)
**How to Build:** Create measure `average_order_value`. Use card visual with conditional formatting.
**Tables Used:** `Fact_Session`
**Drill Dimensions:** Date, Customer_Segment, Product_Category, Device_Type

#### KPI-4: Revenue Trend (Month-over-Month)
**Definition:** Revenue trajectory over time with growth rate.
**Formula:** `SUM(Purchase_Amount) by Month`
**Target:** Positive MoM growth
**How to Build:** Create measures `revenue_current_month`, `revenue_previous_month`, `revenue_mom_growth`. Use line chart with variance.
**Tables Used:** `Fact_Session` + `Dim_Date`
**Drill Dimensions:** Year, Quarter, Month, Week

#### KPI-5: Bounce Rate
**Definition:** Percentage of single-page sessions.
**Formula:** `Bounced Sessions / Total Sessions * 100`
**Target:** < 45% (current: 50.00%)
**How to Build:** Create measure `bounce_rate`. Use gauge visual.
**Tables Used:** `Fact_Session`
**Drill Dimensions:** Date, Entry_Page, Device_Type, Browser_Version, Geographic_Location

#### KPI-6: Customer Lifetime Value (CLV) by Segment
**Definition:** Average lifetime value per customer segment.
**Formula:** `AVG(Lifetime_Value) grouped by Customer_Segment`
**Target:** Identify high-value segments for targeting
**How to Build:** Create measure `avg_customer_ltv`. Use bar chart by segment.
**Tables Used:** `Dim_User`
**Drill Dimensions:** Customer_Segment, CLV_Tier_Corrected, Acquisition_Source

#### KPI-7: Sessions-to-Order Ratio
**Definition:** How many sessions it takes on average to generate one order.
**Formula:** `Total Sessions / Total Converted Sessions`
**Target:** Decrease over time (more efficient conversion)
**How to Build:** Create measure `sessions_per_order`. Use line chart over time.
**Tables Used:** `Fact_Session`
**Drill Dimensions:** Date, Customer_Segment, Device_Type

#### KPI-8: User Retention (Repeat Purchase Rate)
**Definition:** Percentage of buyers who are returning customers.
**Formula:** `Buyers with Previous_Visits > 0 / Total Buyers * 100`
**Target:** > 40%
**How to Build:** Create measure `repeat_purchase_rate`. Use card visual.
**Tables Used:** `Fact_Session`
**Drill Dimensions:** Date, Customer_Segment, Acquisition_Source

#### KPI-9: Top Products by Revenue
**Definition:** Which products and categories drive the most revenue.
**Formula:** `SUM(Purchase_Amount) by Product/Category`
**Target:** Diversify; reduce concentration risk
**How to Build:** Create measure `total_revenue`. Use bar chart or treemap.
**Tables Used:** `Fact_Session` + `Dim_Product`
**Drill Dimensions:** Product_Category, Product_Subcategory, Seasonal_Flag

#### KPI-10: Revenue by Traffic Source & Channel ROI
**Definition:** Revenue attribution by acquisition channel.
**Formula:** `SUM(Purchase_Amount) by Traffic_Source`
**Target:** Optimize spend toward highest-ROI channels
**How to Build:** Create measure `total_revenue`. Use stacked bar chart.
**Tables Used:** `Fact_Session`
**Drill Dimensions:** Traffic_Source, Marketing_Campaign_ID, Geographic_Location

### 7.2 Good-to-Have KPIs (P1 - Deep Insights)

#### KPI-11: Checkout Initiation Rate
**Definition:** Percentage of sessions that reach checkout.
**Formula:** `Checkout Sessions / Total Sessions * 100`
**Current:** 49.95%
**Tables Used:** `Fact_Session`

#### KPI-12: Time-to-Conversion (Session Duration for Buyers)
**Definition:** Average session length of converted sessions vs non-converted.
**Formula:** `AVG(Session_Duration) WHERE Purchase_Flag = "Yes"`
**Tables Used:** `Fact_Session`

#### KPI-13: New vs Returning Visitor Conversion
**Definition:** Compare conversion rates between first-time and repeat visitors.
**Formula:** `Conversion Rate WHERE Is_New_Visitor = 1` vs `Is_New_Visitor = 0`
**Tables Used:** `Fact_Session`

#### KPI-14: Discount Impact Analysis
**Definition:** How discounts affect conversion and AOV.
**Formula:** `AOV WHERE Discount > 0` vs `AOV WHERE Discount = 0`
**Tables Used:** `Fact_Session`

#### KPI-15: Product Return Risk Score
**Definition:** Identify products with high return rates.
**Formula:** `Return_Rate from Dim_Product`
**Tables Used:** `Dim_Product`

#### KPI-16: Inventory Health Dashboard
**Definition:** Monitor stock levels and reorder needs.
**Formula:** `Count of Products WHERE Inventory_Level <= Reorder_Threshold`
**Tables Used:** `Dim_Product`

#### KPI-17: Geographic Revenue Heatmap
**Definition:** Revenue concentration by geography.
**Formula:** `SUM(Purchase_Amount) by Geographic_Location`
**Tables Used:** `Fact_Session`

#### KPI-18: Hour-of-Day & Day-of-Week Patterns
**Definition:** When do users convert best?
**Formula:** `Conversion Rate by Hour_Bucket and Day_Name`
**Tables Used:** `Fact_Session` + `Dim_Date`

#### KPI-19: A/B Test Statistical Significance
**Definition:** Is the experiment showing meaningful differences?
**Formula:** `Conversion Rate by A/B_Test_Group with confidence intervals`
**Tables Used:** `Fact_Session`

#### KPI-20: Payment Method Preferences
**Definition:** Which payment types are most used?
**Formula:** `COUNT by Payment_Method`
**Tables Used:** `Fact_Session`

### 7.3 How Fact & Dimension Tables Solve Each KPI

| KPI | Primary Fact Table | Supporting Dimensions | Join Path |
|-----|-------------------|----------------------|-----------|
| Conversion Rate | Fact_Session | Dim_User, Dim_Date | Session > User (for segment), Session > Date (for trend) |
| Cart Abandonment | Fact_Session | Dim_Product, Dim_User | Session > Product (category drill), Session > User (segment) |
| AOV | Fact_Session | Dim_User, Dim_Date | Session > User (CLV tier), Session > Date (trend) |
| Revenue Trend | Fact_Session | Dim_Date | Session > Date (hierarchy) |
| Bounce Rate | Fact_Session | Dim_Date | Session > Date (trend) |
| CLV by Segment | Dim_User | -- | Direct aggregation |
| Sessions-to-Order | Fact_Session | Dim_Date, Dim_User | Session > Date + User |
| User Retention | Fact_Session | Dim_User | Session > User (true segment) |
| Top Products | Fact_Session | Dim_Product | Session > Product (name, category) |
| Channel ROI | Fact_Session | Dim_User | Session > User (acquisition source match) |
| Checkout Rate | Fact_Session | Dim_Date | Session > Date |
| Time-to-Conversion | Fact_Session | -- | Direct aggregation |
| New vs Returning | Fact_Session | -- | Direct on Previous_Visits_Count |
| Discount Impact | Fact_Session | -- | Direct on Discount_Applied |
| Return Risk | Dim_Product | -- | Direct aggregation |
| Inventory Health | Dim_Product | -- | Direct aggregation |
| Geographic Revenue | Fact_Session | -- | Direct on Geographic_Location |
| Hour/Day Patterns | Fact_Session | Dim_Date | Session > Date |
| A/B Test Results | Fact_Session | Dim_User | Session > User |
| Payment Methods | Fact_Session | -- | Direct on Payment_Method |

---

## 8. Dashboard Design Specification

### 8.1 Dashboard 1: Executive Summary (Must-Have)

**Layout:** Single-page overview with KPI cards and sparklines.

| Position | Visual Type | Content |
|----------|-------------|---------|
| Top Row (4 cards) | Card | Total Revenue, Conversion Rate, AOV, Bounce Rate |
| Top Row (2 cards) | Card | Total Sessions, Unique Buyers |
| Middle Left | Line Chart | Revenue Trend (Monthly) with YoY comparison |
| Middle Right | Funnel Chart | Sessions > Engaged > Add to Cart > Checkout > Purchase |
| Bottom Left | Bar Chart | Revenue by Traffic Source |
| Bottom Right | Bar Chart | Top 10 Product Categories by Revenue |

### 8.2 Dashboard 2: Conversion Deep-Dive (Must-Have)

**Layout:** Two-page detailed analysis.

**Page 1: Funnel Analysis**

| Position | Visual Type | Content |
|----------|-------------|---------|
| Top | Multi-row Card | Conversion Rate, Cart Abandonment Rate, Checkout Rate, Add-to-Cart Rate |
| Left | Funnel Chart | Full conversion funnel |
| Right Top | Bar Chart | Conversion Rate by Device Type |
| Right Bottom | Bar Chart | Conversion Rate by Customer Segment |
| Bottom | Line Chart | Conversion Rate trend (weekly) |

**Page 2: Drop-off Analysis**

| Position | Visual Type | Content |
|----------|-------------|---------|
| Top | Matrix Table | Funnel stage by category with conversion % |
| Middle | Bar Chart | Exit Page distribution for abandoned carts |
| Bottom | Bar Chart | Bounce Rate by Entry Page and Device |

### 8.3 Dashboard 3: Customer 360 (Must-Have)

**Layout:** Customer-centric view.

| Position | Visual Type | Content |
|----------|-------------|---------|
| Top Row | 4 Cards | Total Users, Avg CLV, Repeat Purchase Rate, Avg Orders/User |
| Left | Bar Chart | Customer Distribution by Segment |
| Right | Bar Chart | Avg Lifetime Value by Corrected CLV Tier |
| Bottom Left | Scatter Chart | Lifetime Orders vs Lifetime Value (colored by segment) |
| Bottom Right | Table | Top 20 Customers by Lifetime Value |

### 8.4 Dashboard 4: Revenue Intelligence (Must-Have)

**Layout:** Financial performance tracking.

| Position | Visual Type | Content |
|----------|-------------|---------|
| Top | Card | Total Revenue, MoM Growth, AOV, Total Discounts Given |
| Left | Area Chart | Revenue vs Revenue After Discount (monthly) |
| Right | Bar Chart | Revenue by Payment Method |
| Bottom Left | Line Chart | AOV trend over time |
| Bottom Right | Treemap | Revenue by Geographic Location |

### 8.5 Dashboard 5: Product Performance (Good-to-Have)

| Position | Visual Type | Content |
|----------|-------------|---------|
| Top | Cards | Total Products, Avg Margin, Products in Low Stock, Avg Rating |
| Left | Bar Chart | Revenue by Product Category |
| Right | Scatter | Margin vs Return Rate by Product |
| Bottom | Table | Product inventory alert list (low stock items) |

### 8.6 Dashboard 6: Experimentation (Good-to-Have)

| Position | Visual Type | Content |
|----------|-------------|---------|
| Top | Cards | Control Conv. Rate, Variant A Conv. Rate, Variant B Conv. Rate, Winner |
| Left | Bar Chart | Conversion Rate by A/B Test Group |
| Right | Bar Chart | AOV by A/B Test Group |
| Bottom | Line Chart | Daily conversion rate by test group over time |

### 8.7 Dashboard 7: Behavioral Insights (Good-to-Have)

| Position | Visual Type | Content |
|----------|-------------|---------|
| Top | Cards | Avg Session Duration, Avg Pages Viewed, Avg Scroll Depth, Sessions/User |
| Left | Heatmap | Conversion Rate by Hour of Day and Day of Week |
| Right | Bar Chart | Sessions by Device Type and Browser |
| Bottom | Line Chart | Bounce Rate trend by month |

---

## 9. Data Transformation Summary Table

| # | Transformation | Priority | Tables Affected | Power BI Tool |
|---|---------------|----------|----------------|---------------|
| 1 | Remove duplicate User_ID | Critical | Users | Power Query > Remove Duplicates |
| 2 | Remove duplicate Product_ID | Critical | Products | Power Query > Remove Duplicates |
| 3 | Remove duplicate Session_ID | Critical | Sessions | Power Query > Remove Duplicates |
| 4 | Remove null User_ID row | Critical | Users, Sessions | Power Query > Filter |
| 5 | Clean $ prefix from Base_Price | Critical | Products | Power Query > Replace Values + Change Type |
| 6 | Normalize Email_Subscribed values | Critical | Users | Power Query > Replace Values |
| 7 | Recalculate CLV_Tier from Lifetime_Value | Critical | Users | DAX Calculated Column |
| 8 | Recalculate Customer_Segment join | Critical | Sessions | Use RELATED() to Users table |
| 9 | Standardize Product_Subcategory casing | High | Products | Power Query > Transform > Format |
| 10 | Fill null Seasonal_Flag | High | Products | Power Query > Replace Nulls |
| 11 | Fill null Size_Dimensions | High | Products | Power Query > Replace Nulls |
| 12 | Fill null Marketing_Campaign_ID | High | Sessions | Power Query > Replace Nulls |
| 13 | Fill null Purchase_Amount with 0 | High | Sessions | Power Query > Replace Nulls |
| 14 | Fill null Discount_Applied with 0 | High | Sessions | Power Query > Replace Nulls |
| 15 | Fill Signup_Date from First_Purchase_Date | Medium | Users | Power Query > Fill Down/Conditional |
| 16 | Extract Date components from Timestamp | Medium | Sessions | Power Query > Add Column > Date |
| 17 | Create Dim_Date table | Medium | -- | DAX CALENDARAUTO() |
| 18 | Create boolean flag columns | Medium | Sessions | Power Query > Add Conditional Column |
| 19 | Create computed columns (Profit_Per_Unit, etc.) | Medium | Products, Users, Sessions | Power Query or DAX |
| 20 | Create all DAX measures | High | -- | DAX |

---

## 10. Implementation Checklist

### Phase 1: Data Preparation (30 minutes)
Follow `PowerBI_Build_Guide.md` Step 1-2:
- [ ] Import 4 cleaned CSV files (Dim_User, Dim_Product, Dim_Date, Fact_Session)
- [ ] Verify data types match specification (Section 5.1)
- [ ] Create relationships in Model View (Section 6.6)
- [ ] Hide foreign key columns from report view

### Phase 2: DAX Development (30 minutes)
Follow `PowerBI_Build_Guide.md` Step 4:
- [ ] Copy-paste measures from `DAX_Measures.txt` into Fact_Session table
- [ ] Create calculated columns (Section 5.2)
- [ ] Test measures with card visuals

### Phase 3: Dashboard Build (2-3 hours)
Follow `PowerBI_Build_Guide.md` Step 5:
- [ ] Build Executive Summary dashboard (Must-Have)
- [ ] Build Conversion Deep-Dive dashboard (Must-Have)
- [ ] Build Customer 360 dashboard (Must-Have)
- [ ] Build Revenue Intelligence dashboard (Must-Have)
- [ ] Build Product Performance dashboard (Good-to-Have)
- [ ] Build Experimentation dashboard (Good-to-Have)
- [ ] Build Behavioral Insights dashboard (Good-to-Have)

### Phase 4: Formatting & Polish (30 minutes)
- [ ] Apply conditional formatting (red/yellow/green thresholds)
- [ ] Set cross-filtering interactions
- [ ] Add slicers (Date range, Device, Segment, etc.)
- [ ] Test drill-through functionality

### Phase 5: Validation & Delivery (30 minutes)
- [ ] Cross-validate KPIs with source data
- [ ] Optimize performance (remove unnecessary columns)
- [ ] Save as .pbix and publish to Power BI Service

---

## 11. Key Findings & Recommendations

### 11.1 Immediate Actions (Week 1)

1. **Fix CLV Tier Assignment** -- The current CLV_Tier shows zero correlation with actual Lifetime_Value (all tiers have nearly identical means ~$12,500). Recalculate using quartiles as specified.

2. **Address Cart Abandonment** -- 49.94% cart abandonment means $1 in every $2 of potential revenue is lost at checkout. Implement exit-intent popups and abandoned cart email sequences.

3. **Reduce Bounce Rate** -- 50% bounce rate indicates landing page misalignment. A/B test homepage vs product page as entry points by traffic source.

4. **Device Optimization** -- Revenue is evenly split across Desktop/Mobile/Tablet, but check if Mobile conversion rate lags behind Desktop (investigate in Power BI).

### 11.2 Strategic Initiatives (Month 1-3)

1. **Segment-Specific Journeys** -- New, Returning, Loyal, and Inactive segments each need tailored landing experiences. Use the Customer 360 dashboard to design these.

2. **Channel ROI Optimization** -- All traffic sources contribute nearly equal revenue (~20% each). Investigate cost-per-acquisition to determine true ROI by channel.

3. **Inventory Risk Management** -- 188 products are at or below reorder threshold. Build automated alerts using the Product Performance dashboard.

4. **Personalization Engine** -- Use A/B test results and behavioral data to serve personalized product recommendations based on Customer_Segment and browsing history.

---

## 12. Appendix: Reference Data

### A. Customer Segment Definitions

| Segment | Definition | Count | % of Base |
|---------|-----------|-------|-----------|
| New | First-time or early-stage customers | 17,480 | 35.0% |
| Returning | Repeat visitors, moderate engagement | 17,402 | 34.8% |
| Loyal | High engagement, frequent purchasers | 10,135 | 20.3% |
| Inactive | No recent activity, at-risk of churn | 4,984 | 10.0% |

### B. Product Category Distribution

| Category | Products | % of Catalog |
|----------|---------|-------------|
| Sports | 615 | 20.5% |
| Electronics | 611 | 20.4% |
| Home & Living | 603 | 20.1% |
| Beauty | 599 | 20.0% |
| Fashion | 572 | 19.1% |

### C. Geographic Distribution (Sessions)

| Region | Sessions | % of Traffic |
|--------|---------|-------------|
| Asia, India | 50,171 | 16.7% |
| Europe, Germany | 50,158 | 16.7% |
| Europe, UK | 50,021 | 16.7% |
| Asia, Singapore | 49,940 | 16.6% |
| North America, USA | 49,865 | 16.6% |
| Middle East, UAE | 49,846 | 16.6% |

---

*All data transformations, DAX formulas, and model specifications are ready for direct use in Power BI Desktop. See `PowerBI_Build_Guide.md` for the step-by-step import instructions and `DAX_Measures.txt` for copy-paste ready formulas.*
