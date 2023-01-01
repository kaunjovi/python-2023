# python-2023

Hello 2023. Starting a new notepad in the new year. 

## Folder structure 

## Dependency managment

## Testing and reporting 

## Deploying (on AWS?)

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