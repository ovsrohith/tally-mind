That's a very detailed configuration guide\! I'll integrate the server start-up steps and create a complete, cohesive guide for setting up the **Tally Database Loader**.

Since the second part of your input uses **MySQL** for the installation steps, I'll prioritize that database technology for the complete setup guide, while retaining the initial **MYSQL** and general Tally configuration details for completeness.

-----

# 🚀 Tally Database Loader: Full Configuration & Setup Guide

This comprehensive guide covers database and Tally connection configuration, along with the complete steps for setting up and running the data synchronization utility.

-----

## ⚙️ Configuration Guide

This section explains how to configure the database and Tally connection settings for smooth data synchronization and integration.

### 🗄️ Database Configuration Examples

Below is a sample configuration block for connecting to a **MYSQL** database:

```json
"database": {
    "technology": "mysql",
    "server": "localhost",
    "port": 1433,
    "ssl": false,
    "schema": "<database_name>",
    "username": "sa",
    "password": "<your_password>",
    "loadmethod": "insert"
}
```

#### 🔧 Database Settings Description

| Setting | Description |
| :--- | :--- |
| **technology** | Database technology used. Supported values: **mssql**, **mysql**, **postgres**, **bigquery**, **adls**, **json**, **csv**. |
| **server** | **IP address or hostname** of the database server. `localhost` = same machine. |
| **port** | Port number of the database service. Defaults: **MSSQL → 1433**, **MySQL → 3306**, **PostgreSQL → 5432**. |
| **ssl** | Enables secure connection. `true` = Use SSL (for cloud DBs); `false` = No SSL (for local or LAN DBs). *(Supported for MSSQL/Postgres only)*. |
| **schema** | **Name of the database** where data should be inserted. |
| **username** | Database username. Defaults: **MSSQL → sa**, **MySQL → root**, **PostgreSQL → postgres**. |
| **password** | Password for the database user. *(Trusted login is not supported.)* |
| **loadmethod** | Data loading method. **insert**: Uses SQL `INSERT` queries (most compatible, slower); **file**: Uses file-based loading (faster, but works only when utility and DB server are on the same machine). |

-----

### 📦 Tally Configuration Examples

#### 🔁 Full Sync Example

```json
"tally": {
    "definition": "tally-export-config.yaml",
    "server": "localhost",
    "port": 9000,
    "fromdate": "20230401",
    "todate": "20240331",
    "sync": "full",
    "frequency": 0,
    "company": ""
}
```

#### ⚡ Incremental Sync Example

```json
"tally": {
    "definition": "tally-export-config-incremental.yaml",
    "server": "localhost",
    "port": 9000,
    "fromdate": "auto",
    "todate": "auto",
    "sync": "incremental",
    "frequency": 5,
    "company": ""
}
```

#### 📘 Tally Settings Description

| Setting | Description |
| :--- | :--- |
| **definition** | Name of the export configuration file inside the utility folder. |
| **server** | **IP address or hostname** where **Tally XML Server** is running. |
| **port** | **Tally XML Server port**. Default: **9000**. |
| **fromdate / todate** | **Date range for export**. Format: `YYYYMMDD`. `auto` = detect first & last transaction date. |
| **sync** | **Synchronization mode**. **full**: Export all data (default); **incremental**: Export only modified or new data since last sync. |
| **frequency** | **Frequency (in minutes)** to poll Tally for changes. `0` = one-time sync and exit. |
| **company** | **Name of the Tally company** to export from. Leave blank to use the currently active company. |

-----

## ▶️ Setup and Execution Steps (Using MySQL)

Follow these steps to set up the environment, configure the MySQL database, and run the Tally Data Loader.

### 1\. Clone the Repository and Navigate

```bash
git clone <REPO>
cd <FOLDE>
```

### 2\. Install Dependencies

Use npm to install the necessary project dependencies:

```bash
npm install
```

### 3\. Create Database and Tables (MySQL)

You need to create the target database and load the schema.

  * Log in to your MySQL server:
    ```bash
    mysql -u root -p
    ```
  * Create a new database (e.g., `tally_loader`) and select it:
    ```sql
    CREATE DATABASE <NAME U HAVE GIVEN IN COFIG>;
    USE <DATABASE>;
    ```
  * Import the provided SQL schema file to create all necessary tables (like `ledger`, `voucher`, `company`, etc.):
    ```bash
    mysql -u root -p <DATABASE> < database-structure.sql
    ```

### 4\. Configure `config.json`

Open the `config.json` file and edit the settings to match your environment, specifically for **MySQL** and your **Tally** instance.

```json
{
  "database": {
    "type": "mysql",
    "host": "localhost",
    "port": 3306,
    "user": "root",
    "password": "your_mysql_password", 
    "database": "<DATABASE>"
  },
  "tally": {
    "host": "localhost",
    "port": 9000,
    "company": "My Company Name", 
    "fromDate": "2024-04-01",
    "toDate": "2025-03-31",
    "mode": "full"
  }
}
```

> **📝 Note:**
>
>   * Ensure the **Tally company name** matches exactly as it appears in Tally.
>   * The default Tally XML server port is **9000**.

### 5\. Run the Server

Execute the main script to start the synchronization process:

```bash
node dist/index.mjs
```

#### 🧭 Expected Outcome

If the configuration is correct, you should see logs indicating successful connection and data loading:

```
Connecting to Tally at localhost:9000
Connected to MySQL database tally_loader
Loading masters...
Loading vouchers...
Import completed successfully
```

-----
