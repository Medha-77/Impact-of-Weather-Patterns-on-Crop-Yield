
# Agriculture Data Analysis - Rainfall, Temperature, Humidity, and Yield

## Overview

This project investigates the relationship between weather patterns (rainfall, temperature, humidity) and crop yield.  The aim is to provide insights into how these environmental factors impact agricultural output, potentially for optimizing farming practices or predicting yields.

This project utilizes a cloud-based data pipeline, leveraging the power of AWS for data storage, Snowflake for data warehousing and transformation, and Power BI for interactive data visualization and dashboarding.

## Technologies Used

*   **Cloud Platform:** AWS (Likely AWS S3 for data storage)
*   **Data Warehouse:** Snowflake
*   **Data Transformation & Querying:** Snowflake SQL
*   **Data Visualization & Dashboarding:** Power BI

## Workflow/Steps

The project follows these key steps:

1.  **Data Acquisition & Storage (AWS):**
    *   Agricultural datasets (including weather data and crop yield data) are managed and accessed using AWS cloud services, likely utilizing AWS S3 for storage.
    *   Snowflake Storage Integration is configured to securely connect to the data stored in AWS S3.

2.  **Data Warehousing & Transformation (Snowflake):**
    *   Snowflake is used as a cloud data warehouse to store and manage the agricultural datasets.
    *   A dedicated Snowflake database and schema are created for the project.
    *   Extensive data transformation and cleaning are performed using Snowflake SQL. Key SQL tasks include:
        *   Loading data from AWS S3 into Snowflake tables using the `COPY INTO` command.
        *   Updating data values (e.g., rainfall, area) using `UPDATE` statements for data manipulation and scenario analysis.
        *   Creating new categorical columns (e.g., `Year_Group`, `rainfall_groups`) using `ALTER TABLE` and `UPDATE` statements to segment and group data for enhanced analysis.

3.  **Data Visualization & Dashboarding (Power BI):**
    *   Data is imported from Snowflake into Power BI Desktop.
    *   Interactive dashboards are developed in Power BI to analyze:

       
        Rainfall patterns and their correlation with crop yield.


        ![Image](https://github.com/user-attachments/assets/7e26c436-50a4-484e-95b2-e0e1d0568e9e)

          Temperature variations and their impact on yield.

        ![Image](https://github.com/user-attachments/assets/8e06d853-605d-4527-a2cf-31503b4df24e)

        Humidity levels and their influence on agricultural output.
        
![Image](https://github.com/user-attachments/assets/7aaef907-2e78-40bf-b380-1b2285fe97b9)

         Overall yield analysis in relation to combined weather factors.

![Image](https://github.com/user-attachments/assets/37b8dbb9-7176-4cdf-9fd8-63bbd153c515)
    
     The Power BI dashboard is designed to present findings related to the impact of rainfall, temperature, and humidity on agricultural yield in a clear and interactive manner.

## Readme File Contents (Snowflake Integration Code)

```sql
 CREATE OR REPLACE STORAGE INTEGRATION PBI_Integration
  TYPE = EXTERNAL_STAGE
  STORAGE_PROVIDER = 'S3'
  ENABLED = TRUE
  STORAGE_AWS_ROLE_ARN = 'arn:aws:iam::825765422200:role/powerbi.role'
  STORAGE_ALLOWED_LOCATIONS = ('s3://powerbi.project/')
  COMMENT = 'Optional Comment';


  //description Integration Object
  desc integration PBI_Integration;

//drop integration PBI_Integration

--------------------------------------------
//drop database PowerBI

CREATE database PowerBI;

create schema PBI_Data;

create table PBI_Dataset (
Year int,	Location string,	Area	int,
Rainfall	float, Temperature	float, Soil_type string,
Irrigation	string, yeilds	int,Humidity	float,
Crops	string,price	int,Season string
);

select * from PBI_Dataset;

//drop database test;

create stage PowerBI.PBI_Data.pbi_stage
url = 's3://powerbi.project'
storage_integration = PBI_Integration;

//desc stage s1

//drop stage s1;


copy into PBI_Dataset
from @pbi_stage
file_format = (type=csv field_delimiter=',' skip_header=1 )
on_error = 'continue';

list @pbi_stage;

create table agriculture as
select * from pbi_dataset;

select * from agriculture;

update agriculture
set rainfall = 1.1*rainfall;

update agriculture
set area = 0.9*area;

select * from agriculture;


//Year 2004 & 2009 - Y1
//Year 2010 & 2015 - Y2
//Year 2016 & 2019 - Y3

ALTER TABLE Agriculture
add Year_Group String;

select * from agriculture;

//1st update
update agriculture
set year_group = 'Y1'
where year >=2004 and year<=2009;

//2nd update
update agriculture
set year_group = 'Y2'
where year >=2010 and year<=2015;


//3rd Update
update agriculture
set year_group = 'Y3'
where year >=2016 and year<=2019;


select * from agriculture;


//Rainfall_Groups
//Min 255 Max 4103

//rainfall 255 & 1200 - Low
//rainfall 1200 2800 - Medium
//Rainfall 2800 & 4103 - High

alter table agriculture
add rainfall_groups string;

select * from agriculture;

//1st Update
update agriculture
set rainfall_groups = 'Low'
where rainfall>=255 and rainfall<1200;

//2nd update
update agriculture
set rainfall_groups = 'Medium'
where rainfall >=1200 and rainfall<2800;

//3rd update
update agriculture
set rainfall_groups='High'
where rainfall >=2800;

select * from agriculture;
