# azure-data-ingestion-pipeline
Automated Azure Data Factory pipeline to ingest, transform, and load daily CSV files from Data Lake to SQL Database.

[![Azure](https://img.shields.io/badge/Azure-Data%20Factory-blue?logo=microsoft-azure)](https://azure.microsoft.com/en-us/services/data-factory/)
[![SQL Database](https://img.shields.io/badge/Azure-SQL%20Database-red?logo=microsoft-sql-server)](https://azure.microsoft.com/en-us/services/sql-database/)
[![Data Lake](https://img.shields.io/badge/Azure-Data%20Lake%20Gen2-green?logo=microsoft-azure)](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A robust, scalable, and fully automated ETL pipeline built on Microsoft Azure services for daily data ingestion from multiple CSV sources into Azure SQL Database.

# Azure Data Ingestion Pipeline

## 🎯 Project Overview
This repository contains the complete implementation of an automated daily data ingestion pipeline using Microsoft Azure services. The solution orchestrates the ingestion of three distinct types of CSV files from Azure Data Lake into a structured Azure SQL Database.

---

## 📂 Repository Structure

### 🏗️ Infrastructure Files
| File | Description |
|------|-------------|
| `ARMTemplateForFactory.json` | Azure Resource Manager template for Data Factory deployment |
| `ARMTemplateParametersForFactory.json` | Configuration parameters for ARM template |

### 📊 Sample Data Files
| File | Format | Transformations Applied |
|------|--------|------------------------|
| `CUST_MSTR_20191112.csv` | `CUST_MSTR_YYYYMMDD.csv` | ✅ Date column extracted: `2019-11-12` |
| `master_child_export-20191112.csv` | `master_child_export-YYYYMMDD.csv` | ✅ Date: `2019-11-12`<br>✅ DateKey: `20191112` |
| `H_ECOM_ORDER.csv` | `H_ECOM_ORDER.csv` | ❌ No transformations (loaded as-is) |

### 📋 Documentation
| File | Content |
|------|---------|
| `Assignment_report.docx` | Complete technical documentation, architecture design, and implementation details |

---

## 🚀 Quick Deployment

### Step 1: Deploy Infrastructure
```bash
az deployment group create \
  --resource-group <your-resource-group> \
  --template-file ARMTemplateForFactory.json \
  --parameters @ARMTemplateParametersForFactory.json
```

### Step 2: Upload Sample Data
```bash
# Upload CSV files to your Azure Data Lake Storage container
# Ensure naming conventions match the patterns above
```

### Step 3: Configure Pipeline
1. Import Data Factory pipeline from ARM template
2. Update connection strings and storage details
3. Activate the daily trigger schedule

---

## 🔧 Pipeline Features

- **📅 Daily Automation:** Scheduled execution via Azure Data Factory triggers
- **🔄 Truncate & Load:** Fresh data load pattern with table clearing
- **📝 Dynamic Transformations:** Filename-based date extraction
- **⚡ Parallel Processing:** Concurrent handling of all file types
- **📊 Three Data Sources:** Customer, master-child, and e-commerce order data

---

## 📖 Technical Documentation

For comprehensive technical details, refer to **`Assignment_report.docx`



## 🏗️ Architecture Overview

```
Azure Data Lake Storage Gen2 → Azure Data Factory → Azure SQL Database
```

**Data Flow:**
- **Source**: CSV files stored in Azure Data Lake Storage Gen2
- **Processing**: Azure Data Factory orchestrates ETL operations
- **Destination**: Structured data in Azure SQL Database
- **Schedule**: Automated daily execution

## 🎯 Project Objectives

- **Automate** daily data ingestion from multiple CSV sources
- **Eliminate** manual data processing workflows
- **Ensure** consistent data freshness for analytics and reporting
- **Implement** scalable cloud-native architecture
- **Provide** reliable error handling and monitoring

## 📊 Data Sources & Processing

| File Type | Pattern | Transformations | Target Table |
|-----------|---------|-----------------|--------------|
| Customer Master | `CUST_MSTR_*.csv` | Extract date from filename → `YYYY-MM-DD` | `CUST_MSTR` |
| Child Export | `master_child_export-*.csv` | Extract date + DateKey from filename | `master_child` |
| E-commerce Orders | `H_ECOM_ORDER.csv` | No transformation | `H_ECOM_Orders` |



✨ Visualizing the Pipeline
Here is a visual representation of the pipeline's structure and a successful execution run.

1. Pipeline Canvas View
(This shows the complete pipeline with its three parallel branches for processing each file type.)

<img width="1664" height="829" alt="Screenshot 2025-07-20 at 11 11 07 PM" src="https://github.com/user-attachments/assets/404ed225-d652-4c4d-8b54-bc6d272c3443" />



2. Successful Pipeline Run
(This demonstrates a successful debug or triggered run from the Azure Data Factory monitoring view.)



<img width="1658" height="770" alt="Screenshot 2025-07-20 at 11 11 58 PM" src="https://github.com/user-attachments/assets/8ea95e65-6d60-4a99-93e8-f6784486a830" />




## 🚀 Key Features

- ✅ **Fully Automated**: Zero manual intervention required
- ✅ **Parallel Processing**: Concurrent handling of multiple file types
- ✅ **Dynamic Discovery**: Automatic file detection and processing
- ✅ **Smart Transformations**: Intelligent date extraction from filenames
- ✅ **Truncate & Load**: Ensures data consistency with daily refresh
- ✅ **Error Handling**: Robust failure detection and recovery
- ✅ **Monitoring**: Comprehensive logging and alerting

## 🛠️ Technology Stack

- **Azure Data Factory**: ETL orchestration and pipeline management
- **Azure Data Lake Storage Gen2**: Scalable data lake for raw files
- **Azure SQL Database**: High-performance relational database
- **Azure Monitor**: Pipeline monitoring and alerting
- **Scheduled Triggers**: Automated daily execution

## 📋 Prerequisites

- Azure Subscription with appropriate permissions
- Azure Data Factory instance
- Azure Data Lake Storage Gen2 account
- Azure SQL Database with target tables created
- Service connections configured between services

## 🔧 Setup Instructions

### 1. Clone Repository
```bash
git clone https://github.com/yourusername/azure-data-pipeline.git
cd azure-data-pipeline
```

### 2. Azure Resource Setup
```bash
# Create Resource Group
az group create --name rg-data-pipeline --location eastus

# Create Storage Account for Data Lake
az storage account create \
    --name yourstorageaccount \
    --resource-group rg-data-pipeline \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2 \
    --hierarchical-namespace true

# Create Azure SQL Database
az sql server create \
    --name yoursqlserver \
    --resource-group rg-data-pipeline \
    --location eastus \
    --admin-user sqladmin \
    --admin-password YourPassword123!

az sql db create \
    --resource-group rg-data-pipeline \
    --server yoursqlserver \
    --name DataWarehouse \
    --service-objective Basic
```

### 3. Data Factory Pipeline Deployment
1. Import the pipeline JSON files from `/pipeline-definitions/`
2. Configure linked services for Data Lake and SQL Database
3. Update connection strings and credentials
4. Deploy and test the pipeline

### 4. Database Schema Setup
```sql
-- Run the scripts in /sql-scripts/ to create target tables:
-- - create-tables.sql
-- - configure-permissions.sql
```

## 🔄 Pipeline Workflow

### Master Pipeline: `Daily_File_Load`

1. **File Discovery**
   - Scans Data Lake container for new files
   - Generates file inventory for processing

2. **Parallel Processing** (3 concurrent branches)
   - `CUST_MSTR` files → Filter → Truncate → Process
   - `master_child_export` files → Filter → Truncate → Process  
   - `H_ECOM_ORDER` files → Filter → Truncate → Process

3. **Data Transformation**
   - Dynamic filename-based date extraction
   - Column mapping and data type conversion
   - Quality validation and error handling

4. **Data Loading**
   - Truncate target tables
   - Load transformed data
   - Verify successful completion

## 📁 Repository Structure

```
├── README.md
├── docs/
│   ├── PROJECT_REPORT.md
│   ├── ARCHITECTURE.md
│   └── TROUBLESHOOTING.md
├── pipeline-definitions/
│   ├── Daily_File_Load.json
│   ├── linked-services/
│   └── datasets/
├── sql-scripts/
│   ├── create-tables.sql
│   ├── configure-permissions.sql
│   └── sample-queries.sql
├── monitoring/
│   ├── alerts.json
│   └── dashboard-config.json
├── sample-data/
│   ├── CUST_MSTR_20250720.csv
│   ├── master_child_export-20250720.csv
│   └── H_ECOM_ORDER.csv
└── scripts/
    ├── deploy.ps1
    └── validate.ps1
```

## 📈 Performance & Monitoring

### Key Metrics
- **Processing Time**: < 30 minutes for typical daily volumes 
- **Resource Utilization**: Optimized for cost-effective serverless execution

### Monitoring Dashboard
Access real-time pipeline metrics through:
- Azure Data Factory monitoring portal
- Custom Azure Monitor dashboards
- Automated email alerts for failures

## 🔧 Configuration

### Environment Variables
Update the following in your Data Factory linked services:
```json
{
  "storageAccountName": "yourstorageaccount",
  "containerName": "source-files",
  "sqlServerName": "yoursqlserver.database.windows.net",
  "databaseName": "DataWarehouse"
}
```

### File Naming Conventions
Ensure source files follow these patterns:
- Customer Master: `CUST_MSTR_YYYYMMDD.csv`
- Child Export: `master_child_export-YYYYMMDD.csv`
- E-commerce: `H_ECOM_ORDER.csv`

For detailed troubleshooting, see [TROUBLESHOOTING.md](docs/TROUBLESHOOTING.md)

## 📚 Documentation

- **[Project Report](docs/PROJECT_REPORT.md)**: Comprehensive project documentation
- **[Architecture Guide](docs/ARCHITECTURE.md)**: Detailed technical architecture
- **[API Reference](docs/API_REFERENCE.md)**: Data Factory pipeline specifications

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/enhancement`)
3. Commit changes (`git commit -am 'Add new feature'`)
4. Push to branch (`git push origin feature/enhancement`)
5. Create Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.




**Built with ❤️ using Microsoft Azure**
