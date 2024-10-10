
# **End-to-End Azure Data Engineering Project**

Welcome! This readme walks you through the complete setup and implementation of an Azure Data Engineering pipeline. We'll cover everything from environment setup, data ingestion, transformation, and reporting, to testing the pipeline. Each part includes detailed steps, tools used, and images for clarity.

## **Project Overview**

This project demonstrates how to build an end-to-end data engineering solution using various Azure resources, including Azure Data Factory, Azure SQL Database, Azure Databricks, Azure Data Lake, Azure Synapse Analytics, Power BI, and more. The goal is to migrate, transform, and visualize data efficiently.

 ![image](https://github.com/user-attachments/assets/f2599900-45c3-4b89-9c57-5f1b17c39a8a)
 
### **Agenda**

- **Part I**: Environment Setup
- **Part II**: Data Ingestion
- **Part III**: Data Transformation
- **Part IV**: Data Loading
- **Part V**: Data Reporting
- **Part VI**: Data Security and Access Management
- **Part VII**: Data Governance and Compliance
- **Part VIII**: Automating the Pipeline
- **Part IX**: Optimizing Performance
- **Part X**: Building Analytical Models
- **Part XI**: End-to-End Testing

---

## **Part I: Environment Setup**

In this section, we set up the Azure environment, configuring essential resources like Azure Data Factory, Azure SQL Database, and Azure Data Lake Storage. 
 
![image](https://github.com/user-attachments/assets/3bfb886b-d6df-4f3b-b452-3193eec814d9)
![image](https://github.com/user-attachments/assets/92d22e54-d8b9-4ac4-aada-600fcdaef378)


1. **Azure Portal Access**
   - Log in to the [Azure Portal](https://portal.azure.com) with your credentials. Ensure you have permissions to create and manage resources.
   - If you don't have an account, you can [sign up here](https://azure.microsoft.com/free/).

2. **Creating the Azure SQL Database**
   - Navigate to **SQL Databases** in the portal.
   - Click **Create**, fill in details like **Database name**, and choose a **Server** (create one if needed).
   - Securely note down your SQL Server credentials for later.

3. **Configuring Azure Data Factory**
   - Search for **Data Factory** and create an instance.
   - Set up a pipeline connecting your on-premise SQL database using the [self-hosted integration runtime](https://www.youtube.com/watch?v=twJxzJI1K3w&t=2s).

4. **Setting Up Azure Data Lake Storage Gen 2**
   - Go to **Storage accounts** and create a new account with **hierarchical namespace** enabled.
   - This storage will hold our data in **bronze**, **silver**, and **gold** layers.

5. **Testing Connectivity**
   - Test connectivity between your SQL Database and Data Lake through Azure Data Factory before moving forward.

---


 Alternate way to copy local SQL server data to Azure,and the is the youtube demo for the same-
https://www.youtube.com/watch?v=twJxzJI1K3w&t=2s&pp=ygUhY29ubmVjdCBsb2NhbCBzcWwgc2VydmVyIHRvIGF6dXJl

![image](https://github.com/user-attachments/assets/7a49ef08-5c5e-4ca3-adfd-fede144109e4)

![image](https://github.com/user-attachments/assets/4117e734-dc81-437b-a282-d383cf62fb2c)

 ![image](https://github.com/user-attachments/assets/7b08200d-4c39-4876-b3f2-b3f266520719)

![image](https://github.com/user-attachments/assets/a42acaf7-7eba-4a9a-9528-9a655e1d0c32)
 
![image](https://github.com/user-attachments/assets/0c5acc9b-a39e-4e44-977b-19bce43fce31)

![image](https://github.com/user-attachments/assets/801710b4-4895-4073-9422-60ce4d06ae32)

![image](https://github.com/user-attachments/assets/9a6bf73d-0f37-4fe4-b5c9-5825d96f6a4b)

![image](https://github.com/user-attachments/assets/e6e575a9-c203-4d38-a92c-a1a75ab36e3b)



## **Part 2: SQL Server user credential creation and storing them in Key vault and with required accesses**
 **Create your login username and passwrd for the connection and provide necessary permission through IAMadmin- **

![image](https://github.com/user-attachments/assets/da1ca680-e1c0-441e-8bd9-468985617f6c)

![image](https://github.com/user-attachments/assets/56b92892-a370-4eca-a203-04e6b1d62686)

![image](https://github.com/user-attachments/assets/16d84f43-29fb-46c7-b13e-bf824fabb051)

![image](https://github.com/user-attachments/assets/ca547306-e69d-4a3b-b76a-7f1ae6216be7)
 

### Part 3 - Data Ingestion (1)

In this section, we'll walk through the process of ingesting data using Azure Data Factory (ADF). 

#### Step 1: Accessing Azure Data Factory

1. **Navigate to Azure Data Factory**:
   - Open your Azure portal, locate your resource group, and select Azure Data Factory.
   - Click on **Launch Studio** to enter the Azure Data Factory workspace.

#### Step 2: Integration Runtime Setup

1. **Integration Runtime Overview**:
   - Azure Data Factory cannot directly connect to on-premise SQL Server databases. 
   - To establish this connection, you need to set up an **Integration Runtime (IR)**, specifically a **Self-Hosted Integration Runtime (SHIR)**.

2. **Installing the Self-Hosted Integration Runtime (SHIR)**:
   - In the Azure Data Factory workspace, click on **Manage** in the left panel and select **Integration Runtimes**.
   - You’ll see an **Auto-Resolve Integration Runtime** (used for cloud-based resources). To connect to an on-premise database, you must create a new SHIR.
   - Click on **New**, select **Self-Hosted**, and click **Continue**.
   - Name the runtime (e.g., `SHIR`), add a description, and click **Create**.
   - Choose the **Express Setup** to automatically download and set up the runtime on your local machine.

3. **Testing the Integration Runtime Connection**:
   - Verify that the SHIR is installed and running by typing **Microsoft Integration Runtime** in the search bar of your local system.
   - The SHIR should show as connected to the Azure cloud service with the correct Data Factory name.

#### Step 3: Creating a Pipeline

1. **Creating a New Pipeline**:
   - Go to the **Author** tab in ADF and click on **Pipelines**. 
   - Create a new pipeline by clicking the **+** icon and selecting **Pipeline**.
   - Name the pipeline (e.g., `CopyPipeline`).

2. **Adding a Copy Data Activity**:
   - Drag the **Copy Data** activity onto the pipeline workspace.
   - Rename it appropriately (e.g., `Copy Address Table`).

3. **Configuring the Source Dataset**:
   - Click on **Source** and create a new dataset.
   - Select **SQL Server** as the data store type, name it (`Address`), and create a linked service for the connection.
   - In the linked service setup:
     - Select the SHIR created earlier (`SHIR`).
     - Provide the **Server Name** (`localhost`) and **Database Name** (e.g., `MyDatabase`).
     - For authentication, use **SQL Authentication** and retrieve the password securely from Azure Key Vault.

#### Step 4: Configuring Access Permissions for Key Vault

1. **Granting Access**:
   - In your Azure Key Vault, navigate to **Access Policies** and create a new access policy.
   - Assign **Secret** permissions to your Azure Data Factory's managed identity.
   - Select the ADF managed identity as the principal and save the changes.

2. **Testing the Connection**:
   - Return to ADF, refresh the secret dropdown in the linked service configuration, and select the secret containing your SQL Server password.
   - Test and verify that the connection is successful.

#### Step 5: Configuring the Sink Dataset

1. **Setting Up the Sink Dataset**:
   - In the **Sync** tab of the pipeline, create a new dataset for **Azure Data Lake Storage Gen2**.
   - Choose **Parquet** as the file format for efficient columnar storage.
   - Create a linked service for Azure Data Lake, using **Auto-Resolve Integration Runtime** (since it is a cloud-based resource).
   - Select the appropriate subscription and storage account , and test the connection.

2. **Setting the File Path**:
   - Browse and select the **bronze** container in the data lake for storing ingested files.

#### Step 6: Running the Pipeline

1. **Debugging and Testing**:
   - With both the source and sink configured, you can run the pipeline using the **Debug** option.
   - Monitor the pipeline’s progress and verify that the data from the on-premise SQL Server is copied to the bronze layer in the Azure Data Lake.

![image](https://github.com/user-attachments/assets/97b9d059-c95c-4674-b868-d87f9a0a5b60)

![image](https://github.com/user-attachments/assets/c653d0ab-ee67-481c-b95c-faa484aff667)

![image](https://github.com/user-attachments/assets/8e781ce5-a097-4d69-94c2-dc73d335effe)

 ![image](https://github.com/user-attachments/assets/91a72bb6-761e-42ad-88d8-c7f4b1d4696f)

 ![image](https://github.com/user-attachments/assets/98866eea-082b-4eef-9a68-35aa7082c89b)

 ![image](https://github.com/user-attachments/assets/9379d5ed-445d-4d6c-bdb2-4e6d7062b355)

![image](https://github.com/user-attachments/assets/31606ee4-04ff-420c-99c6-fe6562ac276e)

 ![image](https://github.com/user-attachments/assets/0aa71bf9-744c-4ec6-8386-cf49739ccb92)

 ![image](https://github.com/user-attachments/assets/f01f9976-91e4-42d0-9f7a-ff06ae29203c)

 ![image](https://github.com/user-attachments/assets/50a0ab46-7e72-4721-9212-4ad5eb00ec8b)

 ![image](https://github.com/user-attachments/assets/17c502c6-6765-479e-9789-5afd8327e30a)

https://stackoverflow.com/questions/76325987/access-policies-not-available
 
![image](https://github.com/user-attachments/assets/26301518-f477-4dd4-badc-b1f44371cef2)

![image](https://github.com/user-attachments/assets/d4a838a4-6714-4b95-9c8f-7ca7178173a1)

![image](https://github.com/user-attachments/assets/476d76a2-a310-4250-883c-5d75ba59acda)

 
 ![image](https://github.com/user-attachments/assets/19aaf5bd-804c-42bd-8ac8-c3388bc9043e)

![image](https://github.com/user-attachments/assets/c3c47744-a9cf-4d21-9aa6-b76fe0f5d4b6)

 ![image](https://github.com/user-attachments/assets/455e3a69-5215-43ab-8936-9ff362499765)

![image](https://github.com/user-attachments/assets/89a973aa-66f4-42d9-b7d1-132529231573)

![image](https://github.com/user-attachments/assets/92002ee9-dc5f-411f-a90c-661847fa76cd)

 ![image](https://github.com/user-attachments/assets/549972b4-c4b1-437e-b7b3-a9834c2435e8)



## ***Missing jvm.dll in self-hosted integration runtime Windows Container***

![image](https://github.com/user-attachments/assets/f4f53996-4b41-47a7-8c6f-8efbd06c1329)
![image](https://github.com/user-attachments/assets/ad2ef4ce-a25c-442f-b667-4dcfc98af14c)

**Solution-** 
1.	reinstalled latest jave 23 ttps://download.oracle.com/java/23/latest/jdk-23_windows-x64_bin.exe (sha256)
2.	set system and environment variables for JAVA_HOME and path and checked java -version
3.	created all this in regedit
4.	restarted SHIR from services.msc intergarted service and reran the debug in Az df

 
 ![image](https://github.com/user-attachments/assets/80a5082d-9332-47c1-97ac-1ea3c889e98a)

 ![image](https://github.com/user-attachments/assets/33924a81-0d49-473e-8456-a8d330d3c59c)

![image](https://github.com/user-attachments/assets/41873a83-5649-4004-be17-4cf5ba99f6f6)

Here’s a transcript of Part 4 - Data Ingestion (2) of your Azure Data Engineering project:

---

**Part 4 - Data Ingestion (2) Pipeline to Copy all tables into Bronze container**

### Data Ingestion Process

1. **Creating the Pipeline**: 
   - Navigate to the **Author** tab and click on **Pipelines**.
   - Create a new pipeline by clicking the plus icon and selecting **Pipeline**.
   - Name the pipeline "Copy Pipeline."

2. **Copy Data Activity**:
   - Search for "Copy Data Activity" and drag it into the white space.
   - Rename it to "Copy Address Table."

3. **Setting Up Source**:
   - Click on **Source** to create a source dataset for the address table.
   - Click the **New** button and select **SQL Server** as the data store.
   - Name the dataset "Address."
   - Create a link service for the SQL Server database, providing the name "On-Prem SQL Server."

4. **Link Service Configuration**:
   - Select the self-hosted integration runtime "Shir."
   - Enter the server name as "localhost" (checked via SQL Server Management Studio).
   - Enter the database name.
   - For authentication, select SQL and enter the username. Use Azure Key Vault for the password.

5. **Creating Key Vault Link Service**:
   - Create a new link service for Azure Key Vault, selecting the relevant subscription and key vault.
   - Use system-assigned managed identity for authentication.
   - Test the connection, which should succeed.

6. **Access Policies for Key Vault**:
   - Navigate to the key vault and select **Access Policies** to give Azure Data Factory permission to read secrets.
   - Create a new access policy allowing secret permissions and associate it with Azure Data Factory's managed identity.

7. **Testing Key Vault Access**:
   - Refresh the Azure Data Factory and check if the secrets are now accessible.

8. **Completing Link Service Setup**: 
   - After successfully configuring the link service, select the address table from the SQL Server.
   - Preview the data to ensure correct setup.

9. **Configuring Sync**: 
   - Create a new sync dataset for Azure Data Lake Storage Gen2.
   - Select the Parquet format for storing data.

10. **Link Service for Data Lake**:
    - Configure a new link service for Azure Data Lake, selecting the appropriate subscription and storage account.
    - Test the connection, which should also succeed.

11. **File Location**: 
    - Specify the file path in the Azure Data Lake (e.g., "bronze" container).

12. **Running the Pipeline**:
    - Run the pipeline using the debug option to test the copying process.
    - After successful execution, check the Azure Data Lake’s "bronze" container for the copied file (e.g., `salesLT.address.parquet`).

---

Feel free to let me know if you need any adjustments or further details!

![image](https://github.com/user-attachments/assets/6fd02c43-fd33-4f6f-bd02-a1e1d0b2c51d)

![image](https://github.com/user-attachments/assets/035b4a61-740d-427c-bbf4-8deaab00a233)

SELECT
s. name AS SchemaName,
t. name AS TabletName
FROM sys.tables t
INNER JOIN sys.schemas s
ON t.schema_id = s.schema_id
WHERE s. name in ('production', 'sales')

![image](https://github.com/user-attachments/assets/0bb81c5e-921d-4d9b-8cfc-6a59fd8ff498)
 
 ![image](https://github.com/user-attachments/assets/5f504796-f48c-470b-a643-048d453736e0)

 ![image](https://github.com/user-attachments/assets/5989e0a8-7e78-4021-8d5f-def7fe49f86e)

 ![image](https://github.com/user-attachments/assets/884b06cd-2af5-456e-96a6-ec4e102f768e)

![image](https://github.com/user-attachments/assets/76c96758-6ef8-47be-a7d3-a8bb5dd0444d)

Folder Structure
• bronze/Schema/Tablename/Tablename.parquet
bronze/SalesT/Address/ Address.parquet
 
![image](https://github.com/user-attachments/assets/907c92a1-975f-496a-900f-e74a5ebf766c)

![image](https://github.com/user-attachments/assets/ca4a1464-57d0-4688-a70e-fdad066985df)

![image](https://github.com/user-attachments/assets/8abaea12-57d8-45e5-934f-84d1069a0eaa) 
![image](https://github.com/user-attachments/assets/efa4b30c-d98b-4e56-ab9d-654fe192039c)
![image](https://github.com/user-attachments/assets/ec2decd4-37ce-41f6-b7fe-41a17426aab2)


### Part 5 - Data Transformation (1): Mounting the Data Lake

**Introduction to Databricks:**
- We are inside the Azure Resource Group, ready to use Azure Databricks for data transformation.
- Click on Databricks, then click "Launch Workspace" to access the Databricks workspace.

**Overview of the Workspace:**
- The workspace has various tabs:
  - **Workspace Tab**: Used to create notebooks for writing transformation logic.
  - **Repo Tab**: Integrates with Git for version control.
  - **Recent Tab**: Shows recently accessed notebooks.
  - **Data Tab**: Allows creation of databases and tables.
  - **Compute Tab**: Essential for data transformation; we will create a Spark cluster here.
  - **Workflow Tab**: Used to create jobs from notebooks, but we will trigger notebooks using Azure Data Factory instead.

**Creating a Compute Cluster:**
- The compute tab is crucial for creating a Spark cluster to perform transformations.
- Click on "Create Compute" in the top left to start creating the cluster.
- Configure the cluster:
  - Name: Change to a meaningful name, e.g., `Data_Transformation`.
  - Policy: Select the unrestricted option.
  - Node Type: Choose between single or multi-node; select single node for minimal operations.
  - Access Mode: Set to single user since only one user will be using the cluster.
  - Databricks Runtime Version: Choose the default standard option.
  - Node Type: Select the smallest node type, e.g., 4-core CPU.
  - Termination: Set to terminate after 15 minutes of inactivity to save costs.
  - Advanced Options: Enable "Credential Passthrough for User-Level Data Access" for accessing the Data Lake.

**Understanding Credential Passthrough:**
- This option allows Databricks to access the Data Lake using the credentials of the logged-in user.
- Role assignments must be set in Azure Data Lake, allowing users (with Blob Storage Data Contributor role) to interact with it.
- Since Databricks currently cannot be assigned Blob Storage permissions directly, enabling this option is crucial.

**Creating the Cluster:**
- After configuring, click "Create Cluster." It takes about five minutes to set up.

**Mounting the Data Lake:**
- To mount the Data Lake, we need to copy the necessary Python code from the Microsoft documentation.
- Update the code with:
  - Container name: Change `store` to `bronze`.
  - Storage account name: Replace with ``.
  - Mount point name: Use the container name `bronze`.

**Running the Mount Code:**
- After making changes, run the code to mount the `bronze` container.
- Check if the cluster is attached and run the cell.
- The output should be `true`, indicating successful mounting.

**Accessing Data:**
- Use `dbutils.fs.ls` to list data within the `bronze` container.
- Navigate to the Data Lake to verify the presence of the `sales` folder.
- Modify the code to access specific folders within the `bronze` container.

**Mounting Other Containers:**
- Repeat the mounting process for the `silver` and `gold` containers.
- Although the `silver` container might be empty initially, it’s good to mount it from the start.

**Conclusion of Part 5:**
- Now all three containers are mounted: `bronze`, `silver`, and `gold`.
- We can now read data from the `bronze` container, perform transformations, and write the transformed data to the `silver` container.


![image](https://github.com/user-attachments/assets/59d44c4c-929f-4993-bbce-162e754a20fd)

![image](https://github.com/user-attachments/assets/cad015fc-f705-4b38-8c5d-db7d26333d95)
 
![image](https://github.com/user-attachments/assets/d1546852-8aac-49c1-a22b-1f9b966ca12c)

![image](https://github.com/user-attachments/assets/1e868a48-f0e3-4597-9ad4-4c6f9fd04616)

 ![image](https://github.com/user-attachments/assets/5a90dc4e-3047-46cf-bb90-0cc2177fe816)

** Setting Up Azure Databricksc Microsoft doc-**
---
https://learn.microsoft.com/en-us/azure/databricks/archive/credential-passthrough/adls-passthrough#azure-data-lake-storage-gen2

![image](https://github.com/user-attachments/assets/e3d524e7-9042-43df-a493-fd9163108761)

![image](https://github.com/user-attachments/assets/6039705d-c452-4eb6-8eb8-05ea4fdb319d)

 ![image](https://github.com/user-attachments/assets/dee30f70-a1c9-44a2-b9bb-481722193316)

 ![image](https://github.com/user-attachments/assets/2ce2dc54-6331-48b7-a21a-b90b2c154d4d)

 ![image](https://github.com/user-attachments/assets/a8e94056-bac7-4c10-ba5a-8f0f0381467a)

![image](https://github.com/user-attachments/assets/259cb2ad-cb7a-4f54-a206-ec2a9387b361)

---

**Bronze to silver**
 
![image](https://github.com/user-attachments/assets/0d0e8f77-e98d-436c-9a64-ed6d894a626b)

![image](https://github.com/user-attachments/assets/ebae2824-f9fb-4773-b4d0-35a7823f2148)
 
![image](https://github.com/user-attachments/assets/0d0d02e3-c677-46fa-9e38-7ca93a8e8de6)
 
**Silver to gold-**
 
![image](https://github.com/user-attachments/assets/5663df49-36f9-4de4-90ab-287677eb8ef9)

 ![image](https://github.com/user-attachments/assets/d9188a0c-d9ea-4488-b143-ac9b1cbf3302)

![image](https://github.com/user-attachments/assets/50adae6a-e5c1-47cd-b471-89f81b85845b)

 ![image](https://github.com/user-attachments/assets/afad4280-4ab4-41ce-9961-6e7b43694c31)

 ![image](https://github.com/user-attachments/assets/eda492c6-ba3b-4070-902d-8bb21c0e907c)

![image](https://github.com/user-attachments/assets/fe2ca14a-d33c-4ef2-adce-c7500fa9198a)

![image](https://github.com/user-attachments/assets/63e05863-f771-430a-b819-23fe96a446e0)

 ![image](https://github.com/user-attachments/assets/7a5ab4b7-d6ae-414d-a9d8-b48865e9b73e)

![image](https://github.com/user-attachments/assets/e97a407e-ceca-43b8-9686-70158ff0b712)

![image](https://github.com/user-attachments/assets/78472c10-23ed-443e-b9e5-96f2983a4227)
  
![image](https://github.com/user-attachments/assets/b40511e3-761a-4f9b-a6b3-b2e137b48574)

![image](https://github.com/user-attachments/assets/7c2d0d59-72da-4d3a-b9e5-cfe64e4e7261)

![image](https://github.com/user-attachments/assets/60fb591d-11ad-4b9d-918e-10f95b3758f6)
	 
**Silver to Gold-**
 
![image](https://github.com/user-attachments/assets/dee85001-407a-4447-bca0-acb954cf733c)

![image](https://github.com/user-attachments/assets/723daff8-c8c8-4c4f-9ce7-4b1250ed4f5b)

![image](https://github.com/user-attachments/assets/27b9b1fe-e227-46a9-9930-e49dbf38c496)
 ![image](https://github.com/user-attachments/assets/3b0c01dc-34ae-4dd3-8cad-8d4c44403e08)

![image](https://github.com/user-attachments/assets/397018cf-cb05-45ad-afc7-6a0fd87585ab)

![image](https://github.com/user-attachments/assets/a903b0f3-cfda-46ad-8e84-6671386d8140)

![image](https://github.com/user-attachments/assets/6211de35-bb30-484d-ab63-2f2026242f4f)
![image](https://github.com/user-attachments/assets/8535a34b-f753-42fb-9297-62dcf0b5e2f7)

![image](https://github.com/user-attachments/assets/07f98194-ab23-4ff8-9046-c882d71ab0e3)

Part 7 – creating Pipeline

![image](https://github.com/user-attachments/assets/ac6f6a9c-b89e-4cf9-9cad-d3903b28a376)

Integration runtime as Autoresolve cause Databricks is cloud based

![image](https://github.com/user-attachments/assets/b6dce8e9-0f42-4278-a319-9ced837e5b5c)
 

3rd Token- 

![image](https://github.com/user-attachments/assets/ca5d7745-e18a-47d4-bd54-f39cc45e4dc7)
 
![image](https://github.com/user-attachments/assets/89f0d56a-d35d-4b08-a325-d38526b4f94d)

![image](https://github.com/user-attachments/assets/86656c06-b959-4584-b4ca-5672c986a520)


Check how to enable interactive integration runtime to check test connection
https://www.youtube.com/watch?v=lecyAa6Pv8I&t=168s

![image](https://github.com/user-attachments/assets/3da1df9a-9d15-4388-a647-67729f9fe3a1)

![image](https://github.com/user-attachments/assets/4efdb644-ef41-43a7-b5ad-d29263bda0eb)
 
![image](https://github.com/user-attachments/assets/6aceb6ff-7379-4fbe-b73a-4b94db377622)

![image](https://github.com/user-attachments/assets/d1342d40-74a1-40ad-b787-7b8868509cb5)

![image](https://github.com/user-attachments/assets/f55399cb-dc15-4c12-a7a4-f409ada1650f)

![image](https://github.com/user-attachments/assets/7d7369cd-c729-433b-b782-cd1ccd99c948)
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture3.png)

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/7a94f413ee44abdb4abbec8e6db3a029ad8b6470/Assets/Picture1.png)

Power of delta format
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture4.png)

Part 8 - Data Loading (Azure Synapse Analytics)

Azure synapse analytics = azure data factory(it is built on top of AZ DF) + azure data bricks + additional nalytics
Dedicated costly datalake + compute , but serverless = only compute, as we already have data is already exist  in datlake, so servless will be most optimed

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture5.png)

Link tab-

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture6.png) 
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture7.png)
Connect to built-in sereverless

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture8.png) 

Create view-

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture9.png) 

iF any data modification happen in the main table, it going to be automatically reflected in the view as each time view executes the sql for the table
Create view for all the available tables inside the gold container

 ![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture10.png)
 
Also need to create pipeline to create views for the tables to fetch tables
Publish after execution

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture11.png)
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture12.png)
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture13.png)
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture14.png)
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture15.png)
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture16.png)
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture17.png)

This whole list of child items will be passed as input to the for each loop by setting below item config-

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture18.png)
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture19.png)
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture20.png)

 Run instance-
 
 ![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture21.png)
 ![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture22.png)
 
### Data Reporting (Power BI) - Step-by-Step Implementation

This covers the basics for creating and publishing a report using Power BI for the Azure Data Engineering project.

1. **Set Up Data Connection**: 
   - Open Power BI Desktop.
   - Connect to your data source, e.g., Azure SQL Database or Azure Data Lake Storage.
   
2. **Create a Data Model**: 
   - Load tables and establish relationships between them if necessary.
   - Perform data transformations using Power Query Editor.

3. **Design Visuals**: 
   - Add visuals like charts, tables, and maps to visualize key metrics.
   - Use slicers for interactivity.

4. **Create a Dashboard**:
   - Arrange the visuals to form an interactive dashboard layout.
   - Customize the appearance to enhance clarity and aesthetics.

5. **Set Up Filters and Drill-Downs**:
   - Implement filters and drill-down features for detailed analysis.
   - Ensure proper interactivity between visuals.

6. **Publish to Power BI Service**:
   - Save and publish the report to Power BI Service.
   - Configure data refresh settings to keep the dashboard up-to-date.

7. **Share Reports**:
   - Share the dashboard with stakeholders by providing access through Power BI Service.


![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture23.png)
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture24.png)
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture25.png)
 
Report Tab ,Data Tab and Model Tab

 ![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture26.png)

Model Tab with tables replationship-

 ![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture27.png)
 ![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture28.png)
 
Use manage relation and create new relation so that any updates are in sync and reflected overall among all tables, chose the tables from drop dwon and select cardinality and cross filer direction to make reports more interactive
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture29.png)
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture30.png)
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture31.png)
 
Create dashboard-

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture32.png)

Final Dashboard

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture33.png)

---

### Part 10 - Security and Governance (AAD)

In this section, we cover the implementation of security and governance features using Azure Active Directory (AAD). This is a crucial step in setting up access management for the project, ensuring that resources are securely managed and controlled.

#### 1. **Overview of Security and Governance**
   - We previously gone through the use of Azure Key Vault for storing secrets and configuring Azure Data Factory to access these secrets securely.
   - In this section, we will explore how to set up Azure Active Directory (AAD) security groups to manage user permissions efficiently.

#### 2. **Setting Up a Security Group in Azure Active Directory**
   - Access the Azure portal and navigate to **Azure Active Directory**.
   - Go to the **Groups** section to create a new security group that will manage the permissions for the resources used in this project.

#### 3. **Creating a Security Group**
   - Click on the **New Group** button.
   - Choose **Security** as the group type.
   - Enter a suitable name for the group, such as `Data Engineering Team`.
   - Skip the optional description and proceed to the owner assignment.

#### 4. **Assigning Owners to the Group**
   - Add an owner to the security group. This owner will have management rights to add or remove users and manage the group’s settings.
   - For example, the project lead or a senior data engineer can be assigned as the group owner.

#### 5. **Adding Members to the Group**
   - Add all relevant team members who need access to the resources under this resource group. By managing team members through this security group, we simplify permission management.
   - When a new team member joins, they can simply be added to the security group, automatically inheriting all the necessary permissions.

#### 6. **Assigning Permissions to the Security Group**
   - Navigate to the target **Resource Group** (e.g., `RG_Data_Engineering_Project`).
   - Go to **Access Control (IAM)** and click **Add** -> **Add Role Assignment**.
   - Choose the appropriate role, such as **Contributor**, and assign it to the security group created (`Data Engineering Team`).

#### 7. **Testing the Security Group Configuration**
   - Verify that members of the security group can access and manage the resources in the Resource Group.
   - For example, logging in as a member of the security group should show access to the Resource Group and its associated resources.

#### 8. **Best Practices for Security Management**
   - Always manage access through security groups rather than individual accounts to maintain efficient and scalable user management.
   - Regularly review and update security groups to ensure that permissions align with the current project requirements.

By implementing security and governance using Azure Active Directory, we ensure that access management for the project is both secure and efficient, allowing for easy scalability and control.

--- 




Initially only account of mine which is owner has access -

 ![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture34.png)
 
**So in order to provide access a security group so that all employee/developers/data engineers belonging to that group get access**

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture35.png)

**If it is inaccessible, then you’re owner only, and need to contact your azure admin**

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture36.png)

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture37.png)

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture38.png)

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture39.png)


the final part of this Azure Data Engineering project! In this section, we’ll test the entire data pipeline to ensure everything works seamlessly, just as it would in a real-world scenario. Let's walk through the process step by step.

### 1. Overview of the Pipeline Testing Process
- We have completed building all components of our data engineering architecture. Now, we test the pipeline end-to-end.
- The test scenario involves adding a new row to the on-premise SQL Server database and running the Azure Data Factory (ADF) pipeline to verify that the changes are reflected through every stage until the data is visible in Power BI.

### 2. Triggering the Azure Data Factory Pipeline
- First, let's go into the ADF portal and locate the pipeline we created (named **Copy All Tables**). 
- Remember, when we initially ran the pipeline, we used the **Debug Mode** and **Trigger Now** options. These options manually trigger the pipeline without publishing changes. For real-time projects, we need to automate this process using triggers.

### 3. Creating a Trigger
- We will configure a **Schedule Trigger** in ADF to automate the pipeline.
- Click on **Add Trigger** → **New/Edit**. 
- In the **Choose Trigger** dropdown, click **New**.
  - **Name**: Set it to `scheduled_trigger`.
  - **Description**: (Optional) You can leave this blank for simplicity.
  - **Type**: Select **Schedule**.
  - **Start Date**: This will default to the current date and time, which is fine for our test.
  - **Time Zone**: Set the appropriate time zone for your region (e.g., Auckland, New Zealand, if that's your location).
  - **Recurrence**: Configure the pipeline to run every day. Change the interval from minutes to days and set the frequency to `1` (daily).
  - **Execution Time**: Specify the time for the pipeline to run. For instance, if the current time is `12:09 AM`, set it to `12:25 AM`.
  - **End Date**: Leave it blank if you want the trigger to run indefinitely.
- Click **OK** and publish the trigger.

### 4. Testing the Trigger
- Once the trigger is set, go to the SQL Server database (using SQL Server Management Studio - SSMS). 
- Add a new record to one of the tables, such as the **Customer** table, which we used to create visuals in Power BI. 
  - For instance, add two new customer records. 
- The trigger will activate the pipeline at the configured time (e.g., `12:25 AM`).

### 5. Monitoring the Pipeline Run
- Go back to the **Monitor** tab in ADF to verify if the pipeline has triggered automatically.
- Once the time hits `12:25 AM`, refresh the monitor page to see the pipeline status.
  - The pipeline should show **In Progress** initially and eventually switch to **Succeeded** once it completes all activities.

### 6. Verifying Data Update in Power BI
- Open your Power BI report, which is linked to the Azure Synapse Analytics database.
- We expect the new customer records added earlier to appear in the report. Initially, the **Total Customers** card might show `849`.
- Refresh the Power BI report to pull the latest data from Synapse Analytics. The total count should update to `851`, reflecting the newly added rows.

### 7. Conclusion
- The successful update in Power BI confirms that our end-to-end data pipeline works correctly. The new data flows seamlessly from the on-premise SQL Server through Azure Data Lake, Azure Databricks transformations, Azure Synapse Analytics, and finally into Power BI.
- This end-to-end test validates all the components and their integration, ensuring the entire architecture functions as intended.

Congratulations on completing this project! This pipeline forms a solid foundation for most real-time data engineering scenarios. I hope you found this project useful and gained valuable insights into building scalable data solutions using Azure.

---

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture40.png)
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture41.png)
Make changes in the source- add 2 new rows in the table-

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture42.png) 

**Dashboard data values before trigger execution-**

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture43.png) 

**Updated Dasboard After execution-**

![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture44.png) 
![image](https://github.com/ankan-mazumdar/End-to-End-Azure-Data-Engineering-Project/blob/main/Assets/Picture45.png)

