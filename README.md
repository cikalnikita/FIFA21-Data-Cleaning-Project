# FIFA21-Data-Cleaning-Project
This repository contains the code and documentation for cleaning and preparing the FIFA21 dataset for analysis.

## Project Overview
The FIFA21 inlcudes various atributes related to football player, such as positions, club, and market values. The goal of this project is to clean and preprocess the data to ensure it is ready for accurate and meaningful analysis.

## Dataset
The dataset used in this project can be found [here](https://drive.google.com/file/d/1f9cEonMldDm4bF6_gwTWr07JBePDePeJ/view?usp=sharing). It includes information such as:
- Player Name
- Nationality
- Age
- Club
- Positions
- Height
- Weight
- Preferred Foot
- ...and many more attributes.

## Data Cleaning Steps
### 1. Preparation
a. Import Required Libraries
  ```python
  import pandas as pd
  import numpy as np
```
- `import pandas as pd`: This imports the Pandas library and aliases it as 'pd'. Pandas is used for data manipulation and analysis, providing data structures like DataFrames for tabular data.
- `import numpy as np`: This imports NumPy library and aliases it as 'np'. NumPy is used for numerical computations and provides support for arrays and matrices.
  
b. Import Dataset 
```python
def transform_url(url):
  trf_url = 'https://drive.google.com/uc?id=' + url.split('/')[-2]
  return trf_url
```
- `transform_url`: This function is designed to convert a Google Drive file URL into a format that can directly accessed or downloaded.
- `user.split('/')`: split the URL into a list of elements separated by the `/` character.
- `url.split('/')[-2]`: retrieves the second-to-last element from this list, which is typically the unique file ID in the Google Drive URL.
- `'https://drive.google.com/uc?id='`: a base URL that is concatenated with the file ID to form the new URL.
- `trf_url`: stores this new URL.
- `return`: returns the transformed URL (`trf_url`).

```python
dataset_url= transform_url('https://drive.google.com/file/d/1f9cEonMldDm4bF6_gwTWr07JBePDePeJ/view?usp=sharing')
df_dataset = pd.read_csv(dataset_url)
df_dataset
```
- The function `transform_url` is called with a Google Drive URL as its argument.
- As explained earlier, this function converts the Google Drive URL into a direct download link by extracting the file ID and appending it to a base URL (`https://drive.google.com/uc?id=`).
- The resulting URL is stored in the variable `dataset_url`.
- `pd.read_csv()` is a function from the Pandas library used to read CSV files into a DataFrame, which is a table-like structure for data manipulation.
- The `dataset_url` (which now contains the direct link to the CSV file) is passed as an argument to `pd.read_csv()`.
- This function downloads the CSV file from the provided URL and loads it into a Pandas DataFrame, which is stored in the variable `df_dataset`.
- Simply writing the name of the DataFrame (`df_dataset`) will display its contents in a Jupyter notebook or interactive Python environment (in this project I used **Goole Colab**).

**Display Dataset**:
<img width="1391" alt="Screenshot 2024-08-13 at 11 24 40" src="https://github.com/user-attachments/assets/3e91e11f-5bc7-4e00-beb3-b6a99b53544c">

### 2. Checking Data Info
```python
df_dataset.info()
```
- The code `df_dataset.info(()` is used to get summary of a Pandas DataFrame.
- The `info()` method provides a concise summary of the DataFrame, including information about the data types, non-null counts, and memory usage.

**Result**:

<img width="381" alt="Screenshot 2024-08-13 at 11 38 41" src="https://github.com/user-attachments/assets/97b64145-a090-499e-9ef5-3a5433dcfae0">

### 3. Data Cleaning
```python
dataset = df_dataset.copy()
```
- The code `dataset = df_dataset.copy()` is used to create a new, independent copy of a Pandas DataFrame.
- **Avoid Side Effects**: If you want to make changes to DataFrame without altering the original DataFrame, you use `copy()`. This is useful when you are performing data manipulations, transformations, or analysis that should not affect the original dataset.
- **Experimentation**: It allows you to experiment with data or perform operations such as cleaning, transformation, or analysis on the copied DataFrame while keeping the original DataFrame intact.

a. Convert Height and Weight Columns to Numerical Forms

**Height**
```python
dataset['Height'].unique()
```
- The code `dataset['Height'].unique()` is used to get the unique values from a spesific column in Pandas DataFrame.
- **Get Uniques Values**:This code retrieves an array of unique values from the column `'Height'` in the DataFrame `dataset`. It helps in understanding what distinct values exist in that column.
- `dataset['Heigth']`: This part accesses the column `'Height'` in the DataFrame `dataset`. It returns a Pandas Series containing all values from that column.
- `.unique()`: applied to the Pandas Series to find all unique values present in that Series. It returns a NumPy array of unique values in the order they first appear in the Series.

**Result**:

<img width="708" alt="Screenshot 2024-08-13 at 11 57 29" src="https://github.com/user-attachments/assets/71b6b914-25e8-47ce-8ac6-ca168f744c0a">


Since some of the height measurements are not yet in centimeters, I will convert the data from feet and inches to centimeters.
```python
# conversion function to converting feet-inches to cm
import re

def feet_inches_to_cm(height):
  match = re.match(r"(\d+)'(\d+)\"",height)
  if match:
    feet = int(match.group(1))
    inches = int(match.group(2))
    total_cm = feet * 30.48 + inches * 2.54
    return total_cm
  return None
```
- The parameter `height` is expected to be a string in the format of feet and inches (e.g, `5'7"`).
- `re.match(r"(\d+)'(\d+)\"", height)` uses a regular expression to match the pattern of feet and inches.
- `(\d+)` captures one or more digits representing feet.
- `(\d+)` captures one or more digits representing inches.
- `feet = int(match.group(1))` extracts the feet part.
- `inches = int(match.group(2))` extracts the inches part.
- `total_cm = feet * 30.38 + inches * 2.54` converts the feet to centimeters (1 foot = 30.48 cm) and inches to centimeters (1 inch = 2.54 cm), and then adds them together.
- Returns `None` if the height format does not match the expected pattern.

```python
# function to convert height to cm
def convert_to_cm(height):
  if 'cm' in height:
    return float(height.replace('cm',"))
  elif '\" in height and '\"' in height:
    return feet_inches_to_cm(height)
  else:
    return None
```
- **If height is in centimeters**: `if 'cm' in height`, strip the `'cm'` text and converts the remaining value to a float.
- **If height is in feet and inches**: `elif '\''in height and '\"' in height`, calls `feet_inches_to_cm(height)` to convert feet and inches to centimeters.
- **Otherwise: `elese`, returns `None` if the height format is unrecognized.

```pyton
# applied conversion function to Height column
dataset['height_cm'] = dataset['Height'].apply(convert_to_cm)

dataset[['Height', 'height_cm']]
```
- This part applies the `convert_to_cm` function to the `Height` column of the DataFrame `dataset` and stores the results in a new column `height_cm`.
- `dataset['Height'].apply(convert_to_cm)`: applies the `convert_to_cm` function to each entry in the `Height` column. The result is assigned to a new column named `height_cm`.
- `dataset[['Height', 'height_cm']] displays the original height column alongside the new `height_cm` column for comparison.

**Result**:

<img width="309" alt="Screenshot 2024-08-13 at 12 29 41" src="https://github.com/user-attachments/assets/897a9aca-849a-49a1-9cb1-f2d0455a1e8f">


To make sure all the data successfully converted, we can check it again using `.info()`.
```python
dataset['height_cm'].unique()
```
**Result**:

<img width="678" alt="Screenshot 2024-08-13 at 13 39 55" src="https://github.com/user-attachments/assets/f3baf6ad-4e8c-4df6-9b18-3c08006f25e0">

All the data already converted to centimeters.

**Weight**

Same as height column, the first step is checking if the weight column already in the same measurements.
```python
dataset['Weight'].unique()
```
**Result**:

<img width="703" alt="Screenshot 2024-08-13 at 13 46 54" src="https://github.com/user-attachments/assets/9c34ce0c-0d66-4c3d-b849-2b7dc10424e8">

Since some of the weight measurements are not yet in kilograms, I will convert the data from pounds to kilograms.
```python
# convert lbs to kg
def lbs_to_kg(weight):
  match = re.match(r"(\d+)lbs", weight)
  if match:
    lbs = float(match.group(1))
    kg = lbs * 0.45359237
    return kg
  return None
```
- The parameter `weight` is expected to be a string that inlcudes a number followed by text "lbs" (e.g., "150lbs").
- `re.match(r"(\d+)lbs", weight)` uses a regular expression to match the pattern where a number is followed by "lbs".
- `(\d+) captures one or more digits representing the weight in pounds.
- `lbs = float(match.group(1))` extract the numeric part of the weight in pounds and converts it to a floating-point number.
- `kg = lbs * 0.45359237` converts pounds to kilograms using the conversion factor (1 pound = 0.45359237 kg)
- Returns the weight in kilograms. Returns `None` if the weight format does not match the expected pattern.
```python
# conversion function weight to kg
def convert_to_kg(weight):
    if 'kg' in weight:
        return float(weight.replace('kg', ''))
    elif 'lbs' in weight:
        return lbs_to_kg(weight)
    else:
        return None
```
- `weight` is expected to be a string representing weight in either kilograms or pounds.
- **If weight is in kilograms**: `if 'kg' in weight` strips the `'kg'` text and converts the remaining value to a float.
- **If weight is in pounds**: `elif 'lbs' in weight` calls `lbs_to_kg(weight)` to convert the weight from pounds to kilograms.
- **Otherwise**: `else`, returns `None` if the weight format is unrecognized.
```pyhton
# Applying conversion function to DataFrame
dataset['weight_kg'] = dataset['Weight'].apply(convert_to_kg)

dataset[['Weight','weight_kg']]
```
- This part applies the `convert_to_kg` function to the `Weight` column of the DataFrame `dataset` and stores the results in a new column `weight_kg`.
- `dataset['Weight'].apply(convert_to_kg)`: applies `convert_to_kg` function to each entry in the `Weight` column. The result is assigned to a new column named `weight_kg`.
- `dataset['Weight'].apply(convert_to_kg)` displays the original weight column alongside the new `weight_kg` column for comparison.

**Result**:

<img width="335" alt="Screenshot 2024-08-13 at 14 19 40" src="https://github.com/user-attachments/assets/9a013f8f-4f86-42c0-b697-3e6577dbc09f">

To make sure all the data successfully converted, we can check it again using `.info()`.
```python
dataset['weight_kg'].unique()
```
**Result**:

<img width="598" alt="Screenshot 2024-08-13 at 14 22 22" src="https://github.com/user-attachments/assets/10ebf94c-f34b-4731-bb86-a019315bd217">

All the data already converted to kilograms.

b. Remove the unnecessary newline characters from all columns that have them
```python
def remove_newlines(text):
  if isinstance(text, str):
    return text.replace('\n','')
  return text
```
- This function removes newline characters ('\n') from a string.
- The parameter `text` is expected to be a string, but the function can handle any data type.
- **Check if `text` is a string: `if isinstance(text, str)`**, the function first checks if the input `text` is a string using `isinstance(text, str)`. This ensures that the operation is only performed on strings.
- **Remove Newlines: `return text.replace('\n','')`**. If `text` is not a string, the function simply returns the inputs as it is, without modification.
```python
# applying the `remove_newlines` function to the entrire DataFrame
dataset = dataset.applymap(remove_newlines)

dataset[['Club']]
```
- This line applies the `remove_newlines` function to every element in the DataFrame `dataset`.
- `applymap()` is a Pandas method that applies a given function to each element in a DataFrame. In this case `appplymap(remove_newlines)` applies the `remove_newlines` function to every cell in the `dataset` DataFrame.
- `dataset[['Club']] select the 'Club' column and shows its contents. This allows you to verify that the newline characters have been removed from the strings in this column.

**Result**:

<img width="278" alt="Screenshot 2024-08-13 at 20 34 55" src="https://github.com/user-attachments/assets/7b008bcd-b94a-47bd-9922-e376852c7b72">

After applying this function, the DataFrame `dataset` will have all newline characters removed from any string entries accross all columns.

c. Based on the 'Joined' column, check which players have been playing at a club for more than 10 years! (Make new column with the name Years_at_Club)
```python
# first we have to know what kind of data in column 'Joined'
dataset[['Joined']]
```
**Result**:

<img width="249" alt="Screenshot 2024-08-13 at 20 48 08" src="https://github.com/user-attachments/assets/55b1a9a5-2a7c-4dd2-9c62-03daf29b5678">

```python
# we also check the info of the column 'Joined' to know the data type because based on the result above the data type should be a date type
dataset['Joined'].info()
```
**Result**:

<img width="388" alt="Screenshot 2024-08-13 at 20 51 56" src="https://github.com/user-attachments/assets/dc68e705-40c7-490f-8431-024463bd0293">

```python
# convert the 'Joined' column to datetime format
dataset['Joined'] = pd.to_datetime(dataset['Joined'])
```
- `pd.to_datetime()`: This Pandas function converts the column `'Joined'`, which likely contains date string (e.g., "2020-01-15"), into `datetime` objects.
- `datetime` objects allow for easier date manipulation and calculation.
- After this conversion, the `'Joined'` column will contain dates in a standard `datetime` format, which can be used for further date-based operations.
```python
# get the current date
current_date = pd.Timestamp.now()
```
- `pd.Timestamp.now()`: This function returns the current date and time as a `Timestamp` object in Pandas.
- The `Timestamp` object is similar to Python's `datetime` but is specifically designed for use with Pandas.
- The `current_date` variable will store the exact current date and time at the moment this line of code executed.
```python
# calculate the difference in years between current date and 'Joined' date
dataset['Years_at_Club'] = ((current_date - dataset['Joined']).dt.days / 365.25).round()
```
- `current_date - dataset['Joined']` computes the difference between the `current_date` and the `'Joined'` date for each row. The result is a `Timedelta` object that represents the duration between these two dates.
- `.dt.days` extracts the number of days from the `Timedelta` object.
- Dividing by `365.25` converts the duration from days to years, accounting for leap years (where 0.25 accounts for the extra day every four years).
- `.round()` rounds the resulting number of years to the nearest whole number.
- A new column `'Years_at_Club'` is added to the `dataset`, containing the number of years each player has been at the club.
```python
# displaying the 'Joined' and 'Years_at_Club' columns
dataset[['Joined', 'Years_at_Club']]
```
-`dataset[['Joined', 'Years_at_Club']]` selects the `'Joined'` and `'Years_at_Club'` columns and shows their contents.
This allows you to verify the conversion of the joined dates and the calculation of the years spent at club.

**Result**:

<img width="345" alt="Screenshot 2024-08-14 at 07 58 43" src="https://github.com/user-attachments/assets/ea1df2b3-5b91-47fc-a9d7-ff727b05cacb">

c. 'Value', 'Wage' and "Release Clause' are string columns. Convert them to numbers. For eg, "M" in value column is Million, so multiply the row values by 1,000,000, etc.

The intruction suggest that the  `'Value'`, `'Wage'`, and `'Release Clause'` columns need to be converted from strings to numeric values. For examples:
- **"M"** represents **Million**, so any value with "M" should be multiplied by 1,000,000.
- **"K"** represents **Thousand**, so any value with "K" should be multiplied by 1,000.
- 
**Value**
```python
dataset['Value'].unique()
```
- This method returns an array of unique values found in the `'Value'` column.
- This help to understand the variety of formats or suffixes that might be present in the `'Value'` column before performing the conversion to numeric values.
- By checking the unique values in the `'Value'` column, you get an idea of how values are formatted (e.g., with "M" for millions, "K" for thousands).
- This is essential before writing a function to clean and convert these strings into numeric values.

Result:

<img width="674" alt="Screenshot 2024-08-14 at 08 54 09" src="https://github.com/user-attachments/assets/f6aad147-08e3-42c0-bf1f-640896c7cd7e">

```python
def convert_currency(value):
  value = value.replace('€', '')
  if 'M' in value:
    value = value.replace('M', '')
    return float(value) * 1000000
  elif 'K' in value:
    value = value.replace('K', '')
    return float(value) * 1000
  return float(value)

```
- The `convert_currency` function converts a string representing a monetary value (which includes a currency symbol and shorthand notations like "M" for millions and "K" for thousands) into a numerical value.
- The function takes a single argument `'Value'`, which is expected to be a string that represent a monetary amount, such as `'€5M'` (5 million euros) or `"€300K"` (300 toushand euros).
- `value = value.replace('€', '')`: this line removes euro symbol `"€"` from the string, leaving only the numeric part and the suffix (if any).
- `value = value.replace('M', '')` removes the `"M"` suffix. `return float(value) * 1000000` converts the remaining numeric string to a floating-point number and multiplies it by 1,000,000 to convert millions to the full numeric value. Example: `"5M"` becomes `5 * 1,000,000 = 5,000,000`.
- `value = value.replace('K', '')` removes the `"K"` suffix. `return float(value) * 1000` converts the remaining numeric string to a floating-point number and multiplies it by 1,000 to convert thousands to the full numeric value. Example: `"300K"` becomes `300 * 1,000 = 300,000`.
- If the string contains neither `"M"` nor `"K"`, it is assumed to be a plain numeric value, which is directly converted to a floating point number. Example: `"50000"` remains `500,000`.
```python
dataset['Value'] = dataset['Value'].apply(convert_currency)
```
- This line applies the `convert_currency` function to each element in the `'Value'` column of the `dataset` DataFrame, converting all string representations of monetary values into numeric values.
- Accesses the `'Value'` column, which contains the monetary values as strings.
- Applies the `convert_currency` function to every entry in the `'Value'` column.
- The result is that all values in the `'Value'` column are transformed into numeric values (integers or floats), and the column is updated in the DataFrame.
```python
# displaying the converted 'Value' column
dataset[['Value']
```
- This line displays the `Value` column of the `dataset` DataFrame after the conversion has been applied.
- `dataset[['Value']]: selects and shows the contents of the `Value` column after the conversion process. This allows you to verify that the string values have been correctly converted into numeric values.

**Result**:

<img width="214" alt="Screenshot 2024-08-14 at 09 45 09" src="https://github.com/user-attachments/assets/73863372-f189-4ff8-932d-5d70a67a6003">

**Wage**
```python
dataset['Wage'].unique()
```
**Result**:

<img width="583" alt="Screenshot 2024-08-14 at 09 55 57" src="https://github.com/user-attachments/assets/0e994638-d440-4aba-9b16-59b0079799c3">

```python
def convert_currency(value):
  value = value.replace('€', '')
  if 'M' in value:
    value = value.replace('M', '')
    return float(value) * 1000000
  elif 'K' in value:
    value = value.replace('K', '')
    return float(value) * 1000
  return float(value)

dataset['Wage'] = dataset['Wage'].apply(convert_currency)

dataset[['Wage']]
```
**Result**:

<img width="230" alt="Screenshot 2024-08-14 at 09 57 20" src="https://github.com/user-attachments/assets/ffa4353f-ef75-499b-aab6-3b4eabe521a6">

**Release Clause**
```python
dataset['Release Clause'].unique()
```
**Result**:

<img width="550" alt="Screenshot 2024-08-14 at 09 59 18" src="https://github.com/user-attachments/assets/dce05f87-5346-41a5-9471-82996db75a4d">

```python
def convert_currency(value):
  value = value.replace('€', '')
  if 'M' in value:
    value = value.replace('M', '')
    return float(value) * 1000000
  elif 'K' in value:
    value = value.replace('K', '')
    return float(value) * 1000
  return float(value)

dataset['Release Clause'] = dataset['Release Clause'].apply(convert_currency)

dataset[['Release Clause']]
```
**Result**:

<img width="233" alt="Screenshot 2024-08-14 at 10 00 26" src="https://github.com/user-attachments/assets/f018df16-83d1-4112-a76b-de6b695e9ef5">

d. Some columns have 'star' characters. Strip those columns of these stars and make the columns numerical
```python
# identifying the column with 'Star' characters
dataset[['IR']]
```
- This line of code is selecting and displaying the `IR` column from the dataset DataFrame.
- This retrieves the `IR` column as a DataFrame and displays its contents.

**Result**:

<img width="193" alt="Screenshot 2024-08-14 at 10 05 25" src="https://github.com/user-attachments/assets/9c578a5f-4696-4039-b4de-e857793b11b7">

- The column `IR` contains string values that include "★" characters, which are often used in rating systems.
```python
# removing the 'Star' characters and converting to numeric format
dataset['IR'] = dataset['IR'].str.replace('★', '').astype(float)
```
- This line of code removes the "★" characters from the `IR` column and converts the remaining values into a numeric format.
- `str.replace('★','')` is a string method that replaces all occurences of the "★" characters in the `IR` column with an empty string (`''`), effectively removing the stars.
- `.astype(float)` converts the cleaned strings (which are now just numbers) into floating-point numbers
```python
# displaying the converted 'IR' column
dataset['IR']
```
- This line of code displyas the contents of the `IR` column after the conversion process. Accesses and displays the `IR` column to verify that the star characters have been removed and the values hace been converted to numeric format.

**Result**:

<img width="350" alt="Screenshot 2024-08-14 at 10 22 21" src="https://github.com/user-attachments/assets/723038e0-9760-44e6-b150-94a31259da0f">

- After executing, the `IR` column no longer contains any star characters, and all its value are numerical. This conversion allows for further numerical analysis, like statistical calculations or comparison.

## Conclusion
In this project, we successfully cleaned and prepocessed the FIFA21 dataset, transforming it into a format suitable for analysis. Key steps included converting height and weight measurements into consistent units, handling string data in monetary columns, and calculating player's tenure at their clubs. These cleaning processes ensure that the dataset is ready for accurate and meaningful analysis, enabling deeper insights into the attributes and preformance of football players. With this cleaned data further exploratory data anaysis (EDA) and machine learning models can be effectively implemented tp uncover trends and patterns in the football world.
