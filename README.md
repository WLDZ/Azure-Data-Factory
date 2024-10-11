# Data Ingestion and Storage Solution Using Azure Data Factory and Azure Data Lake

## Overview
This repository outlines the problem we will solve and presents the corresponding solution in the **`solution.md`** file. The solution involves ingesting data from multiple sources (files and REST APIs), processing it using **Azure Data Factory (ADF)**, and storing the processed data in **Azure Data Lake Storage (ADLS)**. It is designed to be flexible, secure, and scalable, incorporating error handling, notifications through **Azure Logic Apps**, and automated deployment via a **CI/CD pipeline**.


## Table of Contents

- [Architecture Overview](#architecture-overview)
- [Prerequisites](#prerequisites)
- [Setup Guide](#setup-guide)
  - [Azure Data Lake Storage](#azure-data-lake-storage)
  - [Azure Data Factory](#azure-data-factory)
  - [Key Vault and Managed Identity](#key-vault-and-managed-identity)
  - [CI/CD Pipeline](#ci-cd-pipeline)
- [Configuration](#configuration)
- [Usage](#usage)
- [Security](#security)
- [Error Handling and Notifications](#error-handling-and-notifications)
- [Contributing](#contributing)
- [License](#license)

## Architecture Overview

The solution ingests data from the following sources:
1. **Lego Catalog Files**: CSV files containing Lego product data, which are updated daily. These files are downloaded from an external source and ingested into **Azure Data Lake Storage** (ADLS).
2. **Users API**: REST API that provides user-specific data about Lego sets and parts. This data is fetched using API requests and stored in **ADLS**.

The data is organized in a hierarchical structure in ADLS based on **Year/Month/Day** to ensure data is easily retrievable and manageable. **Azure Data Factory (ADF)** orchestrates the entire ETL (Extract, Transform, Load) process, including error handling and notifications.

### Additional Features:
- **Error Handling**: Managed through a **Failure Pipeline** that triggers upon activity failures. Additionally, notifications are sent to the admin through a **Logic App**.
- **CI/CD Pipeline**: Automates deployment across Dev and Prod environments using **Azure DevOps**.

## Prerequisites

Before running the solution, ensure you have the following:
- An **Azure subscription** with permissions to create **Azure Data Lake Storage**, **Azure Data Factory**, and **Azure Key Vault**.
- **Azure CLI** installed (optional but recommended).
- **Azure DevOps** or any Git-based repository for storing your ADF pipeline code.
- Basic understanding of **Azure Data Factory**, **Azure Data Lake Storage**, and **Azure Key Vault**.

## Setup Guide

### Azure Data Lake Storage

1. **Provision a Storage Account**:
   - Create a **Storage Account** in the Azure portal, choosing **Data Lake Storage Gen2** as the storage type.
   - Select **Locally Redundant Storage (LRS)** for development and **Geo-Redundant Storage (GRS)** for production.

2. **Create Containers**:
   - Create a root container in ADLS for data ingestion.
   - Organize data into folders based on the source (e.g., `Lego`, `Users`) and by date (`year/month/day`) to avoid data swamp.

### Azure Data Factory

1. **Create an Azure Data Factory Instance**:
   - Provision a new ADF instance in the Azure portal. Choose a region and provide a unique name for the instance.

2. **Create Pipelines for Data Ingestion**:
   - **Lego Files Pipeline**: This pipeline will download LEGO zip files from the provided URL and store them in ADLS.
   - **User Data API Pipeline**: Set up a pipeline to call the **Rebrickable API**, retrieve user-specific data, and store it in ADLS.

3. **Parameterization**:
   - Use parameters for dynamic handling of different datasets (file paths, API keys) to make the pipeline reusable and flexible.

4. **Error Handling**:
   - Set up error-handling mechanisms using **retry policies** and **error notifications** via **Logic Apps** or **ADF Web Activity** to notify the admin on failures.

### Key Vault and Managed Identity

1. **Azure Key Vault**:
   - Store sensitive credentials (API keys, usernames, passwords) securely in **Azure Key Vault**.
   - Link the Key Vault to your ADF pipelines to retrieve secrets securely at runtime.

2. **Managed Identity**:
   - Enable **Managed Identity** for ADF to securely access Azure resources, such as Key Vault and ADLS, without hardcoding credentials.

### CI/CD Pipeline

1. **Source Control Integration**:
   - Use a **Git-based repository** (e.g., **Azure DevOps**) to store ADF pipelines and configurations.

2. **CI/CD Pipeline**:
   - Set up an **Azure DevOps Pipeline** for continuous integration and deployment of ADF pipelines to both development and production environments. Ensure that environment-specific configurations are managed using parameterization or configuration files.

## Configuration

- **Configuration Files**: Store environment-specific configuration details (e.g., file paths, API URLs, credentials) in JSON or YAML files or through services like **Azure App Configuration**.
- **Parameterization**: Ensure that file paths, data sources, and other dynamic values are parameterized within the pipeline.

## Usage

1. **Pipeline Execution**:
   - Trigger pipelines manually via the Azure Data Factory interface or schedule them to run automatically (e.g., daily at 8:00 AM).
   
2. **Monitoring**:
   - Use **Azure Monitor** and **Log Analytics** to monitor the health and performance of the pipeline. Set up alerts to notify the admin of failures or performance issues.

## Security

- **Access Control**:
   - Use **Azure RBAC** to manage access to resources like ADLS, ADF, and Key Vault.
   - Ensure that **ACLs** are configured correctly on ADLS to protect sensitive data.

- **Data Encryption**:
   - Ensure that all data is encrypted both at rest and in transit. ADLS provides default encryption for data at rest.

## Error Handling and Notifications

- **Error Handling**:
   - If an activity fails during pipeline execution, the **Failure Pipeline** is triggered to handle errors, log failures, and initiate notifications.
   - Use **retry policies** in ADF to automatically attempt failed operations.

- **Notifications**:
   - A **Logic App** is integrated into the pipeline to send custom error notifications to the admin in case of failures. The message includes details such as the failed activity, error message, and timestamp.


 ## Solution
[Solution](Solution.md)

