### Directory Structure

Create a directory structure like this:

```
sql_generator/
|-- sql_generator/
|   |-- __init__.py
|   |-- generator.py
|-- setup.py
|-- README.md
```

### Code Files

1. Inside `generator.py`, place the code for generating SQL queries.

```python
import spacy

nlp = spacy.load("en_core_web_sm")

def generate_sql(description):
    doc = nlp(description)
    table = None
    columns = []
    conditions = []
    
    for token in doc:
        if token.dep_ == "dobj" and token.head.text.lower() == "select":
            columns.append(token.text)
        if token.dep_ == "prep" and token.head.text.lower() == "from":
            table = token.children.__next__().text
        if token.dep_ == "prep" and token.head.text.lower() == "where":
            conditions.append(f"{token.children.__next__().text} = '{token.children.__next__().text}'")
    
    if not table or not columns:
        return "Insufficient information to generate SQL query."
    
    sql_query = f"SELECT {', '.join(columns)} FROM {table}"
    
    if conditions:
        sql_query += f" WHERE {' AND '.join(conditions)}"
    
    return sql_query
```

2. Inside `__init__.py`, import the `generate_sql` function.

```python
from .generator import generate_sql
```

### Setup File

Create a `setup.py` file at the root level.

```python
from setuptools import setup, find_packages

setup(
    name='sql_generator',
    version='0.1',
    packages=find_packages(),
    install_requires=[
        'spacy>=3.0.0',
    ],
    author='Your Name',
    author_email='your.email@example.com',
    description='A simple SQL generator from natural language descriptions.',
)
```

### README.md

Write a `README.md` with instructions on how to install and use the package.

### Packaging

1. Navigate to the root directory (`sql_generator/`).
2. Run `python setup.py sdist` to create a source distribution.

### Installation

To install the package locally for testing, you can run:

```bash
pip install .
```

Or, to install the package from the source distribution:

```bash
pip install dist/sql_generator-0.1.tar.gz
```

### Usage

After installing, you can use it in your Python scripts like so:

```python
from sql_generator import generate_sql

description = "Select name, age from Users where id is 1"
print(generate_sql(description))
```

This should give you a good starting point for creating a Python package. You can extend this by adding more features, writing documentation, and eventually publishing it to the Python Package Index (PyPI).
