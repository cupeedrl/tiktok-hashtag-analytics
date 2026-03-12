# Tiktok-Hashtag-Analytics-Tracker
End-to-end Data Pipeline for TikTok Hashtag Analytics using Apache Airflow, PostgreSQL, and Metabase


# Table of Contents
- [Overview](#Overview)
- [Architecture](#Architecture)
- [Tech Stack](#Tech-stack)
- [Features](#Features)
- [Project Structure](#Froject-structure)
- [Database Schema](#database-schema)
- [Quick Start](#quick-start)
- [DAG Workflow](#dag-workflow)
- [Dashboard](#dashboard)
- [Security](#security)
- [Troubleshooting](#troubleshooting)
- [Author](#author)

# Overview

This project demonstrates a complete Data Engineering workflow for tracking and analyzing TikTok hashtag performance. It simulates a real-world analytics pipeline that:
Extracts data from a mock TikTok API (simulating social media metrics)
Transforms raw data into analytics-ready format using dimensional modeling
Loads processed data into a PostgreSQL Data Warehouse
Visualizes insights through an interactive Metabase BI Dashboard
Business Value:
Track trending hashtags in real-time
Analyze engagement metrics (views, likes, shares, comments)
Generate daily rankings and week-over-week growth
Enable data-driven decisions for marketing campaigns

# Architecture

<img width="1359" height="718" alt="image" src="https://github.com/user-attachments/assets/805febc9-1103-4efd-8759-f1317f3cc0b6" />

# Feature
Data Engineering
-Automated daily ETL pipeline (@daily schedule)
-Dimensional modeling (Star Schema)
-Idempotent pipelines (safe to re-run)
-Data quality checks built-in
-Error handling & retry logic
# Data Warehouse
-5 tables: Staging → Dimensions → Facts → Analytics
-4,000+ pre-populated dates (2020-2030)
-Foreign key constraints for data integrity
-UPSERT logic for incremental loads
# Dashboard & Analytics
-Top hashtags by views (ranking)
-Daily trend analysis
-Engagement rate calculations
-Week-over-week growth tracking
-Interactive Metabase BI dashboard
# Infrastructure
-Docker Compose for easy deployment
-Health checks for all services
-Automatic PID file cleanup
-Timezone fix for Windows compatibility
Auto-create Airflow connections

# Project Structure
tiktok-analytics-de/
├── .env                          # Environment variables (DO NOT COMMIT)
├── .env.example                  # Template for .env
├── .gitignore                    # Git ignore rules
├── docker-compose.yml            # Docker orchestration
├── requirements.txt              # Python dependencies
├── db.sql                        # Database schema & seed data
├── README.md                     # This file
├── start.bat                     # Quick start script (Windows)
│
├── dags/
│   ├── __init__.py
│   ├── mock_api.py               # Mock TikTok API simulation
│   └── tiktok_etl_dag.py         # Airflow DAG definition
│
├── scripts/
│   ├── __init__.py
│   ├── db_utils.py               # Database utilities
│   └── transform.sql             # SQL transformations
│
├── streamlit_app/                # Alternative dashboard (optional)
│   ├── __init__.py
│   ├── app.py
│   └── requirements.txt
│
└── logs/                         # Airflow logs (auto-generated)

# SQL Schema

-- Staging: Raw data from API
CREATE TABLE stg_hashtag_raw (
    id SERIAL PRIMARY KEY,
    hashtag VARCHAR(50),
    report_date DATE,
    views INTEGER,
    likes INTEGER,
    shares INTEGER,
    comments INTEGER,
    engagement_rate DECIMAL(10,4),
    extracted_at TIMESTAMP,
    loaded_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Dimension: Date reference
CREATE TABLE dim_date (
    date_id DATE PRIMARY KEY,
    day_of_week INT,
    day_name VARCHAR(10),
    month INT,
    month_name VARCHAR(10),
    quarter INT,
    year INT,
    is_weekend BOOLEAN
);

-- Dimension: Hashtag reference
CREATE TABLE dim_hashtag (
    hashtag_id SERIAL PRIMARY KEY,
    hashtag_name VARCHAR(50) UNIQUE,
    category VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_active BOOLEAN DEFAULT TRUE
);

-- Fact: Daily aggregated metrics
CREATE TABLE fact_hashtag_daily (
    fact_id SERIAL PRIMARY KEY,
    date_id DATE REFERENCES dim_date(date_id),
    hashtag_id INT REFERENCES dim_hashtag(hashtag_id),
    total_views INTEGER,
    total_likes INTEGER,
    total_shares INTEGER,
    total_comments INTEGER,
    engagement_rate DECIMAL(10,4),
    UNIQUE(date_id, hashtag_id)
);

-- Analytics: Pre-calculated rankings
CREATE TABLE agg_hashtag_rank (
    id SERIAL PRIMARY KEY,
    report_date DATE,
    hashtag VARCHAR(50),
    total_views INTEGER,
    daily_rank INTEGER,
    wow_growth DECIMAL(10,2),
    UNIQUE(report_date, hashtag)
);
# Dag flow
clean_staging → mock_api_data → load_dim_hashtag → transform_to_fact → build_hashtag_rank

# 👤 Author: Dat Chu Quoc 

# 🔗 GitHub: https://github.com/cupeedrl

# 📧 Gmail: whisperkuu.41@gmail.com

# 💼 LinkedIn: https://www.linkedin.com/in/dat-chu-quoc-583599387/

# 📄 License
MIT License - Feel free to use for learning and portfolio purposes!

# This project demonstrates:
-Data Modeling: Star Schema design for analytics
-ETL Pipeline: Airflow DAG with 5 tasks
-SQL Skills: Complex queries, JOINs, aggregations
-Docker: Container orchestration
-BI Tools: Metabase dashboard creation
-Best Practices: Idempotency, error handling, documentation

Last Updated: March 2026
