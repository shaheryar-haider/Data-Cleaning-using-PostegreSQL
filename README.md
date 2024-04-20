# Data-Cleaning-using-PostegreSQL

# Overview
Welcome to the data cleaning project using PostgreSQL! This project focuses on cleaning and preparing raw data stored in a PostgreSQL database. The goal is to transform messy or incomplete data into a structured and usable format.

# Project Structure
Scripts: This folder contains SQL scripts for data cleaning operations.
Documentation: Contains documentation related to database schema, data dictionaries, and cleaning processes.
Data: Placeholder folder for input and output data files, if applicable.

# Documentation
Schema: Document the database schema used in this project (Documentation/schema.md).
Data Dictionary: Describe the tables and fields used in the database (Documentation/data_dictionary.md).
Cleaning Processes: Detail the step-by-step process of data cleaning (Documentation/cleaning_processes.md).

# Snippets of Code to clean Data 
1. Create the Target Table
First, create the target table merged_data if it doesn't exist. This table will hold the merged and cleaned data.

```
CREATE TABLE IF NOT EXISTS merged_data (
    trip_id INTEGER,
    trip_duration_second INTEGER,
    from_station_id INTEGER,
    trip_start_time TIMESTAMP,
    from_station_name TEXT,
    trip_stop_time TIMESTAMP,
    to_station_id INTEGER,
    to_station_name TEXT,
    user_type TEXT
);
```

2. Merge Data from CSV Files into the Target Table
Insert data from four CSV files into the merged_data table using an INSERT INTO ... SELECT ... UNION ALL statement.

```
INSERT INTO merged_data (
    trip_id,
    trip_duration_second,
    from_station_id,
    trip_start_time,
    from_station_name,
    trip_stop_time,
    to_station_id,
    to_station_name,
    user_type
)
SELECT
    trip_id,
    trip_duration_second,
    from_station_id,
    trip_start_time,
    from_station_name,
    trip_stop_time,
    to_station_id,
    to_station_name,
    user_type
FROM (
    SELECT * FROM 'C:\Users\Shariq\Desktop\Toronto Bike Share Data\bikeshare2018\bikeshare2018\Bike Share Toronto Ridership_Q1 2018' CSV HEADER
    UNION ALL
    SELECT * FROM 'C:\Users\Shariq\Desktop\Toronto Bike Share Data\bikeshare2018\bikeshare2018\Bike Share Toronto Ridership_Q2 2018' CSV HEADER
    UNION ALL
    SELECT * FROM 'C:\Users\Shariq\Desktop\Toronto Bike Share Data\bikeshare2018\bikeshare2018\Bike Share Toronto Ridership_Q3 2018' CSV HEADER
    UNION ALL
    SELECT * FROM 'C:\Users\Shariq\Desktop\Toronto Bike Share Data\bikeshare2018\bikeshare2018\Bike Share Toronto Ridership_Q4 2018' CSV HEADER
) AS all_data;
```

3. Remove Duplicate Rows
Identify and delete duplicate rows in the merged_data table based on specific criteria (from_station_id and trip_start_time) to ensure each record is unique.

```
DELETE FROM merged_data
WHERE trip_id IN (
    SELECT trip_id
    FROM (
        SELECT
            trip_id,
            ROW_NUMBER() OVER (PARTITION BY from_station_id, trip_start_time ORDER BY trip_id) AS row_num
        FROM merged_data
    ) AS duplicates
    WHERE duplicates.row_num > 1
);

```

4. Handle Missing Values
Update the from_station_name, to_station_name, and user_type columns in the merged_data table to replace NULL values with the default value 'Unknown' for data completeness.

```
UPDATE merged_data
SET
    from_station_name = COALESCE(from_station_name, 'Unknown'),
    to_station_name = COALESCE(to_station_name, 'Unknown'),
    user_type = COALESCE(user_type, 'Unknown')
WHERE
    from_station_name IS NULL
    OR to_station_name IS NULL
    OR user_type IS NULL;
```

# Summary
This refined approach outlines the steps clearly for merging data from multiple files into a single table (merged_data) and performing necessary data cleaning tasks (removing duplicates, handling null values) to ensure the accuracy and completeness of the dataset. Adjust the file paths, table name (merged_data), and column names based on your specific use case and database schema.

# Contributing
Feel free to contribute to this project by submitting pull requests or reporting issues.

# Authors
Your Name: Syed Shaheryar Haider
# License
This project is licensed under the MIT License.
