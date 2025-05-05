# Docker and SQL Project

This project demonstrates how to set up a PostgreSQL database with Docker, ingest data into it, and perform basic SQL operations. The project is part of the Data Engineering Zoomcamp and focuses on handling NYC Taxi data.

## Table of Contents
- [Overview](#overview)
- [Setup Instructions](#setup-instructions)
  - [Prerequisites](#prerequisites)
  - [Clone the Repository](#clone-the-repository)
  - [Setting Up PostgreSQL with Docker Compose](#setting-up-postgresql-with-docker-compose)
  - [Build the Docker Image for Data Ingestion](#build-the-docker-image-for-data-ingestion)
- [Data Ingestion](#data-ingestion)
  - [Download the Data](#download-the-data)
  - [Run the Data Ingestion Script Locally](#run-the-data-ingestion-script-locally)
  - [Run the Script Using Docker](#run-the-script-using-docker)
- [PostgreSQL and pgAdmin Usage](#postgresql-and-pgadmin-usage)
- [SQL Queries](#sql-queries)
- [References](#references)

---

## Overview

The project focuses on deploying PostgreSQL using Docker and Docker Compose. It includes tools and scripts for:
- Downloading and preparing NYC Taxi data.
- Ingesting data into a PostgreSQL database.
- Querying the data using pgAdmin and command-line tools.

---

## Setup Instructions

### Prerequisites
- Docker and Docker Compose must be installed on your system.
- Python 3.9+ should be available if running scripts locally.

### Clone the Repository
```bash
git clone https://github.com/DataTalksClub/data-engineering-zoomcamp.git
cd data-engineering-zoomcamp/01-docker-terraform/2_docker_sql
```

### Setting Up PostgreSQL with Docker Compose

1. Use the provided docker-compose.yaml file:
```yaml
services:
  pgdatabase:
    image: postgres:13
    environment:
      - POSTGRES_USER=root
      - POSTGRES_PASSWORD=root
      - POSTGRES_DB=ny_taxi
    volumes:
      - "./ny_taxi_postgres_data:/var/lib/postgresql/data:rw"
    ports:
      - "5432:5432"
  pgadmin:
    image: dpage/pgadmin4
    environment:
      - PGADMIN_DEFAULT_EMAIL=admin@admin.com
      - PGADMIN_DEFAULT_PASSWORD=root
    ports:
      - "8080:80"
```

2. Start the services:
```bash
docker-compose up -d
```

### Build the Docker Image for Data Ingestion

1. Use the provided Dockerfile:
```Dockerfile
FROM python:3.9.1

RUN apt-get install wget
RUN pip install pandas sqlalchemy psycopg2

WORKDIR /app
COPY ingest_data.py ingest_data.py 

ENTRYPOINT [ "python", "ingest_data.py" ]
```

2. Build the Docker image:

```bash
docker build -t taxi_ingest:v001 .
```

## Data Ingestion

### Download the Data

```bash
wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz
```

### Run the Data Ingestion Script Locally
```bash
python ingest_data.py \
  --user=root \
  --password=root \
  --host=localhost \
  --port=5432 \
  --db=ny_taxi \
  --table_name=yellow_taxi_trips \
  --url=https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz
```

### Run the Script Using Docker
```bash
docker run -it \
  --network=pg-network \
  taxi_ingest:v001 \
    --user=root \
    --password=root \
    --host=pg-database \
    --port=5432 \
    --db=ny_taxi \
    --table_name=yellow_taxi_trips \
    --url=https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz
```

## PostgreSQL and pgAdmin Usage

### Access pgAdmin
1. Open your browser and navigate to `http://localhost:8080`.
Use the default credentials:
Email: `admin@admin.com`
Password: `root`

## SQL Queries

Here are some useful SQL queries to get started:

### Query 1: Count Trips Per Day
```SQL
SELECT
    CAST(tpep_dropoff_datetime AS DATE) AS day,
    COUNT(1) AS trip_count
FROM yellow_taxi_trips
GROUP BY day
ORDER BY trip_count DESC;
```

### Query 2: Join Trips with Zone Information

```SQL
SELECT
    tpep_pickup_datetime,
    tpep_dropoff_datetime,
    total_amount,
    zpu.Zone AS pickup_zone,
    zdo.Zone AS dropoff_zone
FROM yellow_taxi_trips AS t
JOIN zones AS zpu ON t.PULocationID = zpu.LocationID
JOIN zones AS zdo ON t.DOLocationID = zdo.LocationID
LIMIT 100;
```

## References

* [NYC Taxi Data](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)
* [PostgreSQL Documentation](https://www.postgresql.org/docs/)
* [pgAdmin Documentation](https://www.pgadmin.org/docs/)
