Current local implementation
1. Data manually loaded from source websites
2. Raw data lake (Data directory)
3. ETL into data warehouse (SQLite database)
4. Feature engineering (view_creation.py)
5. Loaded into feature store (.parquet view files)
6. |-> Used for training/validation of ML models (regression/forecasting modules)
7. |-> Also used for visualization (visualization.r)

* Orchestrated by the orchestrator script (orchestrator.bat)

* Note that the data warehouse above is compatible with both BI and ML applications:
    * BI workflow: Interactively query data warehouse - find trends, visualizations, make reports
        * The use of the data warehouse for interactive EDA and visualization resembled a BI workflow
    * ML workflow: Use data warehouse as source of curated input data, which is then feature engineered and used for ML training/validation

Possible cloud production-ready implementation:
1. Data ingestion: Fetch data from relevant web API's, load into the data lake
2. Data lake: Cloud storage such as AWS S3
3. ETL: Lambda function or Glue job to run ETL pipeline on demand or on schedule
    * Requires expansion of T step to automate extracting csv's, unzipping folders, etc. that were prepared manually in this project
    * Load data into a data warehouse, such as AWS Athena (compared to RDS, Athena is serverless and good for on-demand queries)
4. Feature engineering: Apply transformations to data from Athena in AWS Glue (supports Python and Spark)
5. Feature store: Sagemaker Feature Store to store the engineered features and make them available for training/validation of ML models
6. |-> ML training/validation: Also use Sagemaker (similar to Databricks) to run training/inference jobs on the engineered features
7. |-> Visualization: Use another Sagemaker job to visualize results in Seaborn and save plots to S3 (does not have first-class R support like Databricks)
    * Also consider AWS QuickSight for BI-style visualization dashboards
* An AWS step function could orchestrate each of these steps, including error handling, retry logic, and notifications
    * Assuming 5 GB of data per step, the pipeline would cost approximately $0.50-$1.00 per run, with only storage having ongoing costs

