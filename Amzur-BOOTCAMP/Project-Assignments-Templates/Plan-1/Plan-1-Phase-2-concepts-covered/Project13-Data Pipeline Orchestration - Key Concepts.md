# Project 13: Data Pipeline Orchestration - Key Concepts

> **Note:** This document establishes the foundational data pipeline orchestration concepts for bootcamp students that enable automated, reliable data processing workflows.

## 1. Simple Workflow Orchestration & Task Automation

### Concept Overview
Data pipeline orchestration involves creating automated sequences of data processing tasks that run reliably on schedules or triggers. Using Apache Airflow, we create workflows (DAGs - Directed Acyclic Graphs) that handle data extraction, transformation, loading, and monitoring without manual intervention.

### Problem It Solves
Many data processing tasks require multiple steps that must run in a specific order, often on schedules (daily, weekly, etc.). Manual execution is unreliable, time-consuming, and doesn't scale. When data processing fails, it's often unclear where the problem occurred and how to restart safely.

### Business Benefits & Value Proposition
**Quantifiable Benefits:**
- **Automation Efficiency**: 80-95% reduction in manual data processing time
- **Reliability Improvement**: 99%+ successful execution rate with proper retry logic
- **Error Reduction**: 90% fewer data processing errors through automated validation
- **Cost Savings**: $30,000-$100,000 annually in operational efficiency gains

**Educational Impact Examples:**
- Students understand how modern data teams automate operations
- Practical experience with enterprise data pipeline tools
- Foundation for understanding data engineering workflows
- Portfolio project demonstrating automation and reliability skills

**Learning Advantages:**
- Hands-on experience with industry-standard orchestration tools
- Understanding of fault-tolerant system design
- Skills applicable to any data-driven organization
- Knowledge of scheduling and dependency management

### Solution Approach
- **Visual Workflow Design**: Creating workflows with clear task dependencies
- **Reliable Scheduling**: Automated execution with proper error handling
- **Task Dependencies**: Managing complex data processing sequences
- **Step-by-Step Learning**: Daily progression from simple to automated systems
- **Monitoring Integration**: Visibility into workflow execution and failures

### Implementation Insight
```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from airflow.operators.bash import BashOperator
from airflow.operators.email_operator import EmailOperator
from datetime import datetime, timedelta
import pandas as pd
import logging

# Simple Airflow DAG examples for bootcamp learning

# Default arguments for all DAGs
default_args = {
    'owner': 'student',
    'depends_on_past': False,
    'start_date': datetime(2024, 1, 1),
    'email_on_failure': True,
    'email_on_retry': False,
    'retries': 2,
    'retry_delay': timedelta(minutes=5)
}

# Example 1: Simple Data Processing DAG
def process_daily_sales():
    """Process daily sales data with error handling"""
    try:
        # Load daily sales data
        df = pd.read_csv('/opt/airflow/data/input/daily_sales.csv')
        
        # Basic data validation
        if df.empty:
            raise ValueError("No sales data found for processing")
        
        # Clean and process data
        df_clean = df.dropna()
        df_clean['total_amount'] = df_clean['quantity'] * df_clean['price']
        df_clean['processed_date'] = datetime.now().date()
        
        # Save processed data
        output_path = f"/opt/airflow/data/processed/sales_{datetime.now().strftime('%Y%m%d')}.csv"
        df_clean.to_csv(output_path, index=False)
        
        logging.info(f"Processed {len(df_clean)} sales records")
        return f"Successfully processed {len(df_clean)} sales records"
        
    except Exception as e:
        logging.error(f"Sales processing failed: {str(e)}")
        raise

def validate_processed_data():
    """Validate the processed data quality"""
    try:
        # Read the processed file
        processed_file = f"/opt/airflow/data/processed/sales_{datetime.now().strftime('%Y%m%d')}.csv"
        df = pd.read_csv(processed_file)
        
        # Basic quality checks
        checks = {
            'total_records': len(df),
            'has_nulls': df.isnull().sum().sum() == 0,
            'positive_amounts': (df['total_amount'] > 0).all(),
            'valid_dates': df['processed_date'].notna().all()
        }
        
        # Log validation results
        for check, result in checks.items():
            if not result and check != 'total_records':
                raise ValueError(f"Data quality check failed: {check}")
        
        logging.info(f"Data validation passed: {checks}")
        return "Data validation successful"
        
    except Exception as e:
        logging.error(f"Data validation failed: {str(e)}")
        raise

# Create the DAG
daily_sales_dag = DAG(
    'daily_sales_processing',
    default_args=default_args,
    description='Process daily sales data automatically',
    schedule_interval='0 8 * * *',  # Run daily at 8 AM
    catchup=False,
    max_active_runs=1
)

# Define tasks
process_task = PythonOperator(
    task_id='process_sales_data',
    python_callable=process_daily_sales,
    dag=daily_sales_dag
)

validate_task = PythonOperator(
    task_id='validate_data_quality',
    python_callable=validate_processed_data,
    dag=daily_sales_dag
)

archive_task = BashOperator(
    task_id='archive_raw_data',
    bash_command="""
    mkdir -p /opt/airflow/data/archive/$(date +%Y%m%d)
    mv /opt/airflow/data/input/*.csv /opt/airflow/data/archive/$(date +%Y%m%d)/
    echo "Raw data archived successfully"
    """,
    dag=daily_sales_dag
)

success_email = EmailOperator(
    task_id='send_success_email',
    to=['data-team@company.com'],
    subject='Daily Sales Processing Completed',
    html_content="""
    <h3>Daily Sales Processing Successful</h3>
    <p>The daily sales data processing has completed successfully.</p>
    <p>Processing Date: {{ ds }}</p>
    <p>Check the processed data in the output directory.</p>
    """,
    dag=daily_sales_dag
)

# Set task dependencies
process_task >> validate_task >> archive_task >> success_email

# Example 2: File Monitoring and Processing DAG
import os
from pathlib import Path

def check_for_new_files():
    """Check if new files are available for processing"""
    input_dir = Path('/opt/airflow/data/input')
    new_files = list(input_dir.glob('*.csv'))
    
    if new_files:
        logging.info(f"Found {len(new_files)} files to process")
        return f"found_files:{','.join([f.name for f in new_files])}"
    else:
        logging.info("No new files found")
        raise ValueError("No files to process")

def process_files(**context):
    """Process all files found in input directory"""
    # Get files from previous task
    files_info = context['task_instance'].xcom_pull(task_ids='check_files')
    file_names = files_info.split(':')[1].split(',')
    
    processed_count = 0
    for file_name in file_names:
        try:
            file_path = f"/opt/airflow/data/input/{file_name}"
            df = pd.read_csv(file_path)
            
            # Process the file
            df_processed = df.copy()
            df_processed['processing_timestamp'] = datetime.now()
            
            # Save to processed directory
            output_path = f"/opt/airflow/data/processed/processed_{file_name}"
            df_processed.to_csv(output_path, index=False)
            
            # Move original to archive
            archive_path = f"/opt/airflow/data/archive/{file_name}"
            os.rename(file_path, archive_path)
            
            processed_count += 1
            logging.info(f"Processed file: {file_name}")
            
        except Exception as e:
            logging.error(f"Failed to process {file_name}: {str(e)}")
            # Don't raise - continue with other files
    
    if processed_count == 0:
        raise ValueError("No files were successfully processed")
    
    return f"Successfully processed {processed_count} files"

# File monitoring DAG
file_processing_dag = DAG(
    'file_monitoring_processing',
    default_args=default_args,
    description='Monitor and process files automatically',
    schedule_interval=timedelta(minutes=30),  # Check every 30 minutes
    catchup=False
)

check_files_task = PythonOperator(
    task_id='check_files',
    python_callable=check_for_new_files,
    dag=file_processing_dag
)

process_files_task = PythonOperator(
    task_id='process_files',
    python_callable=process_files,
    provide_context=True,
    dag=file_processing_dag
)

# Set dependency
check_files_task >> process_files_task

# Example 3: ETL Pipeline with Database Integration
import sqlite3

def extract_data_from_api():
    """Extract data from API (simulated)"""
    # Simulate API data extraction
    import requests
    import json
    
    # For demo purposes, create sample data
    sample_data = [
        {'id': 1, 'name': 'Product A', 'price': 29.99, 'category': 'Electronics'},
        {'id': 2, 'name': 'Product B', 'price': 19.99, 'category': 'Books'},
        {'id': 3, 'name': 'Product C', 'price': 39.99, 'category': 'Clothing'}
    ]
    
    # Save raw data
    with open('/opt/airflow/data/raw/api_data.json', 'w') as f:
        json.dump(sample_data, f)
    
    logging.info(f"Extracted {len(sample_data)} records from API")
    return "Data extraction successful"

def transform_api_data():
    """Transform the extracted data"""
    import json
    
    # Load raw data
    with open('/opt/airflow/data/raw/api_data.json', 'r') as f:
        raw_data = json.load(f)
    
    # Transform data
    df = pd.DataFrame(raw_data)
    
    # Data transformations
    df['price_category'] = df['price'].apply(
        lambda x: 'Low' if x < 25 else 'Medium' if x < 35 else 'High'
    )
    df['extracted_at'] = datetime.now()
    df['product_code'] = df['category'].str[:3].str.upper() + df['id'].astype(str).str.zfill(3)
    
    # Save transformed data
    df.to_csv('/opt/airflow/data/transformed/products_transformed.csv', index=False)
    
    logging.info(f"Transformed {len(df)} records")
    return "Data transformation successful"

def load_to_database():
    """Load transformed data to database"""
    # Connect to SQLite database
    conn = sqlite3.connect('/opt/airflow/data/warehouse.db')
    
    # Read transformed data
    df = pd.read_csv('/opt/airflow/data/transformed/products_transformed.csv')
    
    # Load to database
    df.to_sql('products', conn, if_exists='replace', index=False)
    
    # Verify load
    cursor = conn.cursor()
    cursor.execute("SELECT COUNT(*) FROM products")
    count = cursor.fetchone()[0]
    
    conn.close()
    
    logging.info(f"Loaded {count} records to database")
    return f"Successfully loaded {count} records to database"

# ETL Pipeline DAG
etl_pipeline_dag = DAG(
    'etl_pipeline',
    default_args=default_args,
    description='Complete ETL pipeline',
    schedule_interval='0 6 * * *',  # Run daily at 6 AM
    catchup=False
)

extract_task = PythonOperator(
    task_id='extract_data',
    python_callable=extract_data_from_api,
    dag=etl_pipeline_dag
)

transform_task = PythonOperator(
    task_id='transform_data',
    python_callable=transform_api_data,
    dag=etl_pipeline_dag
)

load_task = PythonOperator(
    task_id='load_data',
    python_callable=load_to_database,
    dag=etl_pipeline_dag
)

# ETL dependencies
extract_task >> transform_task >> load_task

# Simple workflow management class for understanding
class SimpleWorkflowManager:
    def __init__(self):
        self.workflows = {}
        self.execution_history = []
        
    def register_workflow(self, name, dag):
        """Register a workflow for tracking"""
        self.workflows[name] = {
            'dag': dag,
            'executions': 0,
            'last_run': None,
            'success_rate': 0
        }
        
    def execute_workflow(self, name):
        """Simulate workflow execution"""
        if name not in self.workflows:
            raise ValueError(f"Workflow '{name}' not found")
        
        workflow = self.workflows[name]
        
        try:
            # Simulate execution
            start_time = datetime.now()
            # ... workflow execution logic ...
            end_time = datetime.now()
            
            execution_record = {
                'workflow': name,
                'start_time': start_time,
                'end_time': end_time,
                'status': 'success',
                'duration': (end_time - start_time).total_seconds()
            }
            
            workflow['executions'] += 1
            workflow['last_run'] = end_time
            self.execution_history.append(execution_record)
            
            logging.info(f"Workflow '{name}' completed successfully")
            
        except Exception as e:
            execution_record = {
                'workflow': name,
                'start_time': start_time,
                'end_time': datetime.now(),
                'status': 'failed',
                'error': str(e)
            }
            
            self.execution_history.append(execution_record)
            logging.error(f"Workflow '{name}' failed: {str(e)}")
            raise
    
    def get_workflow_stats(self):
        """Get workflow execution statistics"""
        stats = {}
        for name, info in self.workflows.items():
            recent_executions = [
                e for e in self.execution_history 
                if e['workflow'] == name
            ][-10:]  # Last 10 executions
            
            success_count = sum(1 for e in recent_executions if e['status'] == 'success')
            success_rate = (success_count / len(recent_executions)) * 100 if recent_executions else 0
            
            stats[name] = {
                'total_executions': info['executions'],
                'last_run': info['last_run'],
                'success_rate': success_rate,
                'recent_executions': len(recent_executions)
            }
        
        return stats

# Usage example for bootcamp learning
def demonstrate_workflow_orchestration():
    """Complete example showing workflow orchestration in action"""
    
    print("=== Airflow Workflow Orchestration Demo ===")
    
    # Initialize workflow manager
    manager = SimpleWorkflowManager()
    
    # Register workflows
    manager.register_workflow("daily_sales", daily_sales_dag)
    manager.register_workflow("file_processing", file_processing_dag)
    manager.register_workflow("etl_pipeline", etl_pipeline_dag)
    
    print(f"Registered {len(manager.workflows)} workflows")
    
    # Show workflow information
    for name, info in manager.workflows.items():
        print(f"\nWorkflow: {name}")
        print(f"  Description: {info['dag'].description}")
        print(f"  Schedule: {info['dag'].schedule_interval}")
        print(f"  Max Active Runs: {info['dag'].max_active_runs}")
    
    # Get workflow statistics
    stats = manager.get_workflow_stats()
    print(f"\nWorkflow Statistics:")
    for name, stat in stats.items():
        print(f"  {name}:")
        print(f"    Total Executions: {stat['total_executions']}")
        print(f"    Success Rate: {stat['success_rate']:.1f}%")
        print(f"    Last Run: {stat['last_run']}")
    
    return manager

# Error handling and monitoring examples
def setup_workflow_monitoring():
    """Setup monitoring and alerting for workflows"""
    
    # Example monitoring function
    def check_workflow_health():
        """Check workflow health and send alerts"""
        manager = SimpleWorkflowManager()
        stats = manager.get_workflow_stats()
        
        alerts = []
        for name, stat in stats.items():
            if stat['success_rate'] < 90:
                alerts.append(f"Workflow '{name}' has low success rate: {stat['success_rate']:.1f}%")
            
            if stat['last_run'] and (datetime.now() - stat['last_run']).hours > 25:
                alerts.append(f"Workflow '{name}' hasn't run in over 24 hours")
        
        if alerts:
            print("WORKFLOW ALERTS:")
            for alert in alerts:
                print(f"  - {alert}")
        else:
            print("All workflows are healthy")
        
        return alerts
    
    return check_workflow_health

# Workflow best practices for bootcamp students
class WorkflowBestPractices:
    """Best practices for workflow design and management"""
    
    @staticmethod
    def design_principles():
        """Core principles for workflow design"""
        principles = [
            "Keep tasks small and focused on single operations",
            "Make tasks idempotent (safe to run multiple times)",
            "Use proper error handling and logging",
            "Set appropriate retry policies with exponential backoff",
            "Monitor workflow performance and success rates",
            "Document workflow purpose and dependencies",
            "Use meaningful task and workflow names",
            "Test workflows thoroughly before production deployment"
        ]
        
        print("Workflow Design Best Practices:")
        for i, principle in enumerate(principles, 1):
            print(f"{i}. {principle}")
        
        return principles
    
    @staticmethod
    def common_patterns():
        """Common workflow patterns"""
        patterns = {
            "ETL Pipeline": "Extract → Transform → Load → Validate",
            "File Processing": "Monitor → Process → Archive → Notify",
            "Data Quality": "Load → Validate → Clean → Report",
            "Backup & Archive": "Export → Compress → Upload → Verify",
            "Batch Processing": "Collect → Batch → Process → Distribute"
        }
        
        print("Common Workflow Patterns:")
        for pattern, flow in patterns.items():
            print(f"  {pattern}: {flow}")
        
        return patterns
```

### Key Performance Parameters (Bootcamp Level)
**Metrics to Track:**
- **Workflow Success Rate**: Aim for > 95% successful executions
- **Processing Time**: Reasonable completion times for data size
- **Error Recovery**: Clear error messages and retry mechanisms
- **Scheduling Accuracy**: Tasks run on time as scheduled
- **Data Quality**: Consistent output quality from automated processes

**Learning Benchmarks:**
- Day 1: Create basic Hello World DAG that runs successfully
- Day 2: Build multi-step data processing workflow with dependencies
- Day 3: Deploy scheduled workflows with monitoring and notifications
- Portfolio readiness: Professional automation examples for job applications

**Success Indicators:**
- **Multi-Step Workflows**: DAGs with proper task dependencies and error handling
- **Reliable Scheduling**: Workflows run automatically on defined schedules
- **Error Handling**: Graceful failures with notifications and retry logic
- **Technical Understanding**: Students can explain workflow concepts and troubleshoot issues

### Common Pitfalls & Anti-Patterns (Bootcamp Context)
- **Monolithic Tasks**: Creating tasks that do too much instead of focused operations
- **Poor Error Handling**: Workflows fail without clear error messages or recovery
- **No Monitoring**: Deploying workflows without visibility into execution status
- **Tight Coupling**: Tasks that depend on specific file paths or hardcoded values
- **Missing Documentation**: Creating workflows without explaining what they do
- **No Testing**: Deploying workflows without testing with sample data

### Cross-References & Dependencies (Simplified)
- **Builds on Project 9**: ML model training can be orchestrated with Airflow
- **Integrates with Project 12**: N8N handles simple workflows, Airflow handles data processing
- **Supports Project 15**: Big data processing workflows benefit from orchestration
- **Feeds into Project 16**: Capstone uses workflow orchestration for complete automation
- **Foundation Knowledge**: Basic Python, file handling, scheduling concepts, SQL basics

### Architecture Diagram (Simplified)
```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│                 │    │                  │    │                 │
│   Scheduler     │───▶│   Airflow Web    │───▶│   Task Executor │
│                 │    │                  │    │                 │
│ • Cron-based    │    │ • DAG Browser    │    │ • Python Tasks  │
│ • Trigger DAGs  │    │ • Task Monitor   │    │ • Bash Commands │
│ • Handle Deps   │    │ • Log Viewer     │    │ • Email Alerts  │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │                        │
                                │                        │
┌─────────────────┐    ┌──────────────────┐    ┌─────────▼───────┐
│                 │    │                  │    │                 │
│  Data Storage   │◀───│  Processing      │◀───│  Data Sources   │
│                 │    │                  │    │                 │
│ • Processed     │    │ • ETL Logic      │    │ • CSV Files     │
│ • Archives      │    │ • Data Clean     │    │ • Databases     │
│ • Results       │    │ • Validation     │    │ • APIs          │
└─────────────────┘    └──────────────────┘    └─────────────────┘
```

### Scalability & Production Considerations (Bootcamp Perspective)
**Learning Progression:**
- **Start Simple**: Basic DAGs with single tasks (Hello World)
- **Add Complexity**: Multi-step workflows with dependencies
- **Real Applications**: Solve actual data processing problems
- **Build Portfolio**: Create impressive automation examples for job applications

**Resource Requirements (Local Development):**
- **Docker Setup**: Simple containerized Airflow installation
- **Local Files**: Process local CSV files and databases
- **Simple APIs**: Connect to email and basic web services
- **Documentation**: Clear setup guides and troubleshooting help

**Skills Development:**
- **Workflow Thinking**: Understanding task dependencies and sequencing
- **Error Handling**: Building robust, fault-tolerant data processes
- **Monitoring**: Creating visibility into automated processes
- **Technical Communication**: Explaining automation value to stakeholders

### Industry Use Cases & Examples (Bootcamp Context)
**Learning-Friendly Applications:**
- **Daily Reports**: Automated report generation and distribution
- **Data Backup**: Scheduled data backup and archival processes
- **Data Quality**: Automated data validation and cleaning workflows
- **ETL Processing**: Extract-Transform-Load pipelines for business intelligence

**Portfolio Examples:**
- **Sales Analytics**: Daily processing of sales data with automated reporting
- **Data Migration**: Automated data movement and transformation workflows
- **Quality Monitoring**: Automated data quality checks and alerts
- **Backup Automation**: Scheduled backup and recovery processes

**Career Relevance:**
- **Data Engineering**: Essential skill for data pipeline development
- **Business Intelligence**: Automating reporting and analytics workflows
- **DevOps**: Understanding of automation and reliability patterns
- **System Administration**: Automated system maintenance and monitoring

### Further Resources (Bootcamp Friendly)
- [Apache Airflow Documentation](https://airflow.apache.org/docs/) - Official documentation and tutorials
- [Airflow Best Practices](https://airflow.apache.org/docs/apache-airflow/stable/best-practices.html) - Production-ready workflow patterns
- [Docker Airflow Setup](https://airflow.apache.org/docs/apache-airflow/stable/start/docker.html) - Container-based installation guide
- [Workflow Design Patterns](https://github.com/apache/airflow/tree/main/airflow/example_dags) - Example DAGs and patterns
- [Monitoring and Logging](https://airflow.apache.org/docs/apache-airflow/stable/logging-monitoring/logging-tasks.html) - Workflow monitoring best practices

### Assessment & Progress Tracking
**Daily Checkpoints:**
- **Day 1**: Create and run basic Hello World DAG successfully
- **Day 2**: Build multi-step data processing workflow with proper dependencies
- **Day 3**: Deploy scheduled workflows with monitoring and email notifications

**Mastery Indicators:**
- **Explains Workflow Concepts**: Can describe DAGs, tasks, and dependencies clearly
- **Debugs Issues**: Can identify and fix workflow failures using logs and monitoring
- **Designs Processes**: Can analyze manual processes and design automated workflows
- **Manages Dependencies**: Can create complex workflows with proper task ordering

**Portfolio Readiness:**
- **Working Automation**: Complete workflows solving real data processing problems
- **Business Impact Documentation**: Clear explanation of time/cost savings achieved
- **Technical Implementation**: Shows understanding of scheduling, dependencies, and error handling
- **Professional Presentation**: Clean documentation suitable for job applications

### Advanced Learning Extensions (Optional)
**If Time Permits:**
- **Custom Operators**: Build specialized operators for specific data processing needs
- **Dynamic DAGs**: Create workflows that generate tasks dynamically
- **External System Integration**: Connect to databases, cloud services, and enterprise systems
- **Advanced Monitoring**: Custom metrics, alerts, and dashboard integration
- **Performance Optimization**: Optimize workflow performance for large-scale data processing
