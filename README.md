# Formula 1 Analytics Warehouse with dbt & Azure Synapse

![dbt](https://img.shields.io/badge/dbt-1.8.2-FF694B?logo=dbt&logoColor=white)
![Azure Synapse](https://img.shields.io/badge/Azure_Synapse-Serverless-0078D4?logo=microsoftazure&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-T--SQL-blue)
![Python](https://img.shields.io/badge/Python-3.10-3776AB?logo=python&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/CI/CD-GitHub_Actions-2088FF?logo=githubactions&logoColor=white)

This project demonstrates a complete analytics engineering workflow, transforming raw, normalized Formula 1 data into a performance-optimized star schema using dbt Core and Azure Synapse Analytics. It serves as a robust, tested, and documented single source of truth for any F1-related data analysis.

---

## 🚀 Key Features & Skills Demonstrated

* **Modern Data Stack Implementation:** Hands-on experience with a cloud-native data stack, integrating dbt Core with Azure Synapse and Azure Blob Storage.
* **Advanced Data Modeling:** Designed and built a Kimball-style **star schema** from a highly normalized source, creating clean fact and dimension tables optimized for analytical querying and BI tools.
* **Data Quality & Testing:** Implemented a comprehensive data testing suite with over 50 tests (`unique`, `not_null`, `relationships`) to ensure data integrity, accuracy, and reliability.
* **ELT Best Practices:** Followed a structured, multi-layered approach (`Sources` → `Staging` → `Marts`) to ensure modularity, reusability, and maintainability of the transformation logic.
* **Version Control & CI/CD:** Managed the project with Git and set up a **GitHub Actions** workflow to automatically run, build, and test the dbt models on every push to the main branch, ensuring code quality and automating deployments.
* **Documentation as Code:** Leveraged dbt's built-in documentation features to generate a complete, interactive data catalog with model descriptions, column definitions, and a full dependency lineage graph (DAG).
* **SQL & Jinja Mastery:** Wrote clean, modular, and reusable SQL, enhanced with dbt's Jinja templating and macros to abstract logic and reduce code duplication.

## ⚙️ Tech Stack

| Component             | Technology                                                                                                                                                             | Purpose                                                                                                                            |
| --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| **Data Transformation** | [**dbt Core**](https://www.getdbt.com/)                                                                                                                                | Orchestrating SQL transformations, managing dependencies, testing, and documentation.                                              |
| **Data Warehouse** | [**Azure Synapse Analytics**](https://azure.microsoft.com/en-us/products/synapse-analytics) (Serverless SQL Pool)                                                        | The scalable, pay-per-query data warehouse where data is transformed and stored.                                                   |
| **Data Lake Storage** | [**Azure Blob Storage**](https://azure.microsoft.com/en-us/products/storage/blobs)                                                                                     | Staging area for the raw source CSV files, acting as the data lake foundation.                                                     |
| **CI/CD** | [**GitHub Actions**](https://github.com/features/actions)                                                                                                              | Automated pipeline to build and test the dbt project on every code change.                                                         |
| **Languages & Tools** | **SQL**, **Python**, **Git**, **YAML** | Core languages and tools for data transformation, scripting, and project configuration.                                            |
| **BI / Visualization** | *(Ready for)* **Power BI**, **Tableau**, etc.                                                                                                                          | The star schema is optimized for direct connection to any major BI tool for creating dashboards and performing ad-hoc analysis. |

---

## 🏛️ Architecture & Data Flow

This project follows a modern ELT (Extract, Load, Transform) paradigm.

1.  **Extract & Load:** Raw Formula 1 data (CSVs) is uploaded to a container in **Azure Blob Storage**. This acts as the landing zone and data lake. Azure Synapse's Serverless SQL pool can read this data directly without a separate ingestion step.
2.  **Transform:** **dbt** connects to the Synapse workspace and orchestrates all transformations directly within the data warehouse using a multi-layered approach:
    * **Sources:** dbt sources are defined in `sources.yml` to declare the raw data tables in Azure Blob Storage.
    * **Staging Layer:** Raw data is cleaned, columns are renamed (e.g., `camelCase` to `snake_case`), data types are cast, and basic transformations are applied. These models have a 1-to-1 relationship with the source tables.
    * **Marts Layer:** The clean staging models are joined and aggregated to build the final star schema, consisting of dimension tables (e.g., `dim_drivers`, `dim_races`) and a central fact table (`fct_results`).

*A simplified view of the final star schema, optimized for analytics.*

## 📈 Data Lineage

The entire transformation process is captured in the dbt Directed Acyclic Graph (DAG), which provides a clear and interactive view of how data flows from source to final models.

*This DAG is automatically generated by dbt and shows the dependencies between all models.*

---

## 🛠️ Setup & Usage

To run this project locally, you will need Python, dbt, and an Azure account.

### **Prerequisites**

* Python 3.9+ and pip
* Git
* An Azure account with permissions to create a Synapse Workspace and Storage Account.

### **1. Clone the Repository**

```bash
git clone [https://github.com/yourusername/dbt-formula1-analytics-azure.git](https://github.com/yourusername/dbt-formula1-analytics-azure.git)
cd dbt-formula1-analytics-azure
```

### **2. Set up a Virtual Environment & Install Dependencies**

```bash
# Create a virtual environment
python -m venv venv

# Activate it (Windows)
.\venv\Scripts\activate
# Activate it (Mac/Linux)
source venv/bin/activate

# Install dbt and the Synapse adapter
pip install dbt-core dbt-synapse
```

### **3. Configure Your dbt Profile**

dbt requires a `profiles.yml` file to connect to your data warehouse. This file should be placed in `~/.dbt/` (Mac/Linux) or `C:\Users\<your_user>\.dbt\` (Windows).

**Important:** This file contains credentials and should **never** be committed to version control.

**`profiles.yml`:**

```yaml
f1_analytics:
  target: dev
  outputs:
    dev:
      type: synapse
      driver: 'ODBC Driver 18 for SQL Server'
      server: <your-synapse-workspace-name>.sql.azuresynapse.net
      port: 1433
      database: f1db # Or your chosen database name
      schema: dbt_dev
      user: sqladminuser
      password: "{{ env_var('DBT_AZURE_PASSWORD') }}" # Use an environment variable for safety
      authentication: SqlPassword
      encrypt: yes
      trust_cert: yes
```

Set the password as an environment variable in your terminal before running dbt:

```bash
# Windows (PowerShell)
$env:DBT_AZURE_PASSWORD="your-sql-admin-password"

# Mac/Linux
export DBT_AZURE_PASSWORD="your-sql-admin-password"
```

### **4. Run the dbt Project**

```bash
# Test the connection to Azure Synapse
dbt debug

# Build all models, run all tests, and create snapshots
dbt build

# (Optional) Run only models
dbt run

# (Optional) Run only tests
dbt test
```

### **5. Explore the Documentation**

```bash
# Generate the documentation site
dbt docs generate

# Serve the site locally at http://localhost:8080
dbt docs serve
