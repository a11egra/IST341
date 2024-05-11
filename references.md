# References
## ChatGPT Conversations
### Splitting Programs into Separate Files
me
>what's wrong here? full_query is a pandas dataframe: 

```python
qSAH = full_query.groupby(full_query['Program 1 Category'] == 'SAH')

qSAH.info()
qSAH
```


ChatGPT
>It seems like you're trying to group your DataFrame full_query by a condition on the column 'Program 1 Category' being equal to 'SAH'. However, the syntax you've used might not work as expected.

>Here's how you can fix it:

```python
import pandas as pd

# Assuming 'Program 1 Category' is a column in your DataFrame full_query

# Grouping by the condition 'Program 1 Category' == 'SAH'
qSAH = full_query.groupby(full_query['Program 1 Category'] == 'SAH')

# If you want to get the count of rows in each group:
qSAH_info = qSAH.size()

# If you want to see the information of the grouped DataFrame:
print(qSAH_info)

# To see the grouped DataFrame itself, you can iterate through it or access individual groups:
for group_name, group_df in qSAH:
    print("Group:", group_name)
    print(group_df)
```

>In this code:

>`full_query.groupby(full_query['Program 1 Category'] == 'SAH')` groups the DataFrame `full_query` by the condition specified inside the groupby function.
`qSAH_info` contains the count of rows in each group.

>Printing `qSAH_info` will give you information about the size of each group.
You can iterate through the `qSAH` object to access individual groups and their corresponding DataFrames.

me

>i want qSAH to be a new, complete dataframe

ChatGPT
>To create a new DataFrame containing only the rows where the value in the 'Program 1 Category' column is 'SAH', you can filter `full_query` using the condition and assign the result to `qSAH`. Here's how you can do it:

```python
import pandas as pd

# Assuming 'Program 1 Category' is a column in your DataFrame full_query

# Filter the DataFrame to include only rows where 'Program 1 Category' is 'SAH'
qSAH = full_query[full_query['Program 1 Category'] == 'SAH']

# Now qSAH is a new DataFrame containing only rows where 'Program 1 Category' is 'SAH'

```

>In this code:

>`full_query[full_query['Program 1 Category'] == 'SAH']` filters full_query to include only the rows where the value in the 'Program 1 Category' column is 'SAH'.
>The result is assigned to `qSAH`, which becomes a new DataFrame containing only the rows where 'Program 1 Category'  is 'SAH'.


### Index Mapping & Looping

me
>i have created an indexing definition, but i now want to apply this to other dataframes (without re-creating the index). how can i do that?

```
def map_values(df, column_name):
    unique_values = df[column_name].unique()
    mapping = {value: idx for idx, value in enumerate(unique_values)}
    index_mapping = {idx: value for value, idx in mapping.items()}
    df[column_name] = df[column_name].map(mapping)
    return index_mapping

COUNTRY_INDEX = map_values(query_clean, 'Country')
print(COUNTRY_INDEX)

CITIZEN_INDEX = map_values(query_clean, 'Citizenship Status')
print(CITIZEN_INDEX)

GEO_INDEX = map_values(query_clean, 'Continent')
print(GEO_INDEX)

TYPE_INDEX = map_values(query_clean, 'Degree Type')
print(TYPE_INDEX)

TERM_INDEX = map_values(query_clean, 'Entry Semester')
print(TERM_INDEX)
```

ChatGPT
>To apply the indexing definition to other dataframes without re-creating the index, you can modify the `map_values` function to accept an additional argument for the mapping. This way, you can reuse the mapping from one dataframe on another. Here's how you can modify the function:

```
def map_values(df, column_name, mapping=None):
    if mapping is None:
        unique_values = df[column_name].unique()
        mapping = {value: idx for idx, value in enumerate(unique_values)}
    index_mapping = {idx: value for value, idx in mapping.items()}
    df[column_name] = df[column_name].map(mapping)
    return mapping, index_mapping

COUNTRY_MAPPING, COUNTRY_INDEX = map_values(query_clean, 'Country')
print(COUNTRY_INDEX)

CITIZEN_MAPPING, CITIZEN_INDEX = map_values(query_clean, 'Citizenship Status', COUNTRY_MAPPING)
print(CITIZEN_INDEX)

GEO_MAPPING, GEO_INDEX = map_values(query_clean, 'Continent', COUNTRY_MAPPING)
print(GEO_INDEX)

TYPE_MAPPING, TYPE_INDEX = map_values(query_clean, 'Degree Type', COUNTRY_MAPPING)
print(TYPE_INDEX)

TERM_MAPPING, TERM_INDEX = map_values(query_clean, 'Entry Semester', COUNTRY_MAPPING)
print(TERM_INDEX)
```

>In this modified function, the mapping argument is optional. If provided, it will be used for mapping values; otherwise, a new mapping will be created based on the unique values in the dataframe column. This allows you to reuse the same mapping for different dataframes.

### Exporting Feature Importances

Me: 


>what is happening here? 

>code:
```python
IMP_LIST = [CISAT_model.feature_importances_,
            DBOS_model.feature_importances_,
            DPE_model.feature_importances_,
            DSM_model.feature_importances_,
            IMS_model.feature_importances_,
            SAH_model.feature_importances_,
            SCGH_model.feature_importances_,
            SES_model.feature_importances_]
DIVISIONS = ['CISAT', 'DBOS', 'DPE', 'DSM', 'IMS', 'SAH', 'SCGH', 'SES']
new_list = []

for item in DIVISIONS:
  new_var = item + "_features"
  new_list.append(new_var)
print(new_list)
print(IMP_LIST[i])
for i in range(0,9):
  print(new_list[i])
  new_list[i] = pd.DataFrame(IMP_LIST[i], columns=['Feature', 'Importance'])
  IMP_LIST[i].to_csv(f'{name[i]}.csv', index=False)
  new_list[i].info()
```
results:
```python
['CISAT_features', 'DBOS_features', 'DPE_features', 'DSM_features', 'IMS_features', 'SAH_features', 'SCGH_features', 'SES_features']
[0.075762   0.08892351 0.18435615 0.28373743 0.04796767 0.06645551
 0.02289844 0.03655184 0.00719995 0.03700173 0.06270155 0.08644423]
CISAT_features
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-32-15a772689260> in <cell line: 11>()
     11 for i in range(0,9):
     12   print(new_list[i])
---> 13   new_list[i] = pd.DataFrame(IMP_LIST[i], columns=['Feature', 'Importance'])
     14   IMP_LIST[i].to_csv(f'{name[i]}.csv', index=False)
     15   new_list[i].info()

2 frames
/usr/local/lib/python3.10/dist-packages/pandas/core/internals/construction.py in _check_values_indices_shape_match(values, index, columns)
    406         passed = values.shape
    407         implied = (len(index), len(columns))
--> 408         raise ValueError(f"Shape of passed values is {passed}, indices imply {implied}")
    409 
    410 

ValueError: Shape of passed values is (12, 1), indices imply (12, 2)
```


ChatGPT: 


>It looks like there's an issue with the shape of the data you're trying to pass into the `pd.DataFrame` constructor. The error message indicates that the shape of the values you're passing (`IMP_LIST[i]`) is `(12, 1)`, but the columns you're specifying (`['Feature', 'Importance']`) imply a shape of `(12, 2)`.

>The error likely occurred because the `feature_importances_` attribute of your models (`CISAT_model`, `DBOS_model`, etc.) is returning a 1D array instead of a 2D array with two columns. To fix this, you should reshape the `feature_importances_` array to have two columns before passing it to `pd.DataFrame`.

>Here's how you can modify your code to reshape the `feature_importances_` array:

```python
for i in range(0, 8):  # Changed range to match the length of IMP_LIST and new_list
    print(new_list[i])
    # Reshape the 1D array to a 2D array with one column
    reshaped_importances = np.reshape(IMP_LIST[i], (-1, 1))
    new_list[i] = pd.DataFrame(reshaped_importances, columns=['Importance'])
    new_list[i].insert(0, 'Feature', new_list[i].index)  # Insert a 'Feature' column with index values
    new_list[i].to_csv(f'{DIVISIONS[i]}.csv', index=False)  # Use DIVISIONS list for file names
    new_list[i].info()

```

>In this modified code, `reshaped_importances` is a 2D array with one column, and we insert it into the DataFrame with a 'Feature' column containing the index values. Adjustments have been made to use `DIVISIONS` for file naming and to iterate up to 8 to match the length of `IMP_LIST` and `new_list`.

You: 


>i want to add a column with the name of the feature defined by the index: 

```
dictionary COL_INDEX is 
```
```python
{'Enrolled': 0, 'Entry Semester': 1, 'Citizenship Status': 2, 'Continent': 3, 'Country': 4, 'Degree Type': 5, 'Degree 1': 6, 'Dual Degree': 7, 'Campus Tour': 8, 'Open House': 9, 'Spring Preview': 10, "President's Reception": 11, 'Virtual Call': 12}
```



ChatGPT: 


>To add a column with the names of the features based on the index, you can use the `COL_INDEX` dictionary to map the index to the feature name. Here's how you can modify your code to include this:

```python
COL_INDEX = {
    'Enrolled': 0,
    'Entry Semester': 1,
    'Citizenship Status': 2,
    'Continent': 3,
    'Country': 4,
    'Degree Type': 5,
    'Degree 1': 6,
    'Dual Degree': 7,
    'Campus Tour': 8,
    'Open House': 9,
    'Spring Preview': 10,
    "President's Reception": 11,
    'Virtual Call': 12
}
for i in range(0, 8):  # Adjusted range to match the length of IMP_LIST and new_list
    print(new_list[i])
    reshaped_importances = np.reshape(IMP_LIST[i], (-1, 1))
    new_df = pd.DataFrame(reshaped_importances, columns=['Importance'])
    feature_name = [key for key, value in COL_INDEX.items() if value == i][0]
    new_df.insert(0, 'Feature', feature_name)
    new_list[i] = new_df
    new_list[i].to_csv(f'{DIVISIONS[i]}.csv', index=False)
    new_list[i].info()
```

>In this modified code, `feature_name` is extracted from the `COL_INDEX` dictionary based on the current index `i` in the loop, and then inserted as a new column in the DataFrame. Adjustments have been made to use `DIVISIONS` for file naming and to iterate up to 8 to match the length of `IMP_LIST` and `new_list`.

You: 

>i still also want the column with the index number


ChatGPT: 



>To add a column with both the feature name and its index number, you can modify the code to include the index number along with the feature name. Here's the updated code:
```python
COL_INDEX = {
    'Enrolled': 0,
    'Entry Semester': 1,
    'Citizenship Status': 2,
    'Continent': 3,
    'Country': 4,
    'Degree Type': 5,
    'Degree 1': 6,
    'Dual Degree': 7,
    'Campus Tour': 8,
    'Open House': 9,
    'Spring Preview': 10,
    "President's Reception": 11,
    'Virtual Call': 12
}
for i in range(0, 8):  # Adjusted range to match the length of IMP_LIST and new_list
    print(new_list[i])
    reshaped_importances = np.reshape(IMP_LIST[i], (-1, 1))
    new_df = pd.DataFrame(reshaped_importances, columns=['Importance'])
    feature_name = [key for key, value in COL_INDEX.items() if value == i][0]
    new_df.insert(0, 'Feature', feature_name)
    new_df.insert(1, 'Index', i)  # Inserting the index number column
    new_list[i] = new_df
    new_list[i].to_csv(f'{DIVISIONS[i]}.csv', index=False)
    new_list[i].info()
```



>In this modified code, the index number `i` is inserted as a new column named 'Index' in the DataFrame along with the 'Feature' column containing the feature name. Adjustments have been made to use `DIVISIONS` for file naming and to iterate up to 8 to match the length of `IMP_LIST` and `new_list`.

You: 



>all the indexes printed as 0

ChatGPT: 

>It seems there was an issue with how the index was being inserted into the DataFrame. To ensure that each row has the correct index corresponding to its feature, you can modify the code like this:
```python
for i in range(0, 8):  # Adjusted range to match the length of IMP_LIST and new_list
    print(new_list[i])
    reshaped_importances = np.reshape(IMP_LIST[i], (-1, 1))
    new_df = pd.DataFrame(reshaped_importances, columns=['Importance'])
    feature_name = [key for key, value in COL_INDEX.items() if value == i][0]
    indexes = [i] * len(new_df)  # Create a list of indexes corresponding to the length of the DataFrame
    new_df.insert(0, 'Feature', feature_name)
    new_df.insert(1, 'Index', indexes)  # Inserting the index numbers column
    new_list[i] = new_df
    new_list[i].to_csv(f'{DIVISIONS[i]}.csv', index=False)
    new_list[i].info()
```
In this updated code, `indexes` is a list containing the index number `i` repeated for each row in the DataFrame, ensuring that each row has the correct index value.

You: 


>i want the features column to print the same way it does in this statement: 
```python
j = 0
for IMP in IMP_LIST:
    print(f"\nFeature importances for {DIVISIONS[j]}:")
    for i, importance in enumerate(IMP):
        perc = importance * 100
        print(f"  {COLUMNS[i+1]:>21s} has {perc:>5.1f}% of the decision-making importance.")
    j += 1
```


ChatGPT





>To achieve a similar output format as in your loop with `IMP_LIST`, you can modify the code to print the features and their importances for each division. Here's how you can do it:
```python
for i in range(0, 8):  # Adjusted range to match the length of IMP_LIST and new_list
    print(f"\nFeature importances for {DIVISIONS[i]}:")
    for j, importance in enumerate(IMP_LIST[i]):
        perc = importance * 100
        feature_name = [key for key, value in COL_INDEX.items() if value == j][0]
        print(f"  {feature_name:>21s} has {perc:>5.1f}% of the decision-making importance.")
```
This code will print the feature importances for each division, using the `COL_INDEX` dictionary to get the feature names based on their index. Adjustments have been made to use `DIVISIONS` for division names and to iterate up to 8 to match the length of `IMP_LIST`.

You: 



>what does this error mean? 

```python
['CISAT_features', 'DBOS_features', 'DPE_features', 'DSM_features', 'IMS_features', 'SAH_features', 'SCGH_features', 'SES_features']
[0.03973426 0.11982044 0.09855076 0.11003803 0.09360607 0.15170658
 0.00816767 0.05009331 0.03544127 0.12631837 0.09768406 0.06883917]

DBOS_features
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-46-d8ab46240a7c> in <cell line: 12>()
     17 
     18     for i, importance in enumerate(IMP): # construct the new dataframe
---> 19         new_df.insert(0, 'Feature', COLUMNS[i+1])
     20         new_df.insert(1, 'Importance', importance)
     21 
/usr/local/lib/python3.10/dist-packages/pandas/core/frame.py in insert(self, loc, column, value, allow_duplicates)
   4770         if not allow_duplicates and column in self.columns:
   4771             # Should this be a different kind of error??
-> 4772             raise ValueError(f"cannot insert {column}, already exists")
   4773         if not isinstance(loc, int):
   4774             raise TypeError("loc must be int")

ValueError: cannot insert Feature, already exists
```

ChatGPT: 


>The error message "ValueError: cannot insert Feature, already exists" indicates that you are trying to insert a column named 'Feature' into a DataFrame, but a column with that name already exists in the DataFrame.

>This error occurs in the line `new_df.insert(0, 'Feature', COLUMNS[i+1])`, where `COLUMNS[i+1]` is being inserted as a new column named 'Feature'. It seems that `COLUMNS` might already contain a column named 'Feature', which is causing the error.

>To fix this issue, you can either rename the existing 'Feature' column in `new_df` or use a different name for the new column you're inserting. Here's an example of how you could rename the existing 'Feature' column before inserting the new one:

```python
# Rename the existing 'Feature' column
new_df = new_df.rename(columns={'Feature': 'Old_Feature'})

# Insert the new column with a different name
new_df.insert(0, 'Feature', COLUMNS[i+1])
```

>Alternatively, if you don't need the existing 'Feature' column, you can drop it before inserting the new one:

```python
# Drop the existing 'Feature' column
new_df = new_df.drop(columns=['Feature'])

# Insert the new column
new_df.insert(0, 'Feature', COLUMNS[i+1])
```

>Adjust the code based on your specific requirements and DataFrame structure.

You: 
>how do i clear new_df?

ChatGPT: 


>To clear the contents of a DataFrame `new_df`, you can use the `pd.DataFrame` constructor to create an empty DataFrame with the same columns. Here's how you can do it:

```python
new_df = pd.DataFrame(columns=new_df.columns)
```

This line of code creates a new DataFrame with the same columns as `new_df` but with no rows, effectively clearing its contents.

You: 


>no, i just want to empty that variable completely

ChatGPT: 

>If you want to empty the `new_df` variable completely so that it no longer refers to any DataFrame object, you can simply assign `None` to it. This will remove the reference to the DataFrame, allowing the memory occupied by the DataFrame to be reclaimed by the garbage collector.

```python
new_df = None
```



>After executing this line of code, `new_df` will no longer refer to any DataFrame object, effectively emptying the variable.

You: 


>what's wrong here? ValueError: cannot insert Importance, already exists

```python
j = 0
for IMP in IMP_LIST: 
    print(f"\n\n{new_list[i]}") # sense check
    new_df = pd.DataFrame(reshaped_importances, columns=['Importance']) #create dataframe
    for i, importance in enumerate(IMP): # construct the new dataframe
        new_df.insert(0, 'Feature', COLUMNS[i+1])
        new_df.insert(1, 'Importance', importance)
    new_df.to_csv(f'{DIVISIONS[i]}.csv', index=False) # export
    new_list[IMP] = new_df # store as a variable
    j+=1
    new_df = pd.DataFrame()
```

ChatGPT: 


>The error "ValueError: cannot insert Importance, already exists" occurs because you're trying to insert a column named 'Importance' into `new_df`, but a column with that name already exists. This happens because you're using the same `new_df` DataFrame object in each iteration of the loop without clearing it properly.

>To fix this, you can create a new DataFrame object for each division inside the loop. Here's the corrected code:

```python
j = 0
for IMP in IMP_LIST: 
    print(f"\n\n{new_list[j]}") # sense check

    new_df = pd.DataFrame(columns=['Feature', 'Importance']) # create an empty DataFrame

    for i, importance in enumerate(IMP): # construct the new dataframe
        new_df.loc[len(new_df)] = [COLUMNS[i+1], importance]

    new_df.to_csv(f'{DIVISIONS[j]}.csv', index=False) # export
    new_list[j] = new_df # store as a variable

    j += 1
```



>In this corrected code, `new_df` is created as an empty DataFrame at the beginning of each iteration, ensuring that it doesn't have any existing columns or data that could cause the "already exists" error.

### Referencing Indexes
Me:
>what am i doing wrong in my print statement? 

```python
mappings = {}
for column_name in ['Country', 'Citizenship Status', 'Continent', 'Degree Type', 'Entry Semester']:
    mappings[column_name], _ = map_values(query_clean, column_name, mappings.get(column_name))
    CISAT_clean[column_name] = CISAT_clean[column_name].map(mappings[column_name])
    DBOS_clean[column_name] = DBOS_clean[column_name].map(mappings[column_name])
    DPE_clean[column_name] = DPE_clean[column_name].map(mappings[column_name])
    DSM_clean[column_name] = DSM_clean[column_name].map(mappings[column_name])
    IMS_clean[column_name] = IMS_clean[column_name].map(mappings[column_name])
    SAH_clean[column_name] = SAH_clean[column_name].map(mappings[column_name])
    SCGH_clean[column_name] = SCGH_clean[column_name].map(mappings[column_name])
    SES_clean[column_name] = SES_clean[column_name].map(mappings[column_name])

# mapping degrees
def degree_indexing(df):
  DEG_INDEX = map_values(df, 'Degree 1',None)
  print(f"the degree index is {DEG_INDEX}.")
  return DEG_INDEX

query_DEG_INDEX = degree_indexing(query_clean)
CISAT_DEG_INDEX = degree_indexing(CISAT_clean)
DBOS_DEG_INDEX = degree_indexing(DBOS_clean)
DPE_DEG_INDEX = degree_indexing(DPE_clean)
DSM_DEG_INDEX = degree_indexing(DSM_clean)
IMS_DEG_INDEX = degree_indexing(IMS_clean)
SAH_DEG_INDEX = degree_indexing(SAH_clean)
SCGH_DEG_INDEX = degree_indexing(SCGH_clean)
SES_DEG_INDEX = degree_indexing(SES_clean)
```
```python
 print(mappings{'DBOS':'Country'})
```
ChatGPT:
>The error in your print statement is a syntax error. You should use square brackets `[]` to access the value in the `mappings` dictionary, not curly braces `{}`. Additionally, you should use a comma `,` to separate the arguments in the `print` function. Here's the corrected print statement:

```python
print(mappings['DBOS']['Country'])
```

>This assumes that `mappings` is a dictionary of dictionaries, where the keys are column names ('DBOS', etc.) and the values are dictionaries with keys like 'Country', 'Citizenship Status', etc. If `mappings` is structured differently, you'll need to adjust the key access accordingly.

Me:
>now i have this: 
```python
{'United States': 0, 'India': 1, 'Nigeria': 2, 'Ghana': 3, 'The Gambia': 4, 'Kenya': 5, 'Iran': 6, 'China': 7, 'Japan': 8, 'United Kingdom': 9, 'Zimbabwe': 10, 'Uganda': 11, 'Taiwan': 12, 'South Korea': 13, 'Bangladesh': 14, 'Pakistan': 15, 'Jamaica': 16, 'Philippines': 17, 'Saudi Arabia': 18, 'Colombia': 19, 'Eswatini': 20, 'Finland': 21, 'Saint Lucia': 22, 'Kuwait': 23, 'Turkey': 24, 'Hong Kong S.A.R.': 25, 'Romania': 26, 'Germany': 27, 'Burundi': 28, 'Australia': 29, 'United Arab Emirates': 30, 'Austria': 31, 'Russia': 32, 'Liberia': 33, 'Spain': 34, 'Malaysia': 35, 'South Africa': 36, 'Thailand': 37, 'Vietnam': 38, 'Israel': 39, 'Sweden': 40, 'Nepal': 41, 'Canada': 42, 'Italy': 43, 'Congo (Kinshasa)': 44, 'Uzbekistan': 45, 'Rwanda': 46, 'Singapore': 47, 'Jordan': 48, 'Indonesia': 49, 'Ireland': 50, 'Brazil': 51, 'Tanzania': 52, 'Sierra Leone': 53, 'Greece': 54, 'Cambodia': 55, 'Kyrgyzstan': 56, "Cote D'Ivoire": 57, 'Cameroon': 58, 'Sri Lanka': 59, 'Kazakhstan': 60, 'Egypt': 61, 'Ethiopia': 62}
``` 
>how do i input a number and get out the country?

ChatGPT:
>To get the country corresponding to a number in your mappings dictionary, you can reverse the dictionary mapping. Here's how you can do it:

```python
# Assuming your mappings dictionary is named country_mappings
country_mappings = {'United States': 0, 'India': 1, 'Nigeria': 2, ...}

# Reverse the dictionary to map numbers to countries
number_to_country = {v: k for k, v in country_mappings.items()}

# Input a number
number = 3

# Get the country for the input number
country = number_to_country.get(number)

# Print the result
print(country)
```

>This code will print the country corresponding to the input number. If the number does not exist in the dictionary, `None` will be returned.

### Counter and Mapping

Me:
>in these dataframes: `clean_list = [CISAT_clean, DBOS_clean, DPE_clean, DSM_clean, IMS_clean, SAH_clean, SCGH_clean, SES_clean]`, i want to find the most common reoccurring values in these columns: `columns = ['Country', 'Citizenship Status', 'Continent', 'Degree Type', 'Entry Semester']`, and then store the data with the division: `DIVISIONS = ['CISAT', 'DBOS', 'DPE', 'DSM', 'IMS', 'SAH', 'SCGH', 'SES']`, count, and country translated back to a name using my mapping function 

```python
def map_values(df, column_name, mapping):
    if mapping is None:
        unique_values = df[column_name].unique()
        mapping = {value: idx for idx, value in enumerate(unique_values)}
    index_mapping = {idx: value for value, idx in mapping.items()}
    df[column_name] = df[column_name].map(mapping)
    return mapping, index_mapping

# Create mappings for the first dataframe
mappings = {}
for column_name in ['Country', 'Citizenship Status', 'Continent', 'Degree Type', 'Entry Semester']:
    mappings[column_name], _ = map_values(query_clean, column_name, mappings.get(column_name))
    print(mappings[column_name])
    CISAT_clean[column_name] = CISAT_clean[column_name].map(mappings[column_name])
    DBOS_clean[column_name] = DBOS_clean[column_name].map(mappings[column_name])
    DPE_clean[column_name] = DPE_clean[column_name].map(mappings[column_name])
    DSM_clean[column_name] = DSM_clean[column_name].map(mappings[column_name])
    IMS_clean[column_name] = IMS_clean[column_name].map(mappings[column_name])
    SAH_clean[column_name] = SAH_clean[column_name].map(mappings[column_name])
    SCGH_clean[column_name] = SCGH_clean[column_name].map(mappings[column_name])
    SES_clean[column_name] = SES_clean[column_name].map(mappings[column_name])
```

ChatGPT:
>To find the most common reoccurring values in the specified columns for each division and store the data with division, count, and country translated back to a name, you can use the following code:

```python
from collections import Counter

# Define your list of dataframes and columns
clean_list = [CISAT_clean, DBOS_clean, DPE_clean, DSM_clean, IMS_clean, SAH_clean, SCGH_clean, SES_clean]
columns = ['Country', 'Citizenship Status', 'Continent', 'Degree Type', 'Entry Semester']
DIVISIONS = ['CISAT', 'DBOS', 'DPE', 'DSM', 'IMS', 'SAH', 'SCGH', 'SES']

# Initialize a dictionary to store the results
results = {}

# Loop through each dataframe and division
for df, division in zip(clean_list, DIVISIONS):
    division_results = {}
    for column_name in columns:
        # Count the occurrences of each value in the column
        counts = Counter(df[column_name])
        # Get the most common value and its count
        most_common_value, most_common_count = counts.most_common(1)[0]
        # Translate the country value back to a name using the index_mapping
        most_common_country = index_mapping[most_common_value]
        # Store the results for this column
        division_results[column_name] = {
            'Most Common Value': most_common_value,
            'Most Common Count': most_common_count,
            'Most Common Country': most_common_country
        }
    # Store the results for this division
    results[division] = division_results

# Print the results
for division, division_results in results.items():
    print(f"Division: {division}")
    for column_name, values in division_results.items():
        print(f"{column_name}: {values}")
    print()
```

>This code will print the most common reoccurring values for each specified column in each division, along with their counts and the translated country name.


Me:
>index_mapping is not defined. what would i put there?

ChatGPT:
>The `index_mapping` variable is the dictionary that maps the index values back to the original country names. Since you have defined the `map_values` function that creates this mapping, you can use the mappings generated during the mapping process. Here's how you can modify the code to include the `index_mapping`:

```python
from collections import Counter

# Define your list of dataframes and columns
clean_list = [CISAT_clean, DBOS_clean, DPE_clean, DSM_clean, IMS_clean, SAH_clean, SCGH_clean, SES_clean]
columns = ['Country', 'Citizenship Status', 'Continent', 'Degree Type', 'Entry Semester']
DIVISIONS = ['CISAT', 'DBOS', 'DPE', 'DSM', 'IMS', 'SAH', 'SCGH', 'SES']

# Initialize a dictionary to store the results
results = {}

# Initialize an empty index_mapping dictionary
index_mapping = {}

# Loop through each dataframe and division
for df, division in zip(clean_list, DIVISIONS):
    division_results = {}
    for column_name in columns:
        # Count the occurrences of each value in the column
        counts = Counter(df[column_name])
        # Get the most common value and its count
        most_common_value, most_common_count = counts.most_common(1)[0]
        # Translate the country value back to a name using the index_mapping
        most_common_country = index_mapping.get(column_name, {}).get(most_common_value, most_common_value)
        # Store the results for this column
        division_results[column_name] = {
            'Most Common Value': most_common_value,
            'Most Common Count': most_common_count,
            'Most Common Country': most_common_country
        }
    # Store the results for this division
    results[division] = division_results

# Print the results
for division, division_results in results.items():
    print(f"Division: {division}")
    for column_name, values in division_results.items():
        print(f"{column_name}: {values}")
    print()
```

This code assumes that `index_mapping` is a dictionary that maps column names to dictionaries, where each inner dictionary maps index values to country names. For example:

```python
index_mapping = {
    'Country': {0: 'USA', 1: 'Canada', 2: 'UK', ...},
    'Citizenship Status': {0: 'Citizen', 1: 'Permanent Resident', ...},
    ...
}
```

You would populate this `index_mapping` dictionary during the initial mapping process using your `map_values` function.

Me:
>given my mapping function, how would i create and refer to this dictionary? 

```python
def map_values(df, column_name, mapping):
    if mapping is None:
        unique_values = df[column_name].unique()
        mapping = {value: idx for idx, value in enumerate(unique_values)}
    index_mapping = {idx: value for value, idx in mapping.items()}
    df[column_name] = df[column_name].map(mapping)
    return mapping, index_mapping

# Create mappings for the first dataframe
mappings = {}
for column_name in ['Country', 'Citizenship Status', 'Continent', 'Degree Type', 'Entry Semester']:
    mappings[column_name], _ = map_values(query_clean, column_name, mappings.get(column_name))
    print(mappings[column_name])
    CISAT_clean[column_name] = CISAT_clean[column_name].map(mappings[column_name])
    DBOS_clean[column_name] = DBOS_clean[column_name].map(mappings[column_name])
    DPE_clean[column_name] = DPE_clean[column_name].map(mappings[column_name])
    DSM_clean[column_name] = DSM_clean[column_name].map(mappings[column_name])
    IMS_clean[column_name] = IMS_clean[column_name].map(mappings[column_name])
    SAH_clean[column_name] = SAH_clean[column_name].map(mappings[column_name])
    SCGH_clean[column_name] = SCGH_clean[column_name].map(mappings[column_name])
    SES_clean[column_name] = SES_clean[column_name].map(mappings[column_name])
  ```

  ChatGPT:
>To create and refer to the `index_mapping` dictionary using your mapping function, you can modify your code as follows:

```python
# Initialize an empty index_mapping dictionary
index_mapping = {}

# Create mappings for the first dataframe
mappings = {}
for column_name in ['Country', 'Citizenship Status', 'Continent', 'Degree Type', 'Entry Semester']:
    mappings[column_name], index_mapping[column_name] = map_values(query_clean, column_name, mappings.get(column_name))
    print(mappings[column_name])
    CISAT_clean[column_name] = CISAT_clean[column_name].map(mappings[column_name])
    DBOS_clean[column_name] = DBOS_clean[column_name].map(mappings[column_name])
    DPE_clean[column_name] = DPE_clean[column_name].map(mappings[column_name])
    DSM_clean[column_name] = DSM_clean[column_name].map(mappings[column_name])
    IMS_clean[column_name] = IMS_clean[column_name].map(mappings[column_name])
    SAH_clean[column_name] = SAH_clean[column_name].map(mappings[column_name])
    SCGH_clean[column_name] = SCGH_clean[column_name].map(mappings[column_name])
    SES_clean[column_name] = SES_clean[column_name].map(mappings[column_name])

# Now you can refer to the index_mapping dictionary
for column_name, mapping in index_mapping.items():
    print(f"Index mapping for {column_name}: {mapping}")
```

>This code will populate the `index_mapping` dictionary with mappings for each column and allow you to refer to it later in your code.

### Top/Bottom Values and Exporting

Me:
>how do i get the three least common values for bot? 

```python
from collections import Counter

def find_counts(df, column, index, division):
  counts = Counter(df[column])
  top = counts.most_common(3)[0:3]
  bot = counts.most_common(3)[:-1]
  return top, bot
  ```

ChatGPT:
>Your function `find_counts` looks almost correct. However, to get the three least common values, you need to adjust the `bot` variable. Here's the corrected version:

```python
def find_counts(df, column, index, division):
    counts = Counter(df[column])
    top = counts.most_common(3)[0:3]
    bot = counts.most_common()[:-4:-1]
    return top, bot
```

>In the `bot` line, `most_common()` returns all items in the counter in descending order of frequency, and `[:-4:-1]` selects the last three items, which are the least common values.

Me:
>what's wrong with the index? 

```python
from collections import Counter

def find_counts(df, column, index, division):
  counts = Counter(df[column])
  top = counts.most_common(3)[0:3]
  bot = counts.most_common()[:-4:-1]
  for i in range(0,3):
    bot[i][0] = index[bot[i][0]]
  return top, bot

top_CISAT, bot_CISAT = find_counts(CISAT_clean, 'Country', COUNTRY_INDEX, 'CISAT') 

TypeError: 'tuple' object does not support item assignment
```

ChatGPT:
>The error message `'tuple' object does not support item assignment` indicates that you're trying to modify a tuple, which is immutable. In your case, `bot` is a tuple because of the way you're using `most_common()[:-4:-1]`, which returns a tuple of (value, count) pairs.

>To fix this, you can convert `bot` to a list before trying to modify it. Here's the updated function:

```python
def find_counts(df, column, index, division):
    counts = Counter(df[column])
    top = counts.most_common(3)[0:3]
    bot = counts.most_common()[:-4:-1]
    bot = list(bot)  # Convert to a list
    for i in range(0, 3):
        bot[i] = (index[bot[i][0]], bot[i][1])
    return top, bot
```

>This change converts `bot` to a list before the loop where you're trying to modify it. It then correctly updates the first element of each tuple in `bot` with the corresponding value from `index`.

Me:
>can i add a suffix onto top and bot to allow me to name them `top_CISAT` if the division=CISAT? 
```python
for df, division in zip(clean_list, DIVISIONS):
  for column in columns:
    top, bot = find_counts(df, column, COUNTRY_INDEX, division)
```

ChatGPT:
>You can modify your code to add a suffix to `top` and `bot` based on the `division` value. Here's how you can do it:

```python
for df, division in zip(clean_list, DIVISIONS):
    for column in columns:
        top, bot = find_counts(df, column, COUNTRY_INDEX, division)
        top_name = f"top_{division}_{column}"
        bot_name = f"bot_{division}_{column}"
        globals()[top_name] = top
        globals()[bot_name] = bot
```

>In this code, `top_name` and `bot_name` are created dynamically based on the `division` and `column` values. `globals()` is then used to create variables with these names and assign the `top` and `bot` values to them. This allows you to access these values later using the constructed variable names.

Me:
>why is the last value not getting translated? 

```python
from collections import Counter

def find_counts(df, column, index, division):
    counts = Counter(df[column])
    top = counts.most_common(3)[0:3]
    bot = counts.most_common()[:-4:-1]
    for i in range(0, 2):
        bot[i] = {index[bot[i][0]], bot[i][1]}
        top[i] = {index[top[i][0]], top[i][1]}
    return top, bot 

result: [{'Finland', 1}, {1, 'Colombia'}, (17, 1)]
```

ChatGPT:
>The issue in your code lies in the way you're trying to update `bot` and `top` values. You're using curly braces `{}` which create a set, but you should be using parentheses `()` to create a tuple. Here's the corrected version of your function:

```python
def find_counts(df, column, index, division):
    counts = Counter(df[column])
    top = counts.most_common(3)[0:3]
    bot = counts.most_common()[:-4:-1]
    for i in range(0, 3):
        bot[i] = (index[bot[i][0]], bot[i][1])
        top[i] = (index[top[i][0]], top[i][1])
    return top, bot
```

>This change ensures that `bot` and `top` are updated with tuples instead of sets, which is the correct way to construct key-value pairs in this context.

Me:
>can i change the range function to decrease if there is not a third variable to use?

ChatGPT:
>Yes, you can change the range function to decrease dynamically based on the length of `bot` or `top`. Here's how you can modify your function to achieve this:

```python
def find_counts(df, column, index, division):
    counts = Counter(df[column])
    top = counts.most_common(3)[0:3]
    bot = counts.most_common()[:-4:-1]
    min_length = min(len(bot), len(top))
    for i in range(min_length):
        bot[i] = (index[bot[i][0]], bot[i][1])
        top[i] = (index[top[i][0]], top[i][1])
    return top, bot
```

>In this modification, `min_length` is calculated as the minimum of the lengths of `bot` and `top`. The `range` function then iterates up to `min_length`, ensuring that only existing elements are processed.

Me: 
>now i want to put this into a dataframe and export it into a csv in whatever format will be most useful for this situation

ChatGPT:
>To put the data into a DataFrame and export it to a CSV file, you can modify the function to return DataFrames instead of lists, and then use the `to_csv` method to export the DataFrame to a CSV file. Here's how you can do it:

```python
import pandas as pd
from collections import Counter

def find_counts(df, column, index, division):
    counts = Counter(df[column])
    top = counts.most_common(3)[0:3]
    bot = counts.most_common()[:-4:-1]
    min_length = min(len(bot), len(top))
    top_data = [(index[item[0]], item[1]) for item in top[:min_length]]
    bot_data = [(index[item[0]], item[1]) for item in bot[:min_length]]
    top_df = pd.DataFrame(top_data, columns=['Feature', 'Count'])
    bot_df = pd.DataFrame(bot_data, columns=['Feature', 'Count'])
    return top_df, bot_df



for df, division in zip(clean_list, DIVISIONS):
    for column in columns:
        top_df, bot_df = find_counts(df, column, COUNTRY_INDEX, division)
        top_filename = f"top_{division}_{column}.csv"
        bot_filename = f"bot_{division}_{column}.csv"
        top_df.to_csv(top_filename, index=False)
        bot_df.to_csv(bot_filename, index=False)
```

>In this code, `top_df` and `bot_df` are created as DataFrames with columns 'Feature' and 'Count'. These DataFrames are then exported to CSV files using the `to_csv` method, with `index=False` to exclude the index from the output. You may need to adjust the exact column names and file naming conventions to fit your specific needs.

### Create Full Feature DataFrame
Me:
>i need to get the top 3 and bottom 3 into the correct columns in my new dataframe. how do i do that? from collections import Counter

```python
def find_counts(df, column, index, division):
    counts = Counter(df[column])

    top = counts.most_common(3)[0:3]
    bot = counts.most_common()[:-4:-1]

    min_length = min(len(bot), len(top))
    for i in range(min_length):
        bot[i] = (index[bot[i][0]], bot[i][1])
        top[i] = (index[top[i][0]], top[i][1])

    top_df = pd.DataFrame(top, columns=['Feature', 'Count'])
    bot_df = pd.DataFrame(bot, columns=['Feature', 'Count'])
    top_filename = f"top_{division}_{column}.csv"
    bot_filename = f"bot_{division}_{column}.csv"
    top_df.to_csv(top_filename, index=False)
    bot_df.to_csv(bot_filename, index=False)

    return top, bot

top_CISAT, bot_CISAT = find_counts(CISAT_clean, 'Country', COUNTRY_INDEX, 'CISAT')
j = 0
for IMP in IMP_LIST:
    print(f"\n{new_list[j]}") # sense check

    new_df = pd.DataFrame(columns=['Feature Index #', 'Feature', 'Importance', 
                                   'Top 1', 'Top Count 1',
                                   'Top 2', 'Top Count 2',
                                   'Top 3', 'Top Count 3',
                                   'Bottom 1', 'Bottom Count 1',
                                   'Bottom 2', 'Bottom Count 2',
                                   'Bottom 3', 'Bottom Count 3']) # create an empty DataFrame

    for i, importance in enumerate(IMP): # construct the new dataframe
        new_df.loc[len(new_df)] = [i+1,COLUMNS[i+1], importance]

    for df, division in zip(clean_list, DIVISIONS):
      for column, index in zip(columns,indexes):
          top, bot = find_counts(df, column, index, division)

      
    new_df.to_csv(f'{DIVISIONS[j]}_features.csv', index=False) # export
    new_list[j] = new_df # store as a variable
    print(new_list[j])

    j+=1
    new_df = pd.DataFrame()
```

ChatGPT:
>It looks like you're trying to get the top 3 and bottom 3 counts for each feature in your DataFrame. However, there are a few things to note in your code:
1. In the `find_counts` function, you're returning the top and bottom counts, but you're not actually adding these counts to your new DataFrame.
2. You're creating a new DataFrame (`new_df`) inside the loop, but you're not appending the top and bottom counts to it.

>Here's an updated version of your code that should address these issues:

```python
from collections import Counter
import pandas as pd

def find_counts(df, column, index, division):
    counts = Counter(df[column])

    top = counts.most_common(3)
    bot = counts.most_common()[:-4:-1]

    min_length = min(len(bot), len(top))
    top_data = [(index[item[0]], item[0], item[1]) for item in top]
    bot_data = [(index[item[0]], item[0], item[1]) for item in bot]

    return top_data, bot_data

for j, IMP in enumerate(IMP_LIST):
    new_df = pd.DataFrame(columns=['Feature Index #', 'Feature', 'Importance', 
                                   'Top 1', 'Top Count 1',
                                   'Top 2', 'Top Count 2',
                                   'Top 3', 'Top Count 3',
                                   'Bottom 1', 'Bottom Count 1',
                                   'Bottom 2', 'Bottom Count 2',
                                   'Bottom 3', 'Bottom Count 3']) # create an empty DataFrame

    for i, importance in enumerate(IMP): # construct the new dataframe
        new_df.loc[len(new_df)] = [i+1, COLUMNS[i+1], importance, None, None, None, None, None, None, None, None, None, None, None, None]

    for df, division in zip(clean_list, DIVISIONS):
        for column, index in zip(columns, indexes):
            top, bot = find_counts(df, column, index, division)
            for i, (index, feature, count) in enumerate(top):
                new_df.at[i, f'Top {i+1}'] = feature
                new_df.at[i, f'Top Count {i+1}'] = count
            for i, (index, feature, count) in enumerate(bot):
                new_df.at[i, f'Bottom {i+1}'] = feature
                new_df.at[i, f'Bottom Count {i+1}'] = count

    new_df.to_csv(f'{DIVISIONS[j]}_features.csv', index=False) # export
    new_list[j] = new_df # store as a variable
    print(new_list[j])
```

>This code should populate the `new_df` DataFrame with the top and bottom features and their counts for each division, and then export it to a CSV file. Please adjust it further according to your exact requirements and DataFrame structures.

### What's a Dictionary again?
Me:
>how would i reference the values 'ghana' and '83' if i wanted to print them each separately? `[('Ghana', 83), ('India', 73), ('United States', 63)]`

ChatGPT:
>To reference the values 'Ghana' and '83' separately from the tuple ('Ghana', 83), you can use indexing. In Python, you can access elements in a tuple using square brackets `[]` with the index of the element you want. Since the tuple has two elements, 'Ghana' and 83, the index 0 refers to 'Ghana', and the index 1 refers to 83. Here's how you can print them separately:

```python
data = [('Ghana', 83), ('India', 73), ('United States', 63)]

# Accessing the first tuple and its elements
country_1 = data[0][0]  # 'Ghana'
count_1 = data[0][1]    # 83

# Printing the values separately
print(country_1)  # Output: Ghana
print(count_1)    # Output: 83
```

>You can do the same for other tuples in the list by changing the index in `data[index]`.


### DataFrame for Counts

Me:
>if top is a dictionary containing `[('Value1', Count1), ('Value2', Count2), ('Value3', Count3)]` how would i get this to populate my dataframe?

```python
count_df = pd.DataFrame(columns=['Division', 'Feature', 'Value','Count'])
for division in DIVISIONS:
  for column, index in zip(clean_list, columns, indexes):
    top = find_counts(division, column, index)
    for i in top.items():
        count_df.loc[len(count_df)] = [DIVISIONS[division], COLUMNS[column], top[i][0], top[i][1]]
```

ChatGPT:
>Your code snippet has a few issues. Here's a corrected version:

```python
count_df = pd.DataFrame(columns=['Division', 'Feature', 'Value', 'Count'])
for division in DIVISIONS:
    for column, index in zip(clean_list, columns):
        top = find_counts(division, column, index)
        for value, count in top:
            count_df.loc[len(count_df)] = [DIVISIONS[division], COLUMNS[column], value, count]
```

>This corrected version does the following:
- Loops over each `division` in `DIVISIONS`.
- Loops over each `column` and `index` in `clean_list` and `columns` respectively.
- Calls `find_counts(division, column, index)` to get the `top` dictionary.
- Loops over each `value` and `count` pair in the `top` dictionary.
- Adds a new row to `count_df` for each `value` and `count` pair, with the corresponding division, column, value, and count.

>Make sure that `find_counts(division, column, index)` returns a dictionary with the desired structure, and that `DIVISIONS` and `COLUMNS` are defined appropriately in your code.

Me:
>User
add an if statement so that top contains only 2 values if there are less than 3 possible values for it to count from collections import Counter

```python
def find_counts(df, column, index):
    counts = Counter(df[column])

    top = counts.most_common(3)

    top_data=[]

    top_data = {index[item[0]]:item[1] for item in top}

    return top_data
```

ChatGPT:
>You can add an if statement to check the length of `counts.most_common()` and limit `top` to contain only 2 values if there are less than 3 possible values. Here's the modified function:

```python
from collections import Counter

def find_counts(df, column, index):
    counts = Counter(df[column])
    
    if len(counts) < 3:
        top = counts.most_common(len(counts))
    else:
        top = counts.most_common(3)

    top_data = {index[item[0]]:item[1] for item in top}

    return top_data
```

>Now, `top` will contain only 2 values if there are less than 3 possible values in the `counts` Counter object.

### Add a New Row to Count DataFrame
Me:
>how do i complete this final line correctly? from collections import Counter

```python
def find_counts(df, column, index):
    counts = Counter(df[column])

    if len(counts) < 3:
        top = counts.most_common(2) + None
    else:
        top = counts.most_common(3)

    top_data = {index[item[0]]:item[1] for item in top}

    return top_data count_df = pd.DataFrame(columns=['Division', 'Feature', 'Value', 'Count'])
for division,df in zip(DIVISIONS,clean_list):
    for column, index in zip(columns, indexes):
        top = find_counts(df, column, index)
        for value, count in top.items():
            count_df.loc[count_df.shape[0]] = [DIVISIONS[division], COLUMNS[column], value, count]
```

ChatGPT:
>It looks like you're trying to create a DataFrame `count_df` to store the top counts for each column in your DataFrames. The final line of your code snippet seems to be an attempt to add a row to `count_df` for each top count. However, there is a small mistake in the way you're trying to add the row. You should use `count_df.loc[len(count_df)]` instead of `count_df.loc[count_df.shape[0]]` to add a new row. Here's the corrected final line:

```python
count_df.loc[len(count_df)] = [DIVISIONS[division], COLUMNS[column], value, count]
```

>This will add a new row to `count_df` with the specified values for 'Division', 'Feature', 'Value', and 'Count'.

Me:
>i am getting this error `AttributeError: 'DataFrame' object has no attribute 'append'` on this line `count_df = count_df.append(dictionary, ignore_index=True)` of this code: 

```python
def count_df(df,division):
    count_df = pd.DataFrame(columns=['Division', 'Feature', 'Value', 'Count'])
    for column, index in zip(columns, indexes):
        top = find_counts(df, column, index)
        for item in top.items():
          dictionary = {'Division':division, 'Feature': column, 'Value': item[0], 'Count': item[1]}
          count_df = count_df.append(dictionary, ignore_index=True)
    return count_df

for df, division in zip(clean_list,DIVISIONS):
    count_df(df, division)
```

ChatGPT:
>The error message "AttributeError: 'DataFrame' object has no attribute 'append'" suggests that the `count_df` variable might not be a DataFrame when the `append` method is called on it. This could happen if the `find_counts` function returns an empty result, so `top` ends up being an empty dictionary.

>To fix this, you can check if `top` is empty before attempting to append to `count_df`. Here's the modified code:

```python
def count_df(df, division):
    count_df = pd.DataFrame(columns=['Division', 'Feature', 'Value', 'Count'])
    for column, index in zip(columns, indexes):
        top = find_counts(df, column, index)
        if top:  # Check if top is not empty
            for item in top.items():
                dictionary = {'Division': division, 'Feature': column, 'Value': item[0], 'Count': item[1]}
                count_df = count_df.append(dictionary, ignore_index=True)
    return count_df

for df, division in zip(clean_list, DIVISIONS):
    count_df(df, division)
```

>This modification ensures that `dictionary` is only appended to `count_df` when `top` is not empty, which should prevent the `AttributeError` from occurring.

Me:
>can i use something other than append? can i use count_df.loc? how would i do this? 

ChatGPT:
>Yes, you can use a different approach to avoid using append and make the code more efficient. Instead of appending rows to the DataFrame in each iteration of the loop, you can directly assign values to the DataFrame using `.loc` without creating a dictionary. Here's how you can modify your function:

```python
def count_df(df, division):
    count_df = pd.DataFrame(columns=['Division', 'Feature', 'Value', 'Count'])
    for column, index in zip(columns, indexes):
        top = find_counts(df, column, index)
        if top:  # Check if top is not empty
            for item in top.items():
                count_df.loc[len(count_df)] = [division, column, item[0], item[1]]  # Assign values directly to DataFrame
    return count_df

for df, division in zip(clean_list, DIVISIONS):
    count_df(df, division)
```

In this modified version, `count_df.loc[len(count_df)]` is used to add a new row to the DataFrame `count_df`, and the values `[division, column, item[0], item[1]]` are directly assigned to the columns of the new row.





# Links

+ Side-by-Side Bar Plots: https://stackoverflow.com/questions/49289189/seaborn-or-matplotlib-bar-plot-multiple-dataframes-side-by-side
+ Counter: https://stackoverflow.com/questions/3594514/how-to-find-most-common-elements-of-a-list
+ numpy.reshape: https://numpy.org/doc/stable/reference/generated/numpy.reshape.html
+ DataFrame to csv with Pandas: https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.to_csv.html
+ Changing Variable Names With Python For Loops: https://www.geeksforgeeks.org/changing-variable-names-with-python-for-loops/ 
+ Features DataFrame: https://stackoverflow.com/questions/69149062/how-do-i-map-features-against-their-feature-importance-in-a-dataframe