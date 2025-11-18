# 📘 dvdrental LangGraph + PandasAI Pipeline

Natural Language → SQL → PostgreSQL Execution → PandasAI Summary & Visualization → LLM Insights

This project provides an end-to-end analytical pipeline that converts natural-language questions into SQL queries, executes them on a PostgreSQL dvdrental database, summarizes the results using PandasAI, generates visualizations with automatic fallback logic, and finally produces LLM-based business insights.
The entire workflow is orchestrated by LangGraph for reliability and modularity.

# Data download
https://neon.com/postgresql/postgresql-getting-started/postgresql-sample-database

# 🗄 Database Connection

Configured using SQLAlchemy:

DATABASE_URL = "postgresql+psycopg2://postgres:PASSWORD@127.0.0.1:5433/dvdrental"
engine = create_engine(DATABASE_URL)

Make sure:
The PostgreSQL server is running
The dvdrental database is restored and accessible
Your port/user/password match your PostgreSQL setup

# 🚀 Key Features
1. Natural Language → SQL Generation

Uses GPT-4o-mini for SQL generation
SQL is constructed strictly based on dvdrental schema
JOIN logic, filtering, aggregation are generated automatically
Ensures valid PostgreSQL syntax only

2. SQL Execution with Automatic Error Recovery

Queries are executed via SQLAlchemy
If an SQL error occurs or returns an empty result,
the system automatically regenerates and retries the query using the Clarifier node

3. PandasAI-Based DataFrame Analysis

Structured summary of:
Column meanings
Row counts
Missing values
Key statistics (mean, max, min, std, etc.)
Powered by SmartDataframe with GPT-based analysis

4. Chart Generation with Guaranteed Fallback

PandasAI often fails to produce valid matplotlib code.
This pipeline includes a fully reliable fallback that automatically selects the appropriate chart type:

Data Structure	Fallback Chart
1 numeric column	Histogram
≥2 numeric columns	Scatter plot
1 numeric + 1 categorical	Horizontal bar chart
Only categorical	Value-count bar chart
No plottable data	Empty placeholder
5. LLM-Generated Insights

GPT-4o-mini produces a one-paragraph business insight
based on the PandasAI summary

6. LangGraph Workflow

The pipeline is structured as:
SQLGenerator → SQLExecutor → (Clarifier?) → PandasAI → InsightSummary → Composer → END

his ensures deterministic, debuggable execution.

# 🔗 Pipeline Flow Description
Step 1 — Natural Language Input

Example query:
“List the top 10 customers by number of rentals.”

Step 2 — SQL Generation

GPT constructs SQL using dvdrental schema
Ensures valid syntax and correct JOIN structure

Step 3 — SQL Execution

Executes against PostgreSQL
Errors → Clarifier node regenerates a corrected query

Step 4 — PandasAI Summary

Detailed structured summary of the DataFrame
LLM-based explanation of dataset characteristics

Step 5 — Visualization

Tries PandasAI chart code
If it fails → triggers reliable fallback matplotlib code
Chart saved as temp_chart.png

Step 6 — Insight Generation

GPT extracts key business insights from the summary

Step 7 — Final Output Composition

A single formatted report

