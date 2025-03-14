

# 🌾 Agriculture Data Analysis - Rainfall, Temperature, Humidity, and Yield  

## 📌 Overview  
This project analyzes the **relationship between weather patterns** (rainfall, temperature, humidity) and **crop yield**. By leveraging **AWS, Snowflake, and Power BI**, we aim to provide **data-driven insights** to optimize farming practices and predict agricultural production.  

## 🛠️ Technologies Used  
- **Cloud Platform:** AWS (Amazon S3 for data storage)  
- **Data Warehouse:** Snowflake  
- **Data Querying & Transformation:** Snowflake SQL  
- **Data Visualization & Dashboarding:** Power BI  

---  

## 🔄 Project Workflow  

### 1️⃣ Data Acquisition & Storage (**AWS S3**)  
- Agricultural datasets, including weather and yield data, are stored in **AWS S3**.  
- A **Snowflake Storage Integration** is configured to securely access S3 data.  

### 2️⃣ Data Warehousing & Transformation (**Snowflake**)  
- A **Snowflake database and schema** are created for structured data management.  
- Data is **loaded from AWS S3 into Snowflake** using the `COPY INTO` command.  
- **Data cleaning and transformations** include:  
  - Adjusting rainfall and area values for scenario analysis.  
  - Creating categorical columns like `Year_Group` and `Rainfall_Groups` for analysis.  

### 3️⃣ Data Visualization & Dashboarding (**Power BI**)  
- Data from **Snowflake is imported into Power BI Desktop**.  
- **Interactive dashboards** analyze:  
  - Rainfall impact on crop yield.  
  - Temperature variations and their effects on yield.  
  - Humidity levels and their influence on agricultural output.  
  - Overall yield analysis considering multiple weather factors.  



---  

## 📊 Power BI Dashboard Insights  
The **Power BI dashboard** presents **interactive visualizations** showing:  

1. 📈 **Rainfall Patterns vs. Crop Yield**  
   - Identifies how different rainfall levels affect crop yield.  

![Image](https://github.com/user-attachments/assets/7e26c436-50a4-484e-95b2-e0e1d0568e9e)

2. 🌡️ **Temperature Impact on Yield**  
   - Analyzes seasonal temperature variations and their correlation with agricultural production.

![Image](https://github.com/user-attachments/assets/8e06d853-605d-4527-a2cf-31503b4df24e)

3. 💧 **Humidity and Agricultural Output**  
   - Evaluates how humidity levels impact crop growth. 

![Image](https://github.com/user-attachments/assets/7aaef907-2e78-40bf-b380-1b2285fe97b9) 

4. 📊 **Comprehensive Yield Analysis**  
   - Examines the combined impact of rainfall, temperature, and humidity on yield.

![Image](https://github.com/user-attachments/assets/37b8dbb9-7176-4cdf-9fd8-63bbd153c515)
---  


## 🗄️ Snowflake Integration Code  

```sql
-- Creating a Snowflake storage integration for AWS S3  
CREATE OR REPLACE STORAGE INTEGRATION PBI_Integration  
  TYPE = EXTERNAL_STAGE  
  STORAGE_PROVIDER = 'S3'  
  ENABLED = TRUE  
  STORAGE_AWS_ROLE_ARN = 'arn:aws:iam::825765422200:role/powerbi.role'  
  STORAGE_ALLOWED_LOCATIONS = ('s3://powerbi.project/')  
  COMMENT = 'Power BI Integration with AWS S3';  

-- Checking the integration  
DESC INTEGRATION PBI_Integration;  

-- Creating the database and schema  
CREATE DATABASE PowerBI;  
CREATE SCHEMA PBI_Data;  

-- Creating the dataset table  
CREATE TABLE PBI_Dataset (  
    Year INT,  
    Location STRING,  
    Area INT,  
    Rainfall FLOAT,  
    Temperature FLOAT,  
    Soil_type STRING,  
    Irrigation STRING,  
    Yield INT,  
    Humidity FLOAT,  
    Crops STRING,  
    Price INT,  
    Season STRING  
);  

-- Copying data from AWS S3 to Snowflake  
COPY INTO PBI_Dataset  
FROM @pbi_stage  
FILE_FORMAT = (TYPE=CSV FIELD_DELIMITER=',' SKIP_HEADER=1)  
ON_ERROR = 'CONTINUE';  

-- Creating an agriculture table as a copy of PBI_Dataset  
CREATE TABLE Agriculture AS  
SELECT * FROM PBI_Dataset;  

-- Updating data values for scenario analysis  
UPDATE Agriculture SET Rainfall = 1.1 * Rainfall;  
UPDATE Agriculture SET Area = 0.9 * Area;  

-- Creating a Year_Group column to categorize years  
ALTER TABLE Agriculture ADD Year_Group STRING;  

-- Assigning year groups  
UPDATE Agriculture SET Year_Group = 'Y1' WHERE Year BETWEEN 2004 AND 2009;  
UPDATE Agriculture SET Year_Group = 'Y2' WHERE Year BETWEEN 2010 AND 2015;  
UPDATE Agriculture SET Year_Group = 'Y3' WHERE Year BETWEEN 2016 AND 2019;  

-- Creating a Rainfall_Groups column  
ALTER TABLE Agriculture ADD Rainfall_Groups STRING;  

-- Assigning rainfall categories  
UPDATE Agriculture SET Rainfall_Groups = 'Low' WHERE Rainfall BETWEEN 255 AND 1200;  
UPDATE Agriculture SET Rainfall_Groups = 'Medium' WHERE Rainfall BETWEEN 1200 AND 2800;  
UPDATE Agriculture SET Rainfall_Groups = 'High' WHERE Rainfall BETWEEN 2800 AND 4103;  

-- Verifying the final dataset  
SELECT * FROM Agriculture;  
```

---  

## 📂 Project Structure  

```
📂 Agriculture-Data-Analysis  
│── 📁 Data  
│   ├── Rainfall_Temperature_Humidity_Yield.csv  
│── 📁 SQL-Scripts  
│   ├── Snowflake_Integration.sql  
│── 📁 PowerBI-Dashboards  
│   ├── Agriculture_Analysis.pbix  
│── 📜 README.md  
```  

---  

## 📌 Key Takeaways  
✅ **Cloud-Based Data Pipeline:** Efficient data storage (AWS S3), processing (Snowflake), and visualization (Power BI).  
✅ **Data Transformation & Cleaning:** Using **Snowflake SQL** to structure and analyze agricultural datasets.  
✅ **Predictive Insights:** Helps farmers and policymakers optimize agricultural strategies based on weather conditions.  

## 📥 **How to Use the Project**  

1. Open https://github.com/Medha-77/Impact-of-Weather-Patterns-on-Crop-Yield
2. Explore **interactive dashboards & insights!** 


---  

### 🌱 **This project demonstrates how cloud-based data analytics can transform agriculture!** 

select * from agriculture;
