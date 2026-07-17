# Intelligent Systems Project: Macroeconomic and Bond Yield Forecasting

**Academic Year:** 2025/2026  
**Degree:** MSc in Computer Engineering, University of Pisa  
**Course:** Intelligent Systems  
**Instructors:** Prof. Pietro Ducange, Prof. Fabrizio Ruffini  
**Author:** Francesco Chiera  

## Project Overview
This repository contains the code for an Intelligent System designed to forecast macroeconomic regimes and tactical government bond yields (Yield To Maturity). The project follows a complete **Knowledge Discovery in Databases (KDD)** pipeline, from data ingestion to model deployment and interpretability.

The system compares traditional Machine Learning approaches (Random Forest), Deep Learning architectures (LSTM optimized via Optuna), and state-of-the-art Zero-Shot Foundation Models for Time-Series (Salesforce's MOIRAI). Finally, Explainable AI (SHAP) is integrated to provide transparency to the system's decisions.

## Repository Structure
The code is structured sequentially into 6 Jupyter Notebooks to strictly respect the KDD process. **They must be executed in the following order:**

* `01_Data_Ingestion_EDA.ipynb`: Data collection from local CSV files and APIs (FRED, Yahoo Finance), followed by Data Consistency Checks and Exploratory Data Analysis.
* `02_Preprocessing_Feature_Engineering.ipynb`: Data cleaning, target definition (YTM calculation), and generation of complex lagged features to capture historical memory.
* `03A_Modeling_and_Validation_macro.ipynb`: Setup of Purged Time-Series Cross-Validation and training of Naive Baselines, Random Forest, and LSTM on the long-term Macroeconomic dataset.
* `03B_Modeling_and_Validation_Bonds_Macro.ipynb`: Training and hyperparameter optimization (via Optuna) of ML and DL models on the short-term tactical Macro+Bonds dataset.
* `04_Moirai_Forecasting.ipynb`: Implementation of Zero-Shot Inference using the MOIRAI Foundation Model on both Macro and Bond datasets.
* `05_Explainable_AI.ipynb`: Local and Global interpretability analysis using SHAP (Summary Plots and Waterfall Plots) on the trained Random Forest model.

## Prerequisites and Installation

To run the code, you need Python 3.9+ to install the requirements:
* `requirements.txt`: For 1, 2, 3, 5 notebooks.
* `requirements_moirai.txt`: For notebook 04.

*Note: To run Notebook 04, the `gluonts` and `uni2ts` libraries are required, thy cause some inconsistency in the installation of pythorch, so is highly suggested to create a separate enviroment for just this notebook.*

## Instructions for Running the Code

1. **Clone the repository:**
   ```bash
   git clone <your_github_repo_url>
   cd <repository_folder>
   ```

2. **Folder Structure Setup:**
   Ensure you have a `./dataset/` folder containing the initial raw micro-structural CSV files from Borsa Italiana. 
   The code will automatically generate a `./data/` folder to save processed datasets (`.csv`), trained models (`.pkl`, `.pth`), and Optuna studies to pass information between notebooks.

3. **API Keys Configuration:**
   Notebook `01` downloads macroeconomic data from the Federal Reserve Economic Data (FRED). You need to provide a free API Key. 
   * Create a `.env` file in the root directory.
   * Add the following line: `FRED_API_KEY="your_api_key_here"`
   * Alternatively, you can directly set the environment variable or paste the key into the notebook cell when prompted.

4. **Execution:**
   Run the Jupyter Notebooks strictly in order from `01` to `05`. Each notebook relies on the `.csv` or model files generated and saved by the previous one. 
   *Warning:* Notebook `03A`, `03B` (Optuna Optimization) and `04` (MOIRAI Inference) are computationally intensive. Running them on a CUDA-enabled GPU is highly recommended.

## Acknowledgments
Developed as the final project for the Intelligent Systems course at the University of Pisa.