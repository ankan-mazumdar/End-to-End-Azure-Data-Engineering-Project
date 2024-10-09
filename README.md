Project Overview –
 

Agenda-
Part I: Environment setup 
Part 2: Data Ingestion 
Part 3: Data Transformation 
Part 4: Data Loading 
Part 5: Data Reporting  
Part 6: End to End Pipeline Testing

Part I: Environment setup 
Azure Portal
 

 
ankantestsqlserver
SQlserve creds-


Alternate way to copy local SQL server data to Azure-
https://www.youtube.com/watch?v=twJxzJI1K3w&t=2s&pp=ygUhY29ubmVjdCBsb2NhbCBzcWwgc2VydmVyIHRvIGF6dXJl
 

 


 
 

 
 

 
 

 

 

 

 

 

Part 3- Data Ingestion

 
 

 
 
 

 
 
 
 

 

 


https://stackoverflow.com/questions/76325987/access-policies-not-available
 



 
 

 


 

 



 

 
 

Missing jvm.dll in self-hosted integration runtime Windows Container

 
 



Solution- 
1.	reinstalled latest jave 23 ttps://download.oracle.com/java/23/latest/jdk-23_windows-x64_bin.exe (sha256)
2.	set system and environment variables for JAVA_HOME and path and checked java -version
3.	created all this in regedit
4.	restarted SHIR from services.msc intergarted service and reran the debug in Az df

 
 
 






 

 

SELECT
s. name AS SchemaName,
t. name AS TabletName
FROM sys.tables t
INNER JOIN sys.schemas s
ON t.schema_id = s.schema_id
WHERE s. name in ('production', 'sales')

 
 
 
 

 


Folder Structure
• bronze/Schema/Tablename/Tablename.parquet
bronze/SalesT/Address/ Address.parquet
 


 

 




 
 

Part 3 Data Transformations
 



 


 


 


 


https://learn.microsoft.com/en-us/azure/databricks/archive/credential-passthrough/adls-passthrough#azure-data-lake-storage-gen2
 


 

 
 
 

 

Bronze to silver
 

 
 

Silver to gold-
 

 

 
 


 

 
 
 


 
 
	 
 

 

Silver to Gold-
 
 


 
 

 
 

 
 

 






Part 7 – creating Pipeline

 

Integration runtime as Autoresolve cause Databricks is cloud based
 



3rd Token- 
 



Check how to enable interactive integration runtime to check test connection
https://www.youtube.com/watch?v=lecyAa6Pv8I&t=168s

 


 



 


 


 
 


 


 

 
Power of delta format
 

Part 8 - Data Loading (Azure Synapse Analytics)

Azure synapse analytics = azure data factory(it is built on top of AZ DF) + azure data bricks + additional nalytics
Dedicated costly datalake + compute , but serverless = only compute, as we already have data is already exist  in datlake, so servless will be most optimed
 

Link tab-
 


 
Connect to built-in sereverless
 

Create view-
 
iF any data modification happen in the main table, it going to be automatically reflected in the view as each time view executes the sql for the table
Create view for all the available tables inside the gold container
 
Also need to create pipeline to create views for the tables to fetch tables
Publish after execution

 


 
 


 
 
 

 
This whole list of child items will be passed as input to the for each loop by setting below item config-
 
 


 Run instance-
 
 



Part next- data Reporting
Power BI

 


 



 


Report Tab ,Data Tab and Model Tab
 

Model Tab with tables replationship-
 

 

Use manage relation and create new relation so that any updates are in sync and reflected overall among all tables, chose the tables from drop dwon and select cardinality and cross filer direction to make reports more interactive


 
Create dashboard-
 

Final Dashboard
 

Part 10 - Security and Governance (AAD)

Initially only account of mine which is owner has access -
 

So in order to provide access a security group so that all employee/developers/data engineers belonging to that group get access
 

If it is inaccessible, then you’re owner only, and need to contact your azure admin

 
 

 

	 


Part end- End to End Pipeline Testing

 
 


Make changes in the source- add 2 new rows in the table-
 

Dashboard data values before trigger execution-
 
After execution-
 
 






