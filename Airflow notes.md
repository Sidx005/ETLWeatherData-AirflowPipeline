## Airflow DAG — Core Concepts

### 1. Creating a DAG

A **DAG (Directed Acyclic Graph)** is the overall workflow in Airflow. We create it using the `DAG` class and define things like the DAG name, schedule, start date, and whether missed runs should be executed. The DAG acts as the **container for all the tasks** that belong to that workflow.

```python
with DAG(
    dag_id='weather_etl_pipeline',
    default_args=default_args,
    schedule_interval='@daily',
    catchup=False
) as dags:
```

In your project, the DAG represents the complete **weather ETL pipeline**.

---

### 2. Creating Tasks

A **task** is an individual unit of work inside a DAG. With the **TaskFlow API**, we can create a task by putting `@task` above a normal Python function. Airflow then takes that function and manages it as a task, including execution, logging, retries, and communication with other tasks.

Your DAG has three tasks:

```text
Extract → Transform → Load
```

For example:

```python
@task()
def extract_weather_data():
```

This Python function is no longer just a normal function; `@task` tells Airflow to treat it as an Airflow task.

---

### 3. Hooks

**Hooks** are Airflow's way of connecting to and interacting with external systems. Instead of writing all the connection logic yourself, Airflow provides hooks for services such as HTTP APIs, PostgreSQL, MySQL, AWS, etc.

Your DAG uses two hooks. `HttpHook` is used to communicate with the **Open-Meteo API**, while `PostgresHook` is used to communicate with **PostgreSQL**.

```text
HttpHook       → Open-Meteo API
PostgresHook   → PostgreSQL
```

Hooks also work with **Airflow Connections**, so things like host, port, username, password, and API connection details can be managed through Airflow rather than being hardcoded in the DAG.

---

### 4. XCom

**XCom (Cross-Communication)** is used for passing data between Airflow tasks. In your DAG, the Extract task gets the raw weather JSON and returns it. Airflow stores that returned value as an XCom, which the Transform task can receive. The Transform task then returns the cleaned weather data, which is again passed through XCom to the Load task.

```text
Extract
   ↓
XCom → raw weather JSON
   ↓
Transform
   ↓
XCom → transformed weather data
   ↓
Load
```

With the TaskFlow API, this happens naturally when you pass the output of one task into another:

```python
weather_data = extract_weather_data()

transformed_data = transform_weather_data(weather_data)

load_Weather_data(transformed_data)
```

---

### 5. Open-Meteo API

**Open-Meteo** is the external data source in this DAG. The Extract task uses `HttpHook` to call the API with the required latitude and longitude. The API responds with weather information in JSON format. The Transform task then takes the relevant information from this response and prepares it for the database.

So the data flow is:

```text
Open-Meteo
    ↓
Raw JSON
    ↓
Extract task
    ↓
XCom
    ↓
Transform task
    ↓
Cleaned data
    ↓
XCom
    ↓
Load task
    ↓
PostgreSQL
```

---

### 6. Overall Mental Model

Think of your Airflow code as building the pipeline in stages:

**Create the DAG** → defines the workflow.

**Create Tasks** → defines what work needs to be done.

**Use Hooks** → allows tasks to communicate with external systems.

**Use XCom** → allows tasks to pass data to each other.

**Schedule the DAG** → tells Airflow when the workflow should run.

For your project:

```text
             DAG
              │
       Weather ETL Pipeline
              │
      ┌───────┼────────┐
      ↓       ↓        ↓
   Extract  Transform  Load
      │       │        │
 HttpHook    XCom   PostgresHook
      │                │
Open-Meteo         PostgreSQL
```

This is the main Airflow structure you should remember: **DAG → Tasks → Hooks → XCom → external systems/data flow.**
