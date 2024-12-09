# SQL-data-cleaning
This is an educational project on data cleaning and preparation using SQL. The original database in CSV format is located in the file club_member_info.csv. Here, we will explore the steps that need to be applied to obtain a cleansed version of the dataset.
## Creat new table
    CREATE TABLE club_member_info_cleaned (
    	full_name VARCHAR(50),
    	age INTEGER,
    	martial_status VARCHAR(50),
    	email VARCHAR(50),
    	phone VARCHAR(50),
    	full_address VARCHAR(50),
    	job_title VARCHAR(50),
    	membership_date VARCHAR(50)
    );

