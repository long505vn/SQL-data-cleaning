# SQL-data-cleaning
This is an educational project on data cleaning and preparation using SQL. The original database in CSV format is located in the file club_member_info.csv. Here, we will explore the steps that need to be applied to obtain a cleansed version of the dataset.

## 1. CREATE NEW TABLE
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

## 2. Make a copy of the table
        INSERT INTO club_member_info_cleaned 
        SELECT * FROM club_member_info 

## TABLE SAMPLE
|full_name|age|martial_status|email|phone|full_address|job_title|membership_date|
|---------|---|--------------|-----|-----|------------|---------|---------------|
|addie lush|40|married|alush0@shutterfly.com|254-389-8708|3226 Eastlawn Pass,Temple,Texas|Assistant Professor|7/31/2013|
|      ROCK CRADICK|46|married|rcradick1@newsvine.com|910-566-2007|4 Harbort Avenue,Fayetteville,North Carolina|Programmer III|5/27/2018|
|Sydel Sharvell|46|divorced|ssharvell2@amazon.co.jp|702-187-8715|4 School Place,Las Vegas,Nevada|Budget/Accounting Analyst I|10/6/2017|
|Constantin de la cruz|35||co3@bloglines.com|402-688-7162|6 Monument Crossing,Omaha,Nebraska|Desktop Support Technician|10/20/2015|
|  Gaylor Redhole|38|married|gredhole4@japanpost.jp|917-394-6001|88 Cherokee Pass,New York City,New York|Legal Assistant|5/29/2019|
|Wanda del mar       |44|single|wkunzel5@slideshare.net|937-467-6942|10864 Buhler Plaza,Hamilton,Ohio|Human Resources Assistant IV|3/24/2015|
|Joann Kenealy|41|married|jkenealy6@bloomberg.com|513-726-9885|733 Hagan Parkway,Cincinnati,Ohio|Accountant IV|4/17/2013|
|   Joete Cudiff|51|divorced|jcudiff7@ycombinator.com|616-617-0965|975 Dwight Plaza,Grand Rapids,Michigan|Research Nurse|11/16/2014|
|mendie alexandrescu|46|single|malexandrescu8@state.gov|504-918-4753|34 Delladonna Terrace,New Orleans,Louisiana|Systems Administrator III|3/12/1921|
| fey kloss|52|married|fkloss9@godaddy.com|808-177-0318|8976 Jackson Park,Honolulu,Hawaii|Chemical Engineer|11/5/2014|

## 3. Clean data and document it
1. Xoá các khoảng trống trước và sau của full_name

        UPDATE club_member_info_cleaned 
        SET full_name = TRIM(full_name);

2. Viết hoa full_name

        UPDATE club_member_info_cleaned 
        SET full_name = UPPER(full_name);

3. Update các ô trống trong cột maria_status = Unknown

        UPDATE club_member_info_cleaned 
        SET martial_status = 'Unknown'
        WHERE martial_status ="";

4. Update các ô tuổi bị bỏ trống hoặc có giá trị sai (>= 100) thành trung bình của cột tuổi

        UPDATE club_member_info_cleaned 
        SET age = ROUND((SELECT AVG(age) FROM club_member_info_cleaned WHERE age < 100))
        WHERE age = "" OR age >= 100;

## 4. XOÁ CÁC DÒNG BỊ DUPLICATE theo email (Mỗi email chỉ được tương ứng với 1 id) 
1. Tạo bảng mới với các cột như bảng "club_member_info_cleaned" và thêm 1 cột id có thuộc tính autoincrement

        CREATE TABLE club_member_info_cleaned_new (
        	id INTEGER PRIMARY KEY AUTOINCREMENT,
        	full_name VARCHAR(50),
        	age INTEGER,
        	martial_status VARCHAR(50),
        	email VARCHAR(50),
        	phone VARCHAR(50),
        	full_address VARCHAR(50),
        	job_title VARCHAR(50),
        	membership_date VARCHAR(50)
        );

2. COPY dữ liệu bảng cũ sang bảng mới

        INSERT INTO club_member_info_cleaned_new (full_name, age, martial_status, email, phone, full_address, job_title, membership_date)
        SELECT full_name, age, martial_status, email, phone, full_address, job_title, membership_date
        FROM club_member_info_cleaned;

3. DELETE các dòng có giá trị email bị lặp lại

        DELETE FROM club_member_info_cleaned_new 
        WHERE id NOT IN
        (SELECT MIN(id) AS min_id 
        FROM club_member_info_cleaned_new
        GROUP BY email);


# PRACTICE 3.5 Project 2 - Carbon Emission Analysis
## Q1. Which products contribute the most to carbon emissions?

```
    SELECT product_name, AVG(carbon_footprint_pcf) 
    FROM product_emissions
    GROUP BY product_name
    ORDER BY AVG(carbon_footprint_pcf) DESC
```

## Q2. What are the industry groups of these products?

```
    SELECT DISTINCT industry_group
    FROM industry_groups
```

## Q3. What are the industries with the highest contribution to carbon emissions?

```
    SELECT industry_group, AVG(carbon_footprint_pcf)
    FROM product_emissions AS p
    LEFT JOIN industry_groups AS i ON p.industry_group_id = i.id
    GROUP BY industry_group
    ORDER BY AVG(carbon_footprint_pcf) DESC
```

## Q4. What are the companies with the highest contribution to carbon emissions?

```
    SELECT company_name, AVG(carbon_footprint_pcf)
    FROM product_emissions AS p
    LEFT JOIN companies AS c ON p.company_id = c.id
    GROUP BY company_name
    ORDER BY AVG(carbon_footprint_pcf) DESC
```

## Q5. What are the countries with the highest contribution to carbon emissions?

```
    SELECT country_name, SUM(carbon_footprint_pcf)
    FROM product_emissions AS p
    LEFT JOIN countries AS c ON p.country_id = c.id
    GROUP BY country_name
    ORDER BY SUM(carbon_footprint_pcf) DESC
```

## Q6. What is the trend of carbon footprints (PCFs) over the years?

```
    SELECT year, SUM(carbon_footprint_pcf)
    FROM product_emissions AS p
    GROUP BY year
    ORDER BY year 
```

## Q7. Which industry groups has demonstrated the most notable decrease in carbon footprints (PCFs) over time?

        SELECT 
            industry_group,
            Total_2013,
            Total_2017,
            (Total_2017 - Total_2013) AS Thay_doi
        FROM 
        	(
            SELECT 
                industry_group_id,
                SUM(CASE WHEN year = '2013' THEN carbon_footprint_pcf ELSE 0 END) AS Total_2013,
                SUM(CASE WHEN year = '2017' THEN carbon_footprint_pcf ELSE 0 END) AS Total_2017
            FROM product_emissions
            GROUP BY industry_group_id
        	) AS sub
        	
        LEFT JOIN industry_groups AS i ON i.id = sub.industry_group_id
        ORDER BY Thay_doi
