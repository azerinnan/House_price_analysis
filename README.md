# House Price Analysis

## Project Overview
This project focuses on analyzing residential house price trends across states in Peninsular Malaysia, using real-world data to simulate business-style reporting and geographical property insights. The purpose is to gain hands-on experience in data cleaning, SQL querying and building interactive Power BI dashboards for data storytelling.

## Objectives
- Identify the most and least expensive state in Peninsular Malaysia.
- Compare average property prices by state and house type.
- Determine overprice and under price of township and type of house.
- Identify popular type of house based on transaction.

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
|Data Cleaning  | Microsoft Excel         | Cleaned raw dataset by removing duplicates, handling missing values and formatting columns. |
|Data Analysis  | SQL             | Queried cleaned dataset to perform aggregations and comparisons. |
|Data Visualization | Power BI                | Created interactive dashboards to display key trends and comparisons.          |

## Process Steps

**1. Data Cleaning (Microsoft Excel)**

The original data was downloaded from Kaggle and needed adjustments before starting the analysis.
![IMAGE 1: HOUSE PRICE RAW DATA](1_houseprice_data_raw.png)

- Start with capital letters for Township column
- Add ID as unique identifier for each row
- Added separators (,) to Median_Price and Median_PSF columns
- Standardized rows of house type such as 'Bungalow, Semi D' and 'Semi D, Bungalow', used IF statement, VLOOKUP, XLOOKUP and TEXTJOIN. 

    *Before:*
    
    <img src="2_house_type.png" width="40%"/> 

    *After:*

    <img src="3_house_type_cleaned.png" width="40%"/>

