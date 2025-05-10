# Naval Fleet Maintenance Data Pipeline & API System

⚡️ 30 Second Summary:
-
This project focuses on designing and implementing a secure, end-to-end data pipeline to process, clean, store, and query naval fleet maintenance records. It involves integrating various data sources (CSV, XML, JSON) from vendors, REST APIs, and manual uploads, transforming and storing them in a structured format for efficient querying. The project will also expose a secure API for querying readiness status, trends, and compliance metrics, along with a web-accessible dashboard. This project is ideal for developers working in data engineering, API development, or systems integration roles.

<img width="296" alt="postm" src="https://github.com/user-attachments/assets/b5fa8c80-e672-4930-b03b-390111a86cbd" />


Key Skills and Technologies Learned:
-
📊 Data Ingestion & Transformation: Using Python, Apache Airflow, Docker, AWS Lambda, and Apache Spark for effective data handling and transformation.

🛠️ Data Storage & Processing: Designing normalized PostgreSQL schemas and performing complex SQL queries to extract insights.

🔒 Security & Monitoring: Securing APIs with JWT/OIDC, rotating credentials using HashiCorp Vault, and monitoring pipeline health with Prometheus and Grafana.

🔌 API Development: Creating secure REST APIs using Flask or FastAPI, integrating OAuth2, and building endpoints for fleet data and compliance metrics.

📉 Dashboard & Reporting: Developing a compliance dashboard with key metrics, using Prometheus and ELK for monitoring pipeline health.

How this Project Fits into Broader Technical Knowledge:
-
This project encompasses various concepts common to data engineering, API development, and system architecture. It builds a pipeline for extracting, transforming, and loading (ETL) data and integrates it with secure API access. This project also covers best practices for continuous integration, monitoring, and security, crucial for any data-driven product in a professional environment.

🗂️ Data Sources:
-
CSV/XML/JSON files from ship vendors (batch uploads)

REST APIs from government logistics systems (OAuth2/API key authenticated)

Manual uploads via CLI or internal SFTP server

⚙️ Pipeline Components (Tools Stack):
-
Ingestion & Transformation:

Python with pandas and SQLAlchemy: For reading, cleaning, and transforming data.

Apache Airflow: Orchestrating jobs that run daily/weekly.

Docker: Containerizing ingestion scripts to ensure consistent environments.

AWS Lambda: Handling API-triggered events for real-time data ingestion.

Storage:

PostgreSQL / Amazon RDS: Structured data storage for processed maintenance records.

Amazon S3: For staging raw and cleaned files.

Processing:

Apache Spark (optional): To handle large-scale datasets for transformation.

SQL scripts: For data normalization, deduplication, and temporal analysis.

APIs:

Flask / FastAPI: Creating a RESTful API, secured with JWT or OIDC.

API endpoints for:

/status?ship_id=

/fleet/summary

/compliance/issues

Monitoring & Security:

Prometheus + Grafana: For monitoring pipeline health, including API latency and success/failure metrics.

ELK Stack: For logging pipeline events and API interactions.

HashiCorp Vault: For secure storage and rotation of API credentials and database passwords.

CI/CD with GitHub Actions and pre-commit hooks for automated deployment and code quality checks.

🧠 SQL-Focused Features:
-
Complex JOINs across ships, maintenance events, and vendor logs.

Recursive queries for tracing part dependencies.

Window functions for calculating rolling maintenance windows and time-based trends.

Stored procedures for automated compliance scoring.

Materialized views for efficient querying in the compliance dashboard.

📈 End Deliverables:
-
Normalized PostgreSQL schema ready for querying.

Automated pipeline using Apache Airflow (DAGs) to refresh data daily.

REST API with authentication and audit logging.

SQL-based compliance dashboard with:

Fleet readiness score

MTTR and failure trends

Days overdue on service

Prometheus metrics dashboard for pipeline health.

ELK dashboard for pipeline/API logs.

Security documentation:

Secrets rotation

Data access control policies

Audit logs

💡 Bonus Stretch Goals:
-
Unit tests for SQL queries using pytest + dbt.

API rate limiting and throttling via NGINX or API Gateway.

Schema validation for incoming JSON using pydantic.

Detailed Project Walkthrough:
-
# 1. Setup and Containerization 
What to do: Create a Docker image for data ingestion scripts.

Why: Ensures portability and consistency across different environments.

Real-World Application: Containerization is a best practice in production environments to streamline deployment and isolate dependencies.

Learning Moment: Learn Docker basics and how containerization aids in deployment.

Troubleshooting Tip: Ensure the Dockerfile uses the correct base image and includes all necessary dependencies.
```
docker build -t ingestion-scripts .
```
Take Screenshot: Capture Docker container image creation and run command.

<img width="398" alt="docfi" src="https://github.com/user-attachments/assets/aa4230b8-b2fa-4f68-8695-022cfe9c729c" />

<img width="443" alt="docf" src="https://github.com/user-attachments/assets/5a0c1b9c-cba4-44b7-8e9d-e09405f65235" />


## 2. Data Ingestion with Apache Airflow

What to do: Set up an Apache Airflow DAG to automate data ingestion from CSV/XML/JSON files and REST APIs.

Why: Automates the ETL process to ensure data is ingested daily or weekly without manual intervention.

Real-World Application: Automation of ETL processes in production environments saves time and reduces human error.

Learning Moment: Get hands-on with Airflow and DAGs to automate workflows.

Troubleshooting Tip: Check Airflow logs for errors if the DAG fails to execute.
```
from airflow import DAG
from airflow.operators.python_operator import PythonOperator
from airflow.hooks.S3_hook import S3Hook

def ingest_data():
    # Your ingestion code here

dag = DAG('data_ingestion', schedule_interval='@daily')
ingest_task = PythonOperator(task_id='ingest_data', python_callable=ingest_data, dag=dag)
```
Take Screenshot: Capture the running DAG in the Airflow UI.

<img width="434" alt="airfd" src="https://github.com/user-attachments/assets/5c22a846-ae85-4fad-8f7c-414d674b67b4" />


<img width="653" alt="airf" src="https://github.com/user-attachments/assets/231cc202-005a-44e9-9720-65e1fddb3165" />


## 3. Setting Up PostgreSQL & SQL Transformation
What to do: Design a normalized schema in PostgreSQL to store processed data.

Why: Normalized data ensures better performance and scalability for querying large datasets.

Real-World Application: Database design is a key skill for data engineers to ensure data integrity and optimize performance.

Learning Moment: Learn how to create tables, relationships, and indexes in PostgreSQL for efficient querying.

Troubleshooting Tip: Ensure foreign keys and indexes are correctly set up to avoid slow queries.
```
CREATE TABLE ships (
    ship_id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);
```
Take Screenshot: Capture the schema in PostgreSQL.

<img width="446" alt="sql" src="https://github.com/user-attachments/assets/9f2ff59e-fc71-4a00-82d3-b2991aef6e21" />


# 4. Developing the REST API

What to do: Create secure REST API endpoints using Flask/FastAPI.

Why: API endpoints are needed for web access to fleet data and maintenance metrics.

Real-World Application: Building secure APIs is essential for exposing data to external systems while maintaining security.

Learning Moment: Understand how to use JWT/OIDC for securing APIs.

Troubleshooting Tip: Verify token validation and ensure the endpoints are correctly secured with OAuth2.
```
from fastapi import FastAPI, Depends
from fastapi.security import OAuth2PasswordBearer

app = FastAPI()
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

@app.get("/status")
def get_status(ship_id: int, token: str = Depends(oauth2_scheme)):
    return {"ship_id": ship_id, "status": "active"}
```
Take Screenshot: Capture an API response in a tool like Postman.

<img width="446" alt="fastap" src="https://github.com/user-attachments/assets/55cfc692-bd33-4bf4-a72e-1dd27fd4322e" />


<img width="296" alt="postm" src="https://github.com/user-attachments/assets/b3cf5581-1e7a-4dde-bbed-fd872d727b87" />

Challenges & Solutions:
Challenge: Managing multiple data formats and schemas.

Solution: Use pandas and custom scripts to normalize the incoming data and standardize the fields.

Challenge: Securing API and database credentials.

Solution: Implement HashiCorp Vault for credentials management and automated rotation.

Summary:
This project provided practical experience in building a robust data pipeline and API system, securing data at every step, and monitoring pipeline health with modern tools. I also learned how to handle large datasets, design an efficient database schema, and expose secure RESTful APIs.

References:
Apache Airflow Documentation

FastAPI Documentation

HashiCorp Vault Documentation

PostgreSQL Documentation

## Congratulations! You’ve completed the project! 🎉

What You’ve Done:
-
Built and containerized an automated data ingestion pipeline using Airflow and Docker.

Designed a normalized PostgreSQL schema for fleet maintenance data.

Created a secure REST API with FastAPI, exposed fleet readiness and compliance endpoints.

Implemented a monitoring solution using Prometheus and Grafana.

Clean Up:
-
To clean up the environment and delete resources:

Remove Docker containers and images.

Drop tables and schemas in PostgreSQL.

Disable and remove Airflow DAGs.
