# Feature Engineering

Feature engineering is the act of making data easier for a machine learning model to understand. It's not adding anything new but are reshaping and curating the existing data to make the existing patterns more apparent. Feature engineering is the process of using domain knowledge of the data to create features that make machine learning algorithms work better than they would on a simple raw encoding.

## Categorical variables
### Binary categorical

If your dataset contains a feature, which is a binary variable. It is possible to change the encoding from `'Y'` and `'N'` to `1` and `0`. This can be done using [`map`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.map.html), it expects either a function to apply to that column or a dictionary to look up the correct transformation.

```python
new_data['column_name'] = data['column_name'].map({'Y':1, 'N':0})
```

### Ordinal categorical

Same as above, but encoding many categorical variables. This is ideal if the feature can be thought of as an ordinal categorical variable.

```python
new_data['column_name'] = df['column_name'].map({'Poor':1, 'Fair':2, 'Average':3, 'Good':4, 'Very Good':5})
```

### Nominal categorical

TBA.

## Scaling
### Feature scaling

TBA.

## Hyperparameter optimisation

TBA.

## Summary

In summary, you can encode features that are otherwise inaccessible to the model (such as the categorical features). In these circumstances, simple techniques like one-hot encoding or ordinal encoding can go a long way. These techniques allows you to get more from the features you already had. For example, the encoding might be good; however, the pattern was difficult for the model to use. Presenting a variable in a way that makes the data available to the model is a key to the development of a high-performing model.

---

#pandas #sklearn #numpy #sagemaker 