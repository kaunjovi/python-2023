# python-2023

Hello 2023. Starting a new notepad in the new year. 

## Folder structure 

- https://docs.python-guide.org/writing/structure/

### Modules

- As soon as you use import statements, you use modules. 
- These can be either built-in modules such as os and sys, 
- third-party modules you have installed in your environment, 
- or your project’s internal modules.
- 
- Keep module names short, lowercase, 
- and be sure to avoid using special symbols like the dot (.) or question mark (?). 
- 
- You could name your module my_spam.py, 
- but even our trusty friend the underscore, should not be seen that often in module names. 
- However, using other characters (spaces or hyphens) in module names will prevent importing (- is the subtract operator). 
- Try to keep module names short so there is no need to separate words. 
- And, most of all, don’t namespace with underscores; use submodules instead.

```
# OK
import library.plugin.foo
# not OK
import library.foo_plugin
```

### What happens when a module is *Not* found 

- the import modu statement will look for the proper file, 
- which is modu.py in the same directory as the caller
- If it is not found, the Python interpreter will search for modu.py in the “path” recursively 
- and raise an ImportError exception when it is not found.

### What happens when a module is found 
- When modu.py is found, the Python interpreter will execute the module in an isolated scope. 
- Any top-level statement in modu.py will be executed, including other imports if any. 
- Function and class definitions are stored in the module’s dictionary.

### How are import statement of Python different from include of C 

- An include file directive is used by the preprocessor to take all code found in the file and ‘copy’ it into the caller’s code. 
- It is different in Python: the included code is isolated in a module namespace, 
- which means that you generally don’t have to worry that the included code could have unwanted effects, e.g. override an existing function with the same name.


### Bad way of using import, # 1 

```
[...]
from modu import *
[...]
x = sqrt(4)  # Is sqrt part of modu? A builtin? Defined above?
```

### Bad way of using import, # 2

```
from modu import sqrt
[...]
x = sqrt(4)  # sqrt may be part of modu, if not redefined in between
```

### Correct way of using import 

```
import modu
[...]
x = modu.sqrt(4)  # sqrt is visibly part of modu's namespace
```

### Packages

- https://docs.python-guide.org/writing/structure/
- Any directory with an __init__.py file is considered a Python package. 
- The different modules in the package are imported in a similar manner as plain modules, 
- but with a special behavior for the __init__.py file, which is used to gather all package-wide definitions.
- A file modu.py in the directory pack/ is imported with the statement import pack.modu. 
- This statement will look for __init__.py file in pack and execute all of its top-level statements. 
- Then it will look for a file named pack/modu.py and execute all of its top-level statements. 
- After these operations, any variable, function, or class defined in modu.py is available in the pack.modu namespace.
- 
- Do not add too much code in __init__.py 
- When the project complexity grows, there may be sub-packages and sub-sub-packages in a deep directory structure. 
- In this case, importing a single item from a sub-sub-package will require executing all __init__.py files met while traversing the tree.
- 
- Leaving an __init__.py file empty is considered normal and even good practice, 
- if the package’s modules and sub-packages do not need to share any code.
- 
- import very.deep.module as mod. 
- This allows you to use mod in place of the verbose repetition of very.deep.module.

## Mutable and immutable types 

- https://docs.python-guide.org/writing/structure/

## Dynamic typing 

- https://docs.python-guide.org/writing/structure/

## Context Managers 

- https://docs.python-guide.org/writing/structure/

```
with open('file.txt') as f:
    contents = f.read()
```

## Create a Virtual Environment 

- https://towardsdatascience.com/the-good-way-to-structure-a-python-project-d914f27dfcc9
- Maintain dependencies isolated
- Share your dependencies with other people.

## Create a separate directory for tests 

- https://towardsdatascience.com/the-good-way-to-structure-a-python-project-d914f27dfcc9
- Pytest? Or is there something better?
- if you create a file called greetings.py and 
- another one to write some tests called test_greetings.py.
- 
- ModuleNotFoundError
- Move tests in a directory and make it a package (__init__.py)

## Dependency managment

## Testing and reporting 

## Deploying (on AWS?)

- https://docs.aws.amazon.com/lambda/latest/dg/lambda-python.html
- You can run Python code in AWS Lambda. 
- Lambda provides runtimes for Python that run your code to process events. 
- Your code runs in an environment that includes the SDK for Python (Boto3),
    - What about the dependencies ?  
- with credentials from an AWS Identity and Access Management (IAM) role that you manage.
- create an execution role
- create a Python function
- Sample Lambda applications in Python
    - https://github.com/awsdocs/aws-lambda-developer-guide/tree/main/sample-apps/blank-python

## ChatGPT

https://markwschaefer.medium.com/20-entertaining-uses-of-chatgpt-you-never-knew-were-possible-3bc2644d4507

The moderator can say, “And here’s a question for you from our AI?”

## Pandas 

- 4 Techniques for Scaling Pandas to Large Datasets
- https://towardsdatascience.com/4-techniques-for-scaling-pandas-to-large-datasets-acf8805d30eb
- The main reason is that Pandas does in-memory analytics 
- so if the dataset is larger than memory, it becomes very difficult to use Pandas.
- Some operations make intermediate copies. 
- In order to have a smooth experience with Pandas, the dataset should be relatively smaller than the memory.
- Pandas uses a single CPU core to execute the operations. 
- On very large datasets, this makes Pandas slower than the tools that offer distributed computing.
- For very large datasets such as Dask, Vaex, Modin, and Datatable.
- Do you really need all of the dataset?
- The dataset might contain redundant columns or we might simply need only a few columns for a particular task.
- Instead of reading the entire dataset and then filtering the required columns, a better approach is to only read the columns we need.

### Generate some data 

```
import pandas as pd
import numpy as np

df = pd.DataFrame(np.random.randint(0, 100, size=(10000000, 50)))
df = df.rename(columns={i:f"x_{i}" for i in range(50)})
df["category"] = ["A", "B", "C", "D"] * 2500000
```

### How much memory does the code use 

```
np.round(df.memory_usage().sum() / 10**9, 2)

# output
4.08
```

### Time taken to filter  

```
%time df[df["category"]=="A"]

# output
CPU times: user 519 ms, sys: 911 ms, total: 1.43 s
Wall time: 2.43 s
```

### Time taken to sort 

```
%time df.sort_values(by=["x_0", "x_1"])

# output
CPU times: user 2.84 s, sys: 1.19 s, total: 4.03 s
Wall time: 4.52 s
```

### categorical data

- categorical feature with low-cardinality, using the category data type
- Low-cardinality means having very few distinct values compared to the total number of values. 
- The category column in our DataFrame has only 4 distinct values compared to a total of 10 million.

### Downcast numeric columns

- the default integer data type in Pandas is “int64”, 
- which can store numbers between -9,223,372,036,854,775,808 and 9,223,372,036,854,775,807
- We can downcast integer columns to int16 or int8 to reduce memory usage
- use the to_numeric function, which can do the proper downcast for us
- Check memory usage of a integer columns

```
df["x_0"].dtypes
# output
dtype('int64')

np.round(df["x_0"].memory_usage() / 10**6, 2)
# output
80.0
```

- Now downcast it. 

```
df["x_0"] = pd.to_numeric(df["x_0"], downcast="unsigned")

df["x_0"].dtypes
# output
dtype('uint8')

np.round(df["x_0"].memory_usage() / 10**6, 2)
# output
10.0
```

### Use special data structures for sparse data

## type-hints

- What are these ? 
- https://towardsdatascience.com/the-good-way-to-structure-a-python-project-d914f27dfcc9


## Creating a Plotly Graph with 95% CI in Python

- https://python.plainenglish.io/a-step-by-step-guide-to-creating-a-plotly-graph-with-95-ci-in-python-3b7d06ee3f6b
- install pandas and plotly

```
import pandas as pd
import plotly.graph_objects as go
```