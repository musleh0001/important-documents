<div align="center">
    <h1>Pandas Tutorial</h1>
</div>

### Read CSV file

```python
# read without define index
df = pd.read_csv("data/file_name.csv")

# read with define index
df = pd.read_csv("data/file_name.csv", index_col="Column")
```

### Create DataFrame

```python
import pandas as pd

people = {
    "first": ["musleh", "mujahid", "musahid"],
    "email": ["musleh@khan.com", "mujahid@khan.com", "musahid@khan.com"]
}
people_df = pd.DataFrame(people)
```

### Basic DataFrame info

```python
# row, column
df.shape

# All column and their value type
df.info()

# single column
df['column_name']

# multi column
df[['col_name_1', 'col_name_2']]

# read by row
df.iloc[0]

# read by row multi
df.iloc[[0, 1]]

# read by row and col iloc[[row], [col]]
df.iloc[[0, 1], [0, 2]]

# read by row and col
df.loc[[0, 1], ['first', 'email']]

# read all column name
df.columns

# slice
df['col_name'][0:2]

# total column value 
df['col_name'].value_counts()

# use slice [inclusive]
df.loc[0:2, 'col_name_1':'col_name_3']

# set col as index
df.set_index("email", inplace=True)

# return index
df.index

# reset index
df.reset_index(inplace=True)

# read first five row
df.head()

# sort index
df.sort_index(ascending=False)
```

### Filter

```python
filt = (df['last'] == 'Khan') & (df['first'] == 'Musleh')
df[filt, 'email']
df[~filt, 'email']

# str 
filt = df['ProgrammingLanguage'].str.contains('Python', na=False)
df[filt, ['ProgrammingLanguage', 'Country']]
```


