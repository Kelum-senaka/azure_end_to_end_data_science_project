# Azure for End to End Data Science Project


In this project,Different azure services have used for each data science task like data ingestion,data transformation,data loading and data reporting.As a database "AdventureWorks Database" was utilized.Below images shows entire architecture of the project.

<p align="center">
  <img width="60%" height="60%" src="images\architecture.png">
</p>

## Data Ingestion
To connect the on premises database with the cloud storage,self hosted runtime instlled into my local machine.Once it installed link service created for on-premises databases through azure data factory.This on-premises database shoud be copy into the azure data lake storage.Inside the data lake storage i created different directries called bornze,silver and gold.In each layer represents different level of data transformations.ADF was connected with bronze layer.It includes raw data same as on-premises database.Inside the ADF,pipeline created for copy data from on-premises to cloud.Following image represents each components of the pipeline which required to copy the data.

<p align="center">
  <img width="60%" height="60%" src="images\adf_pipeline_ingestion.png">
</p>


## Data Transformation
Once data copy into the bronze layer through the data ingestion,I did data transformation using azure databricks service.Transformation has done in two phases.In first phase,transformed data stored into the silver layer and in second phase it is stored in gold layer.Data inluded inside the gold layer is suitable to consume for the report/data visualization purpose.To do  the each level of transformation prepared the script inside the two notebook files.

Level 1 transformation - bronze to silver.ipynb
Level 2 transformation - silver to gold.ipynb

Full pipeline with data ingestion and transformations

<p align="center">
  <img width="60%" height="60%" src="images\adf_pipeline_full.png">
</p>

## Data Loading
Azure synpase analytics was used to load the data from gold layer to the SQL datbase in azure.But in here I made only data tables views.To make views for all the tables,stored procedure was used iside the SQL DB with refferencing the data lake bronze layer.Inside the synapse created a pipeline for data loading purpose.

<p align="center">
  <img width="60%" height="60%" src="images\synapse_pipeline.png">
</p>

Script for stored procedure:


      USE gold_DB
      GO

      CREATE or ALTER  PROC serverless_gold_view (@viewname NVARCHAR(100))
      AS
      BEGIN

      DECLARE @statement VARCHAR(MAX)
          SET @statement = N' CREATE or ALTER VIEW '+ @viewname + ' AS 
              SELECT *FROM 
                  OPENROWSET(BULK ''https://adls4adventure.dfs.core.windows.net/gold/SalesLT/'+ @viewname +'/'',
                  FORMAT=''DELTA'') AS [result]
          '

      EXEC (@statement)

      END
      GO





## Data Visualization
At the final step,Power BI connect with the synapse SQL DB to visualize the data as per the business requirements.But here I created some simple report.

<p align="center">
  <img width="60%" height="60%" src="images\product_report.png">
</p>