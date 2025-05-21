# House Price Analysis

## Project Overview
This project focuses on analyzing property market across states in Malaysia, using real-world data to simulate business-style reporting and geographical property insights. The purpose is to gain hands-on experience in data cleaning, SQL querying and building interactive Power BI dashboards for data storytelling.

## Objectives
- Compare the average median price by state in Malaysia.
- Over price and under price of house by states.
- Popular type of house based on buying transactions.
- Percentage of tenure of property.

## Data Source
- Dataset Name: House Prices in Malaysia (2025)
- Source: Kaggle
- Data Format: CSV
- Download Link: [house price in malaysia 2025](https://www.kaggle.com/datasets/lyhatt/house-prices-in-malaysia-2025/data)

    | **Variable**     | **Description**                                                                 |
    |------------------|----------------------------------------------------------------------------------|
    | `Township`       | Name of the specific township where the house is located. |
    | `Area`           |  The district grouping multiple townships within a state. |
    | `State`          | The state in Malaysia where the house is located. |
    | `Tenure`         | Type of ownership (Freehold or Leasehold).                       |
    | `Type`           | Classification of the house.      |
    | `Median_Price`   | The median price of house within the location.       |
    | `Median_PSF`     | The median price per square foot for the house.         |
    | `Transactions`   | Total number of recorded property transactions in the specified area or township.|

## Tools & Workflow
| **Step**                  |**Tool Used**        | **Description**                                                                 |
|-----------------------------|----------------------------------------|---------------------------------------------------------------------------------|
|Data Cleaning  | Microsoft Excel         | Cleaned raw dataset by proper and standardized the data and formatting columns. |
|Data Analysis  | SQL             | Queried cleaned dataset to perform aggregations and comparisons. |
|Data Visualization | Power BI                | Created interactive dashboards to display key trends and comparisons.          |


## Data Cleaning (Microsoft Excel)

The original data was downloaded from Kaggle and needed adjustments before starting the analysis.
![IMAGE 1: HOUSE PRICE RAW DATA](1_houseprice_data_raw.png)

- Changed Township column to title case on each word
- Added ID as unique identifier for each row
- Added separators (,) to Median_Price and Median_PSF columns
- Changed the column name to small letter
- Standardized rows of house type such as 'Bungalow, Semi D' and 'Semi D, Bungalow'. Used IF statement, VLOOKUP, XLOOKUP and TEXTJOIN. 

    *Before:*
    
    <img src="2_house_type.png" width="40%"/> 

    *After:*

    <img src="3_house_type_cleaned.png" width="40%"/>


Cleaned data as per shown below and as per link : [HOUSE PRICE DATA CLEANED](https://github.com/azerinnan/draft_house_price/blob/main/house_price_data_cleaned.xlsx)
![IMAGE 4: HOUSE PRICE DATA CLEANED](4_houseprice_data_cleaned.png)

## Data Analysis (SQL)

### `OBJECTIVE 1` : Compare the average median price by state in Malaysia

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
    
- Top 3 states with relatively high average median prices (excluding Labuan) are :
    -   Kelantan (RM 0.76 million)
    -   Perlis (RM 0.62 million)
    -   Sarawak (RM 0.53 million)

- Most affordable states with low average median price are:
    -   Terengganu (RM 0.33 million)
    -   Putrajaya (RM 0.40 million)

---
### `OBJECTIVE 2` : Over price and under price of house by states

```sql
-- determine over price states
WITH overprice AS (
	SELECT state, MAX(median_price) AS max_price
	FROM houseprice
	GROUP BY state
),

-- determine under price states
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

- Kuala Lumpur holds the highest median price at RM 5.46 million, surpassing Johor and Kedah at RM 4.05 million and RM 3.71 million. High demand for premium properties is Kuala Lumpur which the central location of Malaysia's economic and commercial hub.
- The lowest minimum median price recorded by Selangor at RM 27,049 followed by Sarawak at RM 35,000. This could indicate the availability of low-cost housing unit or auctioned properties. 

---
### `OBJECTIVE 3` : Popular type of house based on transaction

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

- The 'Bungalow' is the most popular type of house in the property market, with 593 transactions recorded in Bandar Tasik Senangin, Lenggeng, Negeri Sembilan 
and followed by 'Bungalow & Terrace House' with 363 transactions, located in Bukit Sentosa, Serendah, Selangor.
- These locations, situated away from the busy city, offers a peaceful environment that appeals to buyers.

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

- With 65.90 % freehold tenure is the highest tenure type. Buyers seeking long-term investment and ownership security, as the buyer owns both the property and the land indefinitely.
---


