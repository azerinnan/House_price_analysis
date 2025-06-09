# House Price Analysis

## Project Overview
This project focuses on analyzing property market across states in Malaysia, using real-world data to simulate business-style reporting and geographical property insights. The purpose is to gain hands-on experience in data cleaning, SQL querying and building interactive Power BI dashboards for data storytelling.

## Objectives
- Compare the average median property price by state in Malaysia

- Identify overpriced and underpriced properties by state

- Determine the most popular types of houses based on transaction volume

- Analyze the percentage distribution of property tenure types

## Data Source
- Dataset Name: House Prices in Malaysia (2025)
- Source: Kaggle
- Data Format: CSV
- Download Link: [*House Prices in Malaysia 2025 (Kaggle)*](https://www.kaggle.com/datasets/lyhatt/house-prices-in-malaysia-2025/data)

    | **Variable**     | **Description**                                                                 |
    |------------------|----------------------------------------------------------------------------------|
    | `Township`       | Name of the specific township where the house is located. |
    | `Area`           |  The district grouping multiple townships within a state. |
    | `State`          | The state in Malaysia where the house is located. |
    | `Tenure`         | Type of ownership (Freehold or Leasehold).                       |
    | `Type`           | Classification of the house.      |
    | `Median_Price`   | Median property price within the specified location.       |
    | `Median_PSF`     | The median price per square foot for the house.         |
    | `Transactions`   | Total number of recorded property transactions in the specified area or township.|

## Tools & Workflow
| **Step**                  |**Tool Used**        | **Description**                                                                 |
|-----------------------------|----------------------------------------|---------------------------------------------------------------------------------|
|Data Cleaning  | Microsoft Excel         | Cleaned raw dataset by proper and standardized the data and formatting columns. |
|Data Analysis  | SQL             | Queried cleaned dataset to perform aggregations and comparisons. |
|Data Visualization | Power BI                | Created interactive dashboards to display key insights and comparisons.          |


 

The original data was downloaded from Kaggle and required multiple cleaning steps before meaningful analysis could be performed.
![IMAGE 1: HOUSE PRICE RAW DATA](1_houseprice_data_raw.png)

- Converted values in the Township column to title case for consistent formating.
- Added a unique ID column to serve as a row identifier
- Formatted Median_Price and Median_PSF columns with comma separators for better readability
- Renamed all column headers to lowercase for consistency
- Standardized inconsistent entries in the Type column (e.g., “Bungalow, Semi D” vs. “Semi D, Bungalow”) using formulas such as IF, VLOOKUP, XLOOKUP, and TEXTJOIN 

    *Before Cleaning:*
    
    <img src="2_house_type.png" width="40%"/> 

    *After Cleaning:*

    <img src="3_house_type_cleaned.png" width="40%"/>


The final cleaned dataset is displayed below and can be accessed here: [*House Price Data Cleaned*](https://github.com/azerinnan/draft_house_price/blob/main/house_price_data_cleaned.xlsx)
![IMAGE 4: HOUSE PRICE DATA CLEANED](4_houseprice_data_cleaned.png)

## Data Analysis (SQL)

### `OBJECTIVE 1` : Compare the average median house price by state in Malaysia

```sql_
SELECT
    state,
    ROUND(AVG(median_price),2) AS avg_median_price
FROM houseprice
GROUP BY state
ORDER BY avg_median_price DESC;
```
Insights:

- Labuan shows the highest average median house price at RM 1.08 million, but this is likely skewed due to having the only one township in the property market.
    
- Top 3 states with relatively high average median prices (excluding Labuan):
    -   Kelantan - RM 0.76 million
    -   Perlis - RM 0.62 million
    -   Sarawak (RM 0.53 million)

- Most affordable states based on average median price are:
    -   Terengganu - RM 0.33 million 
    -   Putrajaya - RM 0.40 million

---
### `OBJECTIVE 2` : Identify states with the highest (overpriced) and lowest (underpriced) median house prices.

```sql
-- Determine overpriced states (highest median price per state)
WITH overprice AS (
	SELECT state, MAX(median_price) AS max_price
	FROM houseprice
	GROUP BY state
),

-- Determine underpriced states (lowest median price per state)
underprice AS (
	SELECT state, MIN(median_price) AS min_price
	FROM houseprice
	GROUP BY state;
)

SELECT o.state, o.max_price, u.min_price
FROM overprice AS o
FULL JOIN underprice AS u
ON o.state = u.state
ORDER BY max_price DESC
```
Insights:
- Overpriced States (Highest Median Prices)
	- Kuala Lumpur - RM 5.46 million 
	- Johor - RM 4.05 million
	- Kedah - RM 3.71 million.
	
	High demand for premium properties is Kuala Lumpur which the central location of Malaysia's economic and commercial hub. Johor's location near Singapore and ongoing development projects in Kedah may also contribute to the rising house prices in these states.
- The lowest minimum median price recorded by Selangor at RM 27,049 followed by Sarawak at RM 35,000. This could indicate the availability of low-cost housing unit or auctioned properties within these states. It could also reflect lower demand or less developed in certain areas.

---
### `OBJECTIVE 3` : Identify the most popular type of house based on transactions

```sql
WITH ranktype AS (
		SELECT 
			type,
			township,
			area,
			state,
			transactions,
			ROW_NUMBER() OVER (PARTITION BY type
            ORDER BY transactions DESC) AS highest_transaction
		FROM houseprice
		)
	
SELECT 
	type,
	township,
	area,
	state,
	transactions
FROM ranktype 
WHERE highest_transaction = 1
ORDER BY transactions DESC, type ASC;
```
Insights: 

- The **Bungalow** is the most popular type of house in the property market, with **593 transactions** recorded in Bandar Tasik Senangin, Lenggeng, Negeri Sembilan.
- It is followed by **Bungalow & Terrace House** with **363 transactions**, located in Bukit Sentosa, Serendah, Selangor.
- These areas are located outside major urban centers, offering a quieter and more spacious living environment. This may appeal to buyers looking for peaceful surroundings, larger land size, or more affordable detached housing options compared to properties in city centers.

---
### `OBJECTIVE 4` : Percentage of tenure
```SQL
SELECT
	tenure, 
	COUNT(*) AS count_per_tenure,
	COUNT(*)*100.00/SUM(COUNT(*)) OVER () AS tenure_percentage
FROM houseprice
WHERE tenure IN ('Freehold','Leasehold','Freehold, Leasehold')
GROUP BY tenure
ORDER BY tenure_percentage DESC;
```

Insights: 
- The majority of properties (65.90%) are categorized as Freehold, making it the most common tenure type
- Freehold properties grant buyers owns both the property and the land indefinitely. This tenure type is preferred by buyers seeking long-term investment stability and control over property without concerns of lease expiration.
---
## Data Visualization (Power BI)
After completing data cleaning and analysis, used Power BI to tranform the processed data into an interactive dashboard. It highlights median price comparisons by state, tenure types, housing type distributions and transactions.

### Dashboard Preview

![House Price Dashboard](5_houseprice_dashboard.png)
The House Price 20205 Analysis dashboard can be accessed here: [*House Price 2025 Analysis Dashboard*](https://github.com/azerinnan/draft_house_price/blob/main/6_houseprice_analysis_dashboard.pbix)


### Key Visuals and Features
- **Slicer - State in Malaysia**

	Filter all visual by state to compare house prices and transactions volumes.

- **Cards - Summary statistics**
	
	Displays average, minimum and maximum median house price based on selected states.

- **Filled map - State in Malaysia**
	
	Visualizes property market transactions by state across Malaysia.

- **Table – Popular house types by city and state**

	Shows the most common house type in selected cities, along with the state and number of transactions. Only cities with a clear top house type are included. Some states may not appear if no house type is clearly more popular than others.

- **Pie chart - Tenure type**

	Shows the percentage distribution of tenure types in Malaysia.

### Insights Highlighted
- Freehold is the dominant tenure type with 65.9% of the dataset.
- The average median house price in Malaysia is RM 540.85 K.
- Bungalows in Negeri Sembilan are the most transacted house type, with 593 transactions recorded.
- Kuala Lumpur and Johor have the highest median house price in Malaysian property market.