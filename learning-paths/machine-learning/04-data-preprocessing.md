# Data preprocessing 

The first rule in preprocessing is: **know your data!**

The **TBLF** approach is suggested:
+ **Try:** Explore the problem (in this case, explore your data)
+ **Broken?:** What is broken? (Do you see something wrong in your data)
+ **Learn:** Why is it wrong? (What is wrong with this data? How can you solve it?)
+ **Fix:** Fix the problem (What can I do to bring the wrong data to an acceptable solution?)

**Data**  
: are observations or measurements (unprocessed or processed) represented as text, numbers, or multimedia. [^1]

**Dataset**
: is a structured collection of data generally associated with a unique body of work. [^2]

#### Try

Read the .csv file using pandas [`read_csv`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html#pandas-read-csv) , if a column is has no name it will be named `Unnamed`, so will need to remove it. Then check how many features the dataset has [^3] using [`shape`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.shape.html) on the dataset

```python
print("Number of features: {}".format(data.shape[1]))
```

Then check how much data [^4] is in the dataset 

```python
print("Number of samples: {}".format(data.shape[0]))
```

Can view the first couple of rows within the dataset using [`head`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.head.html?highlight=head#pandas.DataFrame.head)(default amount returned is 5).

```python
data.head(10)
```

Additionally find the columns types and null values, this can help identify how many columns are numerical or categorical [`info`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.info.html).

```python
data.info()
```

It's also possible to create a new [`dataframe`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) from columns in the first dataframe.

```python
new_data = data[['column_name1', 'column_name2', 'column_name3', 'column_name4']]
```

#### Broken?

Check for missing values with [`isnull`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.isnull.html#pandas.DataFrame.isnull) returns **True** for each cell of the dataset that is missing a value.

```python
data.isnull().head(10)
```

Because all the values returned are booleans, using you [`sum`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.sum.html?highlight=sum#pandas.DataFrame.sum) by column (feature), you will give the amount of true values--the number of missing values for each feature. Missing values are an important issue. Most models won't deal well with missing values.

```python
data.isnull().sum()
```

In addition to this you can use [`any`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.any.html#pandas.DataFrame.any) to filter and return any element that is **True**.

```python
data_null = data[data.isnull().any(axis=1)]
```

Remove the missing values with [`dropna`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.dropna.html?highlight=dropna#pandas.DataFrame.dropna) in the dataset. Save the result in a new dataset. Use [`shape`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.shape.html?highlight=shape#pandas.DataFrame.shape) after to confirm that your dataset has fewer rows because missing values have been removed.

```python
data_no_missing_value = data.dropna()
data_no_missing_value.shape
```

Confirm that the new dataset has removed all missing values with [`isna`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.isna.html#pandas.DataFrame.isna) as there is no missing values and it should be **False** for all features.

```python
data_no_missing_value.isna().any()
```

If you cannot afford to remove the rows with missing values or don't want to reduce an already small dataset. An alternative is imputing numerical values, for example the mean for the feature (column) [^4] value.

```python
column_name_MEAN = data["column_name"].mean()
```

Next is to update the missing values with the calculated mean for the feature, [`inplace`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.fillna.html?highlight=fillna#pandas.DataFrame.fillna)  updates the original dataset, so there is no need to create a new dataset.

```python
data["column_name"].fillna(column_name_MEAN, inplace=True)
```

[`isna`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.isna.html#pandas.DataFrame.isna) should return **False** as all missing values have been populated with a numeric value.

```python
data.isna().any()
```

For categorical value imputation, a common approach is to use the most frequent value (the mode). [`value_counts`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.value_counts.html#pandas.Series.value_counts) which finds the most frequent categorical for a feature (how many times it appears in the data).

```python
data["column_name"].value_counts()
```

If you only want the *most* frequency categorical value, then use [`mode`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.mode.html?highlight=mode#pandas.DataFrame.mode), there are slightly different approaches for getting this value.

```python
data["column_name"].mode(0)
# OR access the feature with, both will return the same
data.ranking.mode()[0]
```

After finding the most common value for the feature, use [`inplace`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.fillna.html?highlight=fillna#pandas.DataFrame.fillna) to inpute the most frequent value.

```python
data["column_name"].fillna('COMMON_VALUE', inplace=True)
```

Once again confirm that there is no missing values in the data, as value should be present and the result will be **False** for all features.

```python
data.isna().any()
```

As there is no missing values, [`value_counts`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.value_counts.html#pandas.Series.value_counts) should reflect the new count for the top categorical value is now the sum of the original one plus the number of null values that were inserted. So, if the most common value was 'A' found 10 and there was 10 missing values that were replaced with 'A'. Then the new sum will be A with 15

```python
data["column_name"].value_counts()
```

Unbalanced labels occur when the distribution between the labels presents some of them with much higher frequency than the others. Double check this is not the case for your data set.

#### Try Pt. 2

Explore the distributions by calculating the min value, max value, mean, standard deviation, and the 25% and 75% percentiles for each column. Using [`describe`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.describe.html?highlight=describe#pandas.DataFrame.describe).

```python
data.describe()
```

Use matplotlib  [`.plot.hist`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.hist.html) to plot distribution of each feature.

```python
data["column_name"].plot.hist(bins=100)
```

Not all graphs are great for plotting your feature, its best to explore the different types of plots that can be used e.g. [`boxplot`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.boxplot.html) might be suitable for a numerical feature as the box and whisker plot helps spot outliers.

```python
data.boxplot(["column_name"])
```

Use a correlation matrix for all features, plotting a scatter plot for each combination of numerical features. Using seaborn [`set`](https://seaborn.pydata.org/generated/seaborn.set.html) and [`pairplot`](https://seaborn.pydata.org/generated/seaborn.pairplot.html)

```python
numerical_data = data[["column_name_1", "column_name_2","column_name_3","column_name_4"]]
sns.set()
grid = sns.pairplot(numerical_data)
```

If there is some correlation between other variables. But need numbers to make a decision whether to remove some highly correlated feature. For this, use seaborn [`heatmap`](https://seaborn.pydata.org/generated/seaborn.heatmap.html) is useful while using pandas [`corr`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.corr.html?highlight=corr#pandas.DataFrame.corr) to print correlations.

```python
corr = numerical_data.corr()
ax = sns.heatmap(corr, annot=True)
```

If there are variables that are highly correlated (over 99%), should try removing the feature from the dataset.

```python
del numerical_data["column_high_correlation"]
```

And then use [`heatmap`]() again to confirm the correlation between the variables and the target after removing the correlation.

#### Plotting

To find the distribution of the individual features with seaborn [`displot`](https://seaborn.pydata.org/generated/seaborn.displot.html)(histogram is the default generated).

```python
sns.displot(data[data['column_name_1'] == ?]['column_name_2'])
```

#### Summary

Its important to explore your data over several dimensions and views to bring the raw data to a state of processed data, ready to be used for your model. This will help to understand the business scenario and corresponding dataset. Analysing the data using descriptive statistics can aid with better understanding it.

Visualisation tools, including box and whisker plots and histograms, help support this analysis and understand the distribution of data. Applying multivariate statistics with the help of scatter plots to spot correlations between features. The dataset is cleaned and ready for modeling step.

[^1] [^2] [Difference](https://www.usgs.gov/faqs/what-are-differences-between-data-dataset-and-database) between data and dataset
[^2] This is the number of columns in the `.csv` file
[^3] This is the number of rows in the `.csv` file
[^4] Feature names are case sensitive

---

#matplotlib #pandas #seaborn #data #dataset #data-prepocessing