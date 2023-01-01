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