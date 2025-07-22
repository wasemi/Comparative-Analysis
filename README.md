## Comparative-Analysis

### Project Overview
The airline industry is regionally diverse, and profitability can vary significantly by region. As an analyst, I am tasked with helping leadership identify which regions are operating most efficiently and profitably.

### Data Sources - The data source used is Airline Performance analysis

### Meta Data
-  iata_code - The unique 2- or 3-letter airline code assigned by IATA
- airline_name - Full name of the airline
- region- Geographical region where the airline is primarily based or operates
- functional_currency- The currency used in the airline financial reporting
- ebit_usd - Earnings Before Interest and Taxes (EBIT) reported in US dollars  a measure of profitability
- load_factor - Percentage of available seating capacity that is actually filled with passengers (efficiency metric)
- low_cost_carrier - Indicates whether the airline is a low-cost carrier ('Y') or not ('N')
- airline_age - Age of the airline in years
- num_routes - Number of flight routes operated by the airline
- passenger_yield	- Revenue per revenue passenger kilometer  higher means more revenue per passenger distance
- ask - Available Seat Kilometers total passenger carrying capacity (seats * distance flown)
- avg_fleet_age - Average age of all aircraft in the airline's fleet (in years)
- fleet_size - Total number of aircraft in the airline's fleet
- aircraft_utilisation  Average number of hours each aircraft in the fleet is operated per day


### Tools Used
- Excel - used in initial cleaning of the data
- SQL - used in analysing the data and answering the question statement

### Data Prepartion and Cleaning
- Loaded the data into Excel to understand the data.
- Used Power Query to perform the data cleaning
    - handled nulls in some columns using the mean and median imputation.
- Imported data into SQL using PSQL for further analysis using pgadmin

### Exploratory Data Analysis
- Average EBIT (USD)
- Average Load Factor
- Average Passenger Yield
- a, where ask = Available Seat Kilometers
- Average Airline Age
- Show a count of low-cost carriers per region

### Data Analysis
These are queries i used in answering the business question
```sql
CREATE TABLE airline (
	iata_code VARCHAR (100) PRIMARY KEY NOT NULL,
	airline_name VARCHAR (200),
	region VARCHAR (200),
	functional_currency VARCHAR (250),
	ebit_usd numeric,
	load_factor float,
	low_cost_carrier boolean,
	airline_age int,
	num_routes int,
	passenger_yield float,
	ask VARCHAR (200),
	avg_fleet_age numeric,
	fleet_size int,
	aircraft_utilisation numeric
	
);

--Check for duplicates
SELECT iata_code, airline_name, region, functional_currency, ebit_usd, load_factor, low_cost_carrier,
	airline_age, num_routes, passenger_yield, ask, avg_fleet_age, fleet_size, aircraft_utilisation,
	ROW_NUMBER () OVER (PARTITION BY iata_code) AS rn
FROM airline

--calculate averag EBIT that is Earnings before interest and Taxes
SELECT region, AVG(ebit_usd) AS avg_ebit
FROM airline
GROUP BY region 

SELECT * FROM airline

--calculate average load factor
SELECT region, AVG(load_factor) AS avg_load_factor
FROM airline
GROUP BY region 

--calculate average airline_age
SELECT region, AVG(airline_age) AS avg_airline_age
FROM airline
GROUP BY region 


--calculate Fleet Utilization Proxy: Calculate aircraft utilization as ask / fleet_size
SELECT airline_name, region, aircraft_utilisation, fleet_size,
	(aircraft_utilisation/fleet_size) AS fleet_utilisation_proxy
FROM airline
GROUP BY airline_name, region, aircraft_utilisation, fleet_size 
ORDER BY fleet_utilisation_proxy DESC

--calculate average airline age 
SELECT region, AVG(passenger_yield) AS avg_passenger_yield
FROM airline
GROUP BY region 

--Which region has the best overall balance across profitability and operational efficiency?
SELECT region, SUM(ebit_usd) AS EBIT, SUM(load_factor) AS load_factor, SUM(passenger_yield) AS passenger_yield
FROM airline
GROUP BY ROLLUP (region)


--calculate low_cost carrier
SELECT COUNT(low_cost_carrier) AS count_of_lcc, region
FROM airline
WHERE low_cost_carrier = 'Y'
GROUP BY region
```
