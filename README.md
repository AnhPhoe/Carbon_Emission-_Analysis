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
- Spain has the highest total PCF bya large portion of the high emissions products are produced within Spain, which is consistent with the high PCF from Gamesa Corporación Tecnológica, S.A.
- European Countries:
Germany and Luxembourg are also among the countries with high total PCFs.
The Netherlands is also within the listed countries.
- India has the lowest total PCF of the countries listed.
6. What is the trend of carbon footprints (PCFs) over the years?
```SQL
SELECT
    year,
    ROUND(SUM(carbon_footprint_pcf),2) AS TOTAL_PCF
FROM
    product_emissions
GROUP BY
    year
ORDER BY
     year DESC;
```
|year|TOTAL_PCF|
|----|---------|
|2017|340271.00|
|2016|1640182.00|
|2015|10840415.00|
|2014|624226.00|
|2013|503857.00|
![image](https://github.com/user-attachments/assets/85c5fa56-2459-493c-9661-8582f3fa9947)
- Trend: The graph shows a dramatic spike in carbon footprint in 2015, with relatively low levels in the surrounding years.
7. Which industry groups has demonstrated the most notable decrease in carbon footprints (PCFs) over time?
```SQL
SELECT
    ig.industry_group AS Industry_Group,
    ROUND(SUM(CASE WHEN pe.year = 2013 THEN pe.carbon_footprint_pcf ELSE 0 END), 2) AS `2013_Emission`,
    ROUND(SUM(CASE WHEN pe.year = 2014 THEN pe.carbon_footprint_pcf ELSE 0 END), 2) AS `2014_Emission`,
    ROUND(SUM(CASE WHEN pe.year = 2015 THEN pe.carbon_footprint_pcf ELSE 0 END), 2) AS `2015_Emission`,
    ROUND(SUM(CASE WHEN pe.year = 2016 THEN pe.carbon_footprint_pcf ELSE 0 END), 2) AS `2016_Emission`,
    ROUND(SUM(CASE WHEN pe.year = 2017 THEN pe.carbon_footprint_pcf ELSE 0 END), 2) AS `2017_Emission`
FROM product_emissions pe
LEFT JOIN industry_groups AS ig ON ig.id = pe.industry_group_id
GROUP BY ig.industry_group
ORDER BY
    ROUND(SUM(CASE WHEN pe.year = 2017 THEN pe.carbon_footprint_pcf ELSE 0 END), 2),
    ROUND(SUM(CASE WHEN pe.year = 2016 THEN pe.carbon_footprint_pcf ELSE 0 END), 2),
    ROUND(SUM(CASE WHEN pe.year = 2015 THEN pe.carbon_footprint_pcf ELSE 0 END), 2),
    ROUND(SUM(CASE WHEN pe.year = 2014 THEN pe.carbon_footprint_pcf ELSE 0 END), 2),
    ROUND(SUM(CASE WHEN pe.year = 2013 THEN pe.carbon_footprint_pcf ELSE 0 END), 2);
```
|Industry_Group|2013_Emission|2014_Emission|2015_Emission|2016_Emission|2017_Emission|
|--------------|-------------|-------------|-------------|-------------|-------------|
|Household & Personal Products|0.00|0.00|0.00|0.00|0.00|
|"Pharmaceuticals, Biotechnology & Life Sciences"|32271.00|40215.00|0.00|0.00|0.00|
|Tobacco|0.00|0.00|1.00|0.00|0.00|
|Semiconductors & Semiconductors Equipment|0.00|0.00|3.00|0.00|0.00|
|Retailing|0.00|19.00|11.00|0.00|0.00|
|Gas Utilities|0.00|0.00|122.00|0.00|0.00|
|Food & Beverage Processing|0.00|0.00|141.00|0.00|0.00|
|Telecommunication Services|52.00|183.00|183.00|0.00|0.00|
|Trading Companies & Distributors and Commercial Services & Supplies|0.00|0.00|239.00|0.00|0.00|
|"Textiles, Apparel, Footwear and Luxury Goods"|0.00|0.00|387.00|0.00|0.00|
|"Consumer Durables, Household and Personal Products"|0.00|0.00|931.00|0.00|0.00|
|Tires|0.00|0.00|2022.00|0.00|0.00|
|Containers & Packaging|0.00|0.00|2988.00|0.00|0.00|
|"Mining - Iron, Aluminum, Other Metals"|0.00|0.00|8181.00|0.00|0.00|
|"Forest and Paper Products - Forestry, Timber, Pulp and Paper, Rubber"|0.00|0.00|8909.00|0.00|0.00|
|Chemicals|0.00|0.00|62369.00|0.00|0.00|
|Electrical Equipment and Machinery|0.00|0.00|9801558.00|0.00|0.00|
|Food & Staples Retailing|0.00|773.00|706.00|2.00|0.00|
|Semiconductors & Semiconductor Equipment|0.00|50.00|0.00|4.00|0.00|
|Utilities|122.00|0.00|0.00|122.00|0.00|
|Consumer Durables & Apparel|2867.00|3280.00|0.00|1162.00|0.00|
|Media|9645.00|9645.00|1919.00|1808.00|0.00|
|Energy|750.00|0.00|0.00|10024.00|0.00|
|Automobiles & Components|130189.00|230015.00|817227.00|1404833.00|0.00|
|Software & Services|6.00|146.00|22856.00|22846.00|690.00|
|Commercial & Professional Services|1157.00|477.00|0.00|2890.00|741.00|
|"Food, Beverage & Tobacco"|4995.00|2685.00|0.00|100289.00|3162.00|
|Technology Hardware & Equipment|61100.00|167361.00|106157.00|1566.00|27592.00|
|Capital Goods|60190.00|93699.00|3505.00|6369.00|94949.00|
|Materials|200513.00|75678.00|0.00|88267.00|213137.00|
![image](https://github.com/user-attachments/assets/78c3001f-446c-4979-ad41-471651472c46)
- The "Chemicals" industry group has a significantly higher emission value in 2015 (dark blue bar) compared to all other industries and years. 
