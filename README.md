# 🏦 Startup Financial Model

## Overview

The HeySalad Financial Model is a Python-based financial analysis tool that evaluates startup investment viability by analyzing projected revenue growth, unit economics, and key financial metrics. The model takes input data from startups, processes it, and generates insights into their growth potential.

## Features

- CAGR Calculation: Computes the Compound Annual Growth Rate (CAGR) to estimate startup revenue growth over a period of five years.

- Unit Economics Evaluation: Assesses the startup's financial health by calculating the LTV-to-CAC ratio and Gross Margin.

- Investment Viability Assessment: Determines whether a startup is a good investment based on its growth potential and financial health.

- Excel Integration: Reads startup data from an Excel file (```startup_data.xlsx```) and writes results to another Excel file (``` startup_results.xlsx```).

## How It Works

- ### Data Input: The model reads startup data from an Excel file (```startup_data.xlsx```).

- ### Financial Calculations:

- Calculates CAGR based on revenue projections.

- Evaluates unit economics using Customer Acquisition Cost (CAC) and Lifetime Value (LTV).

- Assesses investment potential based on financial indicators.

- ### Output Generation: The processed results are written to ```startup_results.xlsx``` for further review.

- Download Results: The generated output file is available for download after execution.

## Installation

To use this model, you need Python with the following dependencies:

```bash
pip install pandas numpy openpyxl
```

## Usage

1. Upload your startup data Excel file (```startup_data.xlsx```).

2. Run the script to analyze the data.

3. Download the generated results Excel file (```startup_results.xlsx```).

## Running the Model in Google Colab

1. Open Google Colab.

2. Upload the ```startup_data.xlsx``` file.

3. Run the Python script in a Colab notebook.

4. Download the ```startup_results.xlsx``` file once analysis is complete.

## File Descriptions

- ```HeySalad Financial Model.ipynb``` : Jupyter Notebook containing the Python script.

- ```startup_data.xlsx```: Input file with startup financial data.

- ```startup_results.xlsx```: Output file with processed financial insights.

## Key Financial Formulas

### CAGR Calculation:

```bash CAGR = \left(\frac{End\ Revenue}{Start\ Revenue}\right)^{\frac{1}{Years}} - 1```

### Unit Economics Assessment:

- LTV to CAC Ratio = ```LTV / CAC```

- Healthy Unit Economics: ```LTV/CAC > 3``` and ```Gross Margin > 40%```

### Investment Decision Criteria

If CAGR > 20% and the unit economics are healthy, the startup is classified as a good investment.

Otherwise, the startup is labeled as risky.

# Implement with AI

## Set Up Azure Services
We need to create 3 key resources in Azure:

✅ **Azure Function App** → Runs the code  
✅ **Azure Storage Account** → Stores Excel files  
✅ **Azure OpenAI Service** → Provides AI feedback  

### **1️⃣ Create an Azure Function App**
1. **Go to Azure Portal** → [portal.azure.com](https://portal.azure.com)
2. Search for **Function App** → Click **Create**
3. Fill in the details:
   - **Subscription**: Your active subscription
   - **Resource Group**: Create a new one (e.g., `startup-analysis`)
   - **Function App Name**: Choose a unique name (e.g., `startup-analysis-func`)
   - **Region**: Select the nearest region
   - **Runtime Stack**: Select **Python**
   - **Operating System**: **Linux**
   - **Plan Type**: **Consumption (Serverless)**
4. Click **Review + Create** → **Create**

### **2️⃣ Create an Azure Storage Account**
1. Go to **Azure Portal** → Search **Storage Accounts**
2. Click **Create**
3. Fill in details:
   - **Resource Group**: Use the one you just created (`startup-analysis`)
   - **Storage Account Name**: e.g., `startupstorage123`
   - **Region**: Same as your Function App
   - **Performance**: **Standard**
   - **Replication**: **Locally Redundant Storage (LRS)**
4. Click **Review + Create** → **Create**

### **3️⃣ Create an Azure OpenAI Service**
1. Go to **Azure Portal** → Search **Azure OpenAI**
2. Click **Create**
3. Fill in details:
   - **Subscription**: Your active subscription
   - **Resource Group**: `startup-analysis`
   - **Region**: Choose an available region (e.g., `Japan East`)
   - **Pricing Tier**: Standard
4. Click **Review + Create** → **Create**

## Set Up VS Code for Azure Functions**

### **1️⃣ Install Required Tools**
Run these commands in **VS Code Terminal**:

```sh
# Install Azure Functions Core Tools
npm install -g azure-functions-core-tools@4 --unsafe-perm true

# Install Azure CLI (if not installed)
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Install Python Packages
pip install azure-functions azure-storage-blob openai pandas
```

### **2️⃣ Create an Azure Function Project**
1. Open **VS Code**
2. Run:

```sh
# Initialize a new function project
func init AzureFunctionProject --python
cd AzureFunctionProject

# Create a new function
func new --name startup_analysis --template "HTTP trigger" --authlevel "anonymous"
```

## **🔹 Step 3: Write the Python Code**
Open **`startup_analysis/__init__.py`** and **replace** the code with:

```python
import azure.functions as func
import json
import os
import pandas as pd
from azure.storage.blob import BlobServiceClient
import openai

# Azure Storage Configuration
AZURE_STORAGE_CONNECTION_STRING = os.getenv("AZURE_STORAGE_CONNECTION_STRING")
CONTAINER_NAME = "startup-data"

# Azure OpenAI Configuration
openai.api_type = "azure"
openai.api_key = os.getenv("AZURE_OPENAI_KEY")
openai.api_base = os.getenv("AZURE_OPENAI_ENDPOINT")
openai.api_version = "2023-12-01-preview"
deployment_name = os.getenv("AZURE_OPENAI_DEPLOYMENT")


# Function to read Excel from Blob Storage
def read_excel_from_blob(file_name):
    blob_service_client = BlobServiceClient.from_connection_string(AZURE_STORAGE_CONNECTION_STRING)
    blob_client = blob_service_client.get_blob_client(container=CONTAINER_NAME, blob=file_name)

    with open(file_name, "wb") as file:
        file.write(blob_client.download_blob().readall())

    df = pd.read_excel(file_name)
    return df.to_dict(orient='records')


# Function to calculate CAGR
def calculate_cagr(start_revenue, end_revenue, years):
    return ((end_revenue / start_revenue) ** (1 / years)) - 1


# Function to evaluate unit economics
def evaluate_unit_economics(cac, ltv, gross_margin):
    ltv_to_cac_ratio = ltv / cac if cac > 0 else 0
    return {
        'LTV_to_CAC_Ratio': ltv_to_cac_ratio,
        'Healthy_Unit_Economics': ltv_to_cac_ratio > 3 and gross_margin > 0.4
    }


# Function to get AI feedback
def get_ai_feedback(startup_report):
    response = openai.ChatCompletion.create(
        model=deployment_name,
        messages=[
            {"role": "system", "content": "You are an AI startup investment advisor."},
            {"role": "user", "content": f"Assess this startup:\n{startup_report}"}
        ]
    )
    return response["choices"][0]["message"]["content"]


# Function to analyze startup investment potential
def assess_startup(data):
    monthly_revenue = data['Monthly_Revenue']
    growth_rate = data['Expected_Growth_Rate'] / 100
    years = 5

    # Projected revenue in 5 years
    projected_revenue = monthly_revenue * ((1 + growth_rate) ** (years * 12))
    cagr = calculate_cagr(monthly_revenue, projected_revenue, years)

    # Evaluate unit economics
    unit_econ = evaluate_unit_economics(data['CAC'], data['LTV'], data['Gross_Margin'])

    # Generate report
    report = {
        'Projected_Revenue_5_Years': projected_revenue,
        'CAGR': cagr,
        'LTV_to_CAC_Ratio': unit_econ['LTV_to_CAC_Ratio'],
        'Healthy_Unit_Economics': unit_econ['Healthy_Unit_Economics'],
        'Investment_Viability': 'Good' if unit_econ['Healthy_Unit_Economics'] and cagr > 0.2 else 'Risky'
    }

    # Get AI feedback
    report['AI_Feedback'] = get_ai_feedback(report)

    return report


# Azure Function HTTP trigger
def main(req: func.HttpRequest) -> func.HttpResponse:
    try:
        req_body = req.get_json()
        file_name = req_body["file_name"]

        # Read Excel from Blob Storage
        startup_list = read_excel_from_blob(file_name)
        results = [assess_startup(startup) for startup in startup_list]

        return func.HttpResponse(json.dumps(results), mimetype="application/json")

    except Exception as e:
        return func.HttpResponse(str(e), status_code=500)
```


## Deploy to Azure**

### **1️⃣ Set Up Environment Variables**
Create **`local.settings.json`** and add:

```json
{
    "IsEncrypted": false,
    "Values": {
        "AzureWebJobsStorage": "YOUR_AZURE_STORAGE_CONNECTION_STRING",
        "FUNCTIONS_WORKER_RUNTIME": "python",
        "AZURE_STORAGE_CONNECTION_STRING": "YOUR_STORAGE_CONNECTION_STRING",
        "AZURE_OPENAI_KEY": "YOUR_OPENAI_API_KEY",
        "AZURE_OPENAI_ENDPOINT": "YOUR_OPENAI_ENDPOINT",
        "AZURE_OPENAI_DEPLOYMENT": "YOUR_OPENAI_DEPLOYMENT_NAME"
    }
}
```

Replace **with your real Azure keys**.

### **2️⃣ Deploy the Function**
Run:

```sh
func azure functionapp publish startup-analysis-func
```


## **🚀 Final Test**
Call the API using **Postman** or **cURL**:

```sh
curl -X POST "https://startup-analysis-func.azurewebsites.net/api/startup_analysis" \
     -H "Content-Type: application/json" \
     -d '{"file_name": "startup_data.xlsx"}'
```

## Contributors

Chenran GU, Peter

## Contact

For any inquiries or contributions, please reach out via GitHub Issues.
