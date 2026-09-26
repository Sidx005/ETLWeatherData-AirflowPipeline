# Astro 

### What is Astro?

**Astro** is Astronomer's development and deployment platform for **Apache Airflow**.

It makes it easier to create, run, and manage Airflow projects without manually setting up all the Airflow components.

```text
Astro
  ↓
Airflow environment
  ↓
DAGs
  ↓
Tasks
```

### Why use Astro?

Normally, setting up Airflow requires configuring things like:

* Airflow
* PostgreSQL metadata database
* Scheduler
* Webserver
* Triggerer
* Docker
* Airflow dependencies/providers

Astro packages these together into a local development environment.

---

## Astro CLI

The **Astro CLI** is the command-line tool used to manage an Astro project.

Important commands:

```bash
astro dev init
```

Creates a new Astro Airflow project.

```bash
astro dev start
```

Starts the local Airflow environment.

```bash
astro dev stop
```

Stops the environment.

```bash
astro dev restart
```

Restarts the environment.

---

## Astro project structure

After:

```bash
astro dev init
```

you get a project similar to:

```text
ETLWeather/
├── dags/
├── include/
├── plugins/
├── tests/
├── Dockerfile
├── requirements.txt
└── airflow_settings.yaml
```

### Important folders

**`dags/`**

Contains your Airflow DAG files.

Example:

```text
dags/
└── weather_etl.py
```

**`include/`**

Used for additional files/data your DAGs may need.

**`plugins/`**

Used for custom Airflow plugins.

**`tests/`**

Contains tests for your DAGs.

---

## Docker + Astro

Astro runs Airflow using Docker containers.

Your environment had containers such as:

```text
webserver
scheduler
triggerer
postgres
```

You can see them with:

```bash
docker ps
```

Your setup looked like:

```text
Airflow Webserver
      │
      ├── Scheduler
      ├── Triggerer
      └── PostgreSQL
```

---

## Astro Runtime

The **Astro Runtime** is the Docker image containing Airflow and the required dependencies/providers.

Your Dockerfile became:

```dockerfile
FROM quay.io/astronomer/astro-runtime:12.1.1
```

This was important because your DAG uses:

```python
from airflow.providers.http.hooks.http import HttpHook
```

and the Runtime already included the required HTTP provider.

---

## Astro UI

After:

```bash
astro dev start
```

you can access Airflow locally at:

```text
http://localhost:8080
```

From there you can:

* View DAGs
* Trigger DAGs
* See task status
* View logs
* Inspect XComs
* Manage connections
* Manage variables
* Explore task execution

---

## Astro and PostgreSQL

Astro also runs a PostgreSQL container for the local Airflow environment.

Inside Docker, Airflow connects using:

```text
Host: postgres
Port: 5432
```

From your Mac, the PostgreSQL container was exposed as:

```text
localhost:14998
```

because Docker showed:

```text
127.0.0.1:14998 -> 5432
```

So:

```text
Inside Docker:
postgres:5432

From Mac:
localhost:14998
```

---

## Astro vs Airflow



**Airflow** = the workflow orchestration system.

**Astro** = the tooling/platform that makes running and managing Airflow easier.

For your project:

```text
Astro
  ↓
Airflow
  ↓
Weather ETL DAG
  ↓
Extract → Transform → Load
  ↓
PostgreSQL
```


> **Astro is Astronomer's platform and CLI for developing, running, and deploying Apache Airflow projects, with Docker-based local environments and preconfigured Airflow Runtime images.**
