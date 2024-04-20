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

The first step is removing duplicates from the data. 
'''
-- Step 1: Removing Duplicates
DELETE FROM raw_data
WHERE id IN (
    SELECT id
    FROM (
        SELECT id, ROW_NUMBER() OVER (PARTITION BY unique_column1, unique_column2 ORDER BY id) AS row_num
        FROM raw_data
    ) AS duplicates
    WHERE duplicates.row_num > 1
);
'''

-- Step 2: Handling Missing Values
UPDATE raw_data
SET column1 = COALESCE(column1, 'Unknown'),
    column2 = COALESCE(column2, 0),
    column3 = COALESCE(column3, 'N/A')
WHERE column1 IS NULL OR column2 IS NULL OR column3 IS NULL;


# Contributing
Feel free to contribute to this project by submitting pull requests or reporting issues.

# Authors
Your Name: Syed Shaheryar Haider
# License
This project is licensed under the MIT License.
