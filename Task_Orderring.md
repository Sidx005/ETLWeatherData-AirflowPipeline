The order is decided by the **dependencies between tasks**, not simply by the order in which the functions appear in the file.

In your DAG:

```python
weather_data = extract_weather_data()
transformed_data = transform_weather_data(weather_data)
load_Weather_data(transformed_data)
```

When you pass one task's output into another task, Airflow automatically creates a dependency.

### Your dependency chain

```text
Extract
   ↓
Transform
   ↓
Load
```

Because:

```python
transform_weather_data(weather_data)
```

uses the output of:

```python
extract_weather_data()
```

Airflow knows:

> **Transform cannot run until Extract finishes.**

Similarly:

```python
load_Weather_data(transformed_data)
```

uses Transform's output, so:

> **Load cannot run until Transform finishes.**

### Important distinction

The **function definition order**:

```python
def extract_weather_data():
def transform_weather_data():
def load_Weather_data():
```

doesn't by itself determine execution order.

It's the **task dependencies** that determine the order.

For example, if you wrote:

```python
a = task_a()
b = task_b()
c = task_c()
```

without connecting them, Airflow doesn't automatically assume:

```text
A → B → C
```

You can explicitly create dependencies using:

```python
task_a >> task_b >> task_c
```

But with the **TaskFlow API**, passing outputs between tasks does this automatically:

```python
a = task_a()
b = task_b(a)
c = task_c(b)
```

creates:

```text
task_a → task_b → task_c
```

So in your weather DAG, **the data flow itself defines the execution order**.
