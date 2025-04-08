# Carbon_Emission-_Analysis

## 1. Introduction
This report aims to analyze carbon emissions to examine the carbon footprint across various industries. We aim to identify sectors with the highest levels of emissions by analyzing them across countries and years, as well as to uncover trends.
![image](https://github.com/user-attachments/assets/1fa339f2-1b38-4b3d-9b2b-6e240fc2bc37)

### 1.1 Data model
The dataset consists of 4 tables containing information regarding carbon emissions generated during the production of goods.
![image](https://github.com/user-attachments/assets/c1d88050-685a-4f1e-a07a-a1f4b79f4b59)

### 1.2 Data structure
Table 'product_emissions'
```SQL
SELECT * FROM product_emissions LIMIT 10;
```
|id|company_id|country_id|industry_group_id|year|product_name|weight_kg|carbon_footprint_pcf|upstream_percent_total_pcf|operations_percent_total_pcf|downstream_percent_total_pcf|
|--|----------|----------|-----------------|----|------------|---------|--------------------|--------------------------|----------------------------|----------------------------|
|10056-1-2014|82|28|2|2014|Frosted Flakes(R) Cereal|0.7485|2|57.50|30.00|12.50|
|10056-1-2015|82|28|15|2015|"Frosted Flakes, 23 oz, produced in Lancaster, PA (one carton)"|0.7485|2|57.50|30.00|12.50|
|10222-1-2013|83|28|8|2013|Office Chair|20.68|73|80.63|17.36|2.01|
|10261-1-2017|14|16|25|2017|Multifunction Printers|110.0|1488|30.65|5.51|63.84|
|10261-2-2017|14|16|25|2017|Multifunction Printers|110.0|1818|25.08|4.51|70.41|
|10261-3-2017|14|16|25|2017|Multifunction Printers|110.0|2274|20.05|3.61|76.34|
|10324-1-2016|15|16|19|2016|KURALON  fiber|1500.0|10000|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|
|10418-1-2013|84|9|19|2013|Portland Cement|1000.0|1102|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|
|10661-10-2014|85|28|11|2014|Regular Straight 505® Jeans – Steel (Water<Less™)|0.7665|15|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|
|10661-10-2015|85|28|6|2015|Regular Straight 505® Jeans – Steel (Water<Less™)|0.7665|15|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|N/a (product with insufficient stage-level data)|
Table 'industry_groups'


### 1.2 Cleaning data
1. Which products contribute the most to carbon emissions?
```SQL
SELECT product_name, 
		ROUND(AVG(carbon_footprint_pcf),2) AS Average_PCF
FROM product_emissions pe 
GROUP BY product_name
ORDER BY carbon_footprint_pcf DESC
LIMIT 10;
```
|product_name|Average_PCF|
|------------|-----------|
|Wind Turbine G128 5 Megawats|3718044.00|
|Wind Turbine G132 5 Megawats|3276187.00|
|Wind Turbine G114 2 Megawats|1532608.00|
|Wind Turbine G90 2 Megawats|1251625.00|
|Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.|191687.00|
|Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall|167000.00|
|TCDE|99075.00|
|Mercedes-Benz GLE (GLE 500 4MATIC)|91000.00|
|Mercedes-Benz S-Class (S 500)|85000.00|
|Mercedes-Benz SL (SL 350)|72000.00|

-	Wind turbines have the highest PCFs
-	Construction Materials: Steel-intensive construction has a considerable carbon footprint.
-	Even within the automotive category, these Mercedes-Benz models have relatively high PCFs.
2. What are the industry groups of these products?
```SQL

SELECT	pe.product_name,
		ig.industry_group,
		ROUND(AVG(pe.carbon_footprint_pcf),2) AS Average_PCF
FROM product_emissions pe
JOIN industry_groups ig ON ig.id = pe.industry_group_id
GROUP BY pe.product_name
ORDER BY pe.carbon_footprint_pcf DESC
LIMIT 10;
```
|product_name|industry_group|Average_PCF|
|------------|--------------|-----------|
|Wind Turbine G128 5 Megawats|Electrical Equipment and Machinery|3718044.00|
|Wind Turbine G132 5 Megawats|Electrical Equipment and Machinery|3276187.00|
|Wind Turbine G114 2 Megawats|Electrical Equipment and Machinery|1532608.00|
|Wind Turbine G90 2 Megawats|Electrical Equipment and Machinery|1251625.00|
|Land Cruiser Prado. FJ Cruiser. Dyna trucks. Toyoace.IMV def unit.|Automobiles & Components|191687.00|
|Retaining wall structure with a main wall (sheet pile): 136 tonnes of steel sheet piles and 4 tonnes of tierods per 100 meter wall|Materials|167000.00|
|TCDE|Materials|99075.00|
|Mercedes-Benz GLE (GLE 500 4MATIC)|Automobiles & Components|91000.00|
|Mercedes-Benz S-Class (S 500)|Automobiles & Components|85000.00|
|Mercedes-Benz SL (SL 350)|Automobiles & Components|72000.00|
- The wind turbines, all categorized under "Electrical Equipment and Machinery," have the highest average PCFs.
- TAutomobiles & Components are a significant contributor to carbon emissions.   
The data highlights the need for automakers to continue developing and implementing strategies to reduce their carbon footprint.
- "Materials" includes the retaining wall structure and TCDE. These products have moderate PCFs compared to the wind turbines but are still significant.
3. What are the industries with the highest contribution to carbon emissions?
```SQL
SELECT  ig.industry_group,
		ROUND(SUM(pe.carbon_footprint_pcf),2) AS total_PCF
FROM product_emissions pe
JOIN industry_groups ig ON pe.industry_group_id = ig.id
GROUP BY ig.industry_group
ORDER BY total_PCF DESC
LIMIT 10;
```
|industry_group|total_PCF|
|--------------|---------|
|Electrical Equipment and Machinery|9801558.00|
|Automobiles & Components|2582264.00|
|Materials|577595.00|
|Technology Hardware & Equipment|363776.00|
|Capital Goods|258712.00|
|"Food, Beverage & Tobacco"|111131.00|
|"Pharmaceuticals, Biotechnology & Life Sciences"|72486.00|
|Chemicals|62369.00|
|Software & Services|46544.00|
|Media|23017.00|

- Electrical Equipment and Machinery: This industry has the overwhelmingly largest total PCF, making it the most significant contributor in this dataset.
- Automobiles & Components: This industry is the second-highest contributor, though its total PCF is considerably lower than that of Electrical Equipment and Machinery.
- Materials: This industry ranks third in total PCF.
4. What are the companies with the highest contribution to carbon emissions?
```SQL
SELECT  companies.company_name,
		ROUND(SUM(pe.carbon_footprint_pcf),2) AS total_PCF
FROM product_emissions pe
JOIN companies ON pe.company_id = companies.id
GROUP BY companies.company_name
ORDER BY total_PCF DESC
LIMIT 10;
```
|company_name|total_PCF|
|------------|---------|
|"Gamesa Corporación Tecnológica, S.A."|9778464.00|
|Daimler AG|1594300.00|
|Volkswagen AG|655960.00|
|"Mitsubishi Gas Chemical Company, Inc."|212016.00|
|"Hino Motors, Ltd."|191687.00|
|Arcelor Mittal|167007.00|
|Weg S/A|160655.00|
|General Motors Company|137007.00|
|"Lexmark International, Inc."|132012.00|
|"Daikin Industries, Ltd."|105600.00|
- Gamesa Corporación Tecnológica, S.A.: This company has the highest contribution by a very large margin (9,778,464.00).
- Daimler AG: This automotive manufacturer has the second-highest contribution (1,594,300.00).
- Volkswagen AG: Another major automotive company, with the third-highest contribution (655,960.00).
It's clear that Gamesa stands out as the company with the most significant carbon footprint in this list.
5. What are the countries with the highest contribution to carbon emissions?
```SQL
SELECT  c.country_name,
		ROUND(SUM(pe.carbon_footprint_pcf),2) AS total_PCF
FROM product_emissions pe
JOIN countries c ON pe.country_id = c.id
GROUP BY c.country_name
ORDER BY total_PCF DESC
LIMIT 10;
```
|country_name|total_PCF|
|------------|---------|
|Spain|9786130.00|
|Germany|2251225.00|
|Japan|653237.00|
|USA|518381.00|
|South Korea|186965.00|
|Brazil|169337.00|
|Luxembourg|167007.00|
|Netherlands|70417.00|
|Taiwan|62875.00|
|India|24574.00|
