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

## Contributors

Chenran GU, Peter

## Contact

For any inquiries or contributions, please reach out via GitHub Issues.
