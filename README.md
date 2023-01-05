# Getting Started with DuckDB
1. [Install duckdb](https://duckdb.org/docs/installation):
	- MAC: use homebrew `brew install duckdb`
	- Windows: not sure
	- Universal: Python: `pip install duckdb==0.6.1`
2. Install CLI:
	- Download the CLI (is a regular file) and put it anywhere...
	- cd into the directory and start with `./duckdb`
3. Continued soon.

Install DBT:
```
pip install dbt-duckdb
```

# Create a table and populate it:
```
PRAGMA temp_directory='./.tmp'

CREATE TABLE weather_2 (
		date				  		DATE,
		time				  		VARCHAR,
		date_time		  		VARCHAR,
		visibility	  		INTEGER,
		sea_level_pressure	  REAL,
		relative_humidity_pct	  FLOAT,
		wind_speed_mph	  FLOAT,
		millibars  				FLOAT,
		air_temp_F			  FLOAT,
		wind_direction_degrees	  FLOAT
);

-- Insert from a file
COPY weather_2 FROM '/Users/yaakov/Desktop/Census Data/LA/weather_CA_2019.csv' (AUTO_DETECT TRUE);


-- Or, make life easy, and create the table as a select statement!
CREATE TABLE weather_3 AS SELECT * FROM read_csv_auto('/Users/yaakov/Desktop/Census Data/LA/weather_CA_2019.csv');

-- See if there are any diffs
SELECT
	A.*
FROM
	weather_2 AS A
LEFT JOIN
	weather_3 AS B
	ON A.date=B.date
	AND A.date_time = B.date_time
	-- AND A.visibility = B.visibility
WHERE
	B.date IS NULL
;
```
Now let's load in an SQL db:
```
INSTALL sqlite;
LOAD sqlite;

CALL sqlite_attach('/Users/yaakov/Desktop/Census Data/LA/California Traffic Collision Data from SWITRS/switrs.sqlite');

-- See the tables:
PRAGMA show_tables;
```

Let's compare performance
```
CREATE SCHEMA duck;
CREATE TABLE duck.victims  AS SELECT * from victims;
CREATE TABLE duck.case_ids  AS SELECT * from case_ids;
CREATE TABLE duck.collisions  AS SELECT * from collisions;
CREATE TABLE duck.parties  AS SELECT * from parties;


-- Count em'
SELECT
	'duck.victims' AS TABLE_NAME,
	count (*) AS N_ROWS
from duck.victims

UNION

SELECT
	'duck.case_ids' AS TABLE_NAME,
	count (*) AS N_ROWS
from duck.case_ids

UNION

SELECT
	'duck.collisions' AS TABLE_NAME,
	count (*) AS N_ROWS
from duck.collisions

UNION

SELECT
	'duck.parties' AS TABLE_NAME,
	count (*) AS N_ROWS
from duck.parties
;

EXPLAIN ANALYZE SELECT count (*) from victims_duck;


 ```
