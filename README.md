# **Bank Customer Churn Prediction (DMML)**

## 1. Introduction

This project focuses on implementing a data management pipeline for a **bank churn prediction model**. The pipeline includes data ingestion, validation, preprocessing, feature storage, model building, and orchestration using **DVC** and **Apache Airflow**.

### Objectives

- Automate the data pipeline for efficient handling of raw and processed data.
- Ensure data quality through validation checks.
- Store and retrieve features efficiently using **PostgreSQL**.
- Automate the pipeline using **Apache Airflow**.
- Implement version control using **DVC** to track dataset and model changes.

---

## 2. Pipeline Overview

The pipeline workflow consists of the following stages:

1. **Data Ingestion** – Fetch data from Kaggle and an external API.
2. **Raw Data Storage** – Store raw data in an AWS S3 bucket with timestamp-based versioning.
3. **Data Validation** – Check for missing values, duplicates, and type mismatches.
4. **Data Preparation & Transformation** – Clean and transform data for training.
5. **Feature Store** – Store selected features in PostgreSQL.
6. **DVC for Versioning** – Track data and model changes.
7. **Model Building** – Train and evaluate multiple ML models.
8. **Orchestration & Automation** – Use Airflow to automate pipeline execution.

---

## 3. Data Ingestion

### Sources

- **Kaggle Dataset**: Historical churn data via the Kaggle API.
- **External API**: Provides new data for inference.

### Implementation

- Script: `1_Data_Ingestion.py`
- Local save path: `data/raw/bank_churn_YYYYMMDD_HHMMSS.csv`
- Logging enabled for data download tracking.

### Challenges & Solutions

- **Kaggle API authentication** → Resolved via correct `kaggle.json` setup.
- **API rate limits** → Handled using retry logic with exponential backoff.

---

## 4. Raw Data Storage

### Storage Location

- **AWS S3 Bucket**: `dmml-bank-churn-data`
- **Directory Structure**:
  - `raw_data/` – Original datasets.
  - `reports/` – Validation reports.

### Versioning Strategy

- Timestamp-based file naming.
- Older versions retained for reproducibility.

### Challenges

- **Permission errors** → Solved by updating bucket policies and IAM roles.
- **Network failures** → Retry with progressive backoff.
- **Policy updates** → Monitored and adjusted as needed.

---

## 5. Data Validation

### Checks Performed

- Missing values
- Data type mismatches
- Duplicate records
- Outlier detection

### Report Generation

- Logs and validation reports saved in `reports/`.
- Summary statistics, histograms, and box plots generated.

**Screenshot Placeholder**:  
![Validation Report](path/to/validation_report.png)

---

## 6. Data Preparation & Transformation

### Feature Engineering

- **BalancePerProduct**: `Balance / NumOfProducts`
- **Geography**: One-hot encoding

### Data Cleaning

- Drop irrelevant columns.
- Handle missing values (imputation/removal).

### Storage

- Processed data saved to `processed_data/` in S3.
- Transformed data in `transformed_data/`.

### Challenges

- Handled categorical variables via encoding.
- Used statistical checks for outliers and incorrect values.
- Unified training/API data formats with pre-processing rules.

---

## 7. Feature Store

### Database: PostgreSQL

#### Table Schema

```sql
CREATE TABLE feature_values (
  id SERIAL PRIMARY KEY,
  CreditScore FLOAT,
  Age FLOAT,
  Tenure INT,
  Balance FLOAT,
  NumOfProducts INT,
  IsActiveMember INT,
  Geography_France BOOLEAN,
  Geography_Germany BOOLEAN,
  Geography_Spain BOOLEAN,
  BalancePerProduct FLOAT,
  Exited INT, -- Nullable for API data
  data_source VARCHAR(10), -- 'train' or 'api'
  version TIMESTAMP -- Timestamp-based versioning
);
## 8. DVC for Versioning

- DVC is used to track raw data, processed data, features, and trained model files.
- Remote storage is configured using AWS S3 to store versioned artifacts.
- Enables reproducibility by allowing restoration of any previous version of data or models.
- Integrates seamlessly with Git for full pipeline version control.

## 9. Model Building

- Multiple machine learning models were trained and evaluated.
- Feature selection and correlation analysis were performed prior to training.
- Models such as Logistic Regression and Random Forest were used.
- Performance metrics (accuracy, precision, recall, F1 score) were computed.
- The best-performing model was saved for downstream inference.

## 10. Orchestration & Automation

- Apache Airflow was used to automate the entire pipeline.
- Separate DAGs were created for data ingestion, validation, transformation, and model training.
- Tasks are modular and scheduled for periodic execution.
- Retry mechanisms and logs are included for failure handling and monitoring.

## 11. Conclusion

- The project successfully implemented a full ML pipeline for bank churn prediction.
- Ensured automation, reproducibility, and version control throughout the pipeline.
- Integrated tools like DVC, PostgreSQL, Airflow, and S3 for robust data handling.

### Key Takeaways

- **Scalability**: Supports retraining with new data inputs automatically.
- **Reproducibility**: Pipeline outputs can be reproduced at any stage using DVC.
- **Automation**: Airflow minimizes manual intervention across stages.
- **Performance**: Feature engineering and model evaluation ensured optimal results.

### Future Enhancements

- Implement a real-time feature store for live inference.
- Integrate model monitoring to detect drift.
- Deploy the model via a web API for real-time usage.
