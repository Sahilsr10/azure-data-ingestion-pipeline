# azure-data-ingestion-pipeline
Automated Azure Data Factory pipeline to ingest, transform, and load daily CSV files from Data Lake to SQL Database.

[![Azure](https://img.shields.io/badge/Azure-Data%20Factory-blue?logo=microsoft-azure)](https://azure.microsoft.com/en-us/services/data-factory/)
[![SQL Database](https://img.shields.io/badge/Azure-SQL%20Database-red?logo=microsoft-sql-server)](https://azure.microsoft.com/en-us/services/sql-database/)
[![Data Lake](https://img.shields.io/badge/Azure-Data%20Lake%20Gen2-green?logo=microsoft-azure)](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A robust, scalable, and fully automated ETL pipeline built on Microsoft Azure services for daily data ingestion from multiple CSV sources into Azure SQL Database.

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
- **Success Rate**: 99%+ pipeline execution success
- **Data Freshness**: Daily updates guaranteed by 6 AM
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

## 🚨 Troubleshooting

### Common Issues

**Pipeline Fails to Start**
- Verify trigger is enabled and properly configured
- Check linked service connections
- Confirm file permissions in Data Lake

**Transformation Errors**
- Validate filename patterns match expected format
- Check for special characters in filenames
- Verify CSV file structure matches expected schema

**SQL Loading Failures**
- Confirm target tables exist with correct schema
- Check SQL Database connection and credentials
- Verify sufficient database storage space

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

## 🏆 Acknowledgments

- Microsoft Azure Data Factory team for excellent documentation
- Azure community for best practices and examples
- Project stakeholders for requirements and testing support

## 📞 Support

For questions or support:
- 📧 Email: [your-email@company.com]
- 📖 Documentation: [Internal Wiki Link]
- 🐛 Issues: [GitHub Issues](https://github.com/yourusername/azure-data-pipeline/issues)

---

**Built with ❤️ using Microsoft Azure**
