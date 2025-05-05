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
