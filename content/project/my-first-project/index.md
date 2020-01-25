---
# Documentation: https://sourcethemes.com/academic/docs/managing-content/

title: "Test Jupyter Project"
summary: "My beginning notes on the Kaggle Titanic dataset."
authors: []
tags: ["Learning"]
categories: ["Data Science"]
date: 2020-01-21T22:08:19+09:00

# Optional external URL for project (replaces project detail page).
external_link: ""

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
# check page_header.html for placement resize options
image:
  caption: "Titanic"
  focal_point: "Center"
  preview_only: false
  placement: 2
# custom image size
custom_size:
  use: true
  size: 900

# Custom links (optional).
#   Uncomment and edit lines below to show custom links.
# links:
# - name: Follow
#   url: https://twitter.com
#   icon_pack: fab
#   icon: twitter

url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
slides: ""
---
<!-- <script src="https://gist.github.com/tyler-wel/ee0ea5c7789274658aa6a6e5f39d261f.js"></script> -->

# Titanic Data Challenge

## Introduction
**Kaggle Description**: 
> On April 15, 1912, during her maiden voyage, the widely considered “unsinkable” RMS Titanic sank after colliding with an iceberg. Unfortunately, there weren’t enough lifeboats for everyone onboard, resulting in the death of 1502 out of 2224 passengers and crew.

> While there was some element of luck involved in surviving, it seems some groups of people were more likely to survive than others.

> In this challenge, we ask you to build a predictive model that answers the question: “what sorts of people were more likely to survive?” using passenger data (ie name, age, gender, socio-economic class, etc).

**Goal**: To predict whether or not a passenger will survive the sinking of the Titanic based on provided information

Let's begin! First, let's do some boilerplate setup.

### Imports


```python
%reload_ext autoreload
%autoreload 2

# custom helpers
from helpers.helper import get_splits
# data handling
import numpy as np
import pandas as pd
# output
from termcolor import cprint
import matplotlib.pyplot as plt
import seaborn as sns

cprint('All Modules Imported!', 'green')
```

    [32mAll Modules Imported![0m


### Data Import


```python
import os
os.listdir('./data/')
```




    ['gender_submission.csv', 'test.csv', 'train.csv']




```python
train_data = pd.read_csv('./data/train.csv', index_col='PassengerId')
test_data = pd.read_csv('./data/test.csv', index_col='PassengerId')

cprint('Data Imported!', 'green')
cprint('Training Data Example:', 'cyan')
display(train_data)
```

    [32mData Imported![0m
    [36mTraining Data Example:[0m



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
    </tr>
    <tr>
      <th>PassengerId</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
    </tr>
    <tr>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>113803</td>
      <td>53.1000</td>
      <td>C123</td>
      <td>S</td>
    </tr>
    <tr>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>Allen, Mr. William Henry</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>373450</td>
      <td>8.0500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>887</td>
      <td>0</td>
      <td>2</td>
      <td>Montvila, Rev. Juozas</td>
      <td>male</td>
      <td>27.0</td>
      <td>0</td>
      <td>0</td>
      <td>211536</td>
      <td>13.0000</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <td>888</td>
      <td>1</td>
      <td>1</td>
      <td>Graham, Miss. Margaret Edith</td>
      <td>female</td>
      <td>19.0</td>
      <td>0</td>
      <td>0</td>
      <td>112053</td>
      <td>30.0000</td>
      <td>B42</td>
      <td>S</td>
    </tr>
    <tr>
      <td>889</td>
      <td>0</td>
      <td>3</td>
      <td>Johnston, Miss. Catherine Helen "Carrie"</td>
      <td>female</td>
      <td>NaN</td>
      <td>1</td>
      <td>2</td>
      <td>W./C. 6607</td>
      <td>23.4500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <td>890</td>
      <td>1</td>
      <td>1</td>
      <td>Behr, Mr. Karl Howell</td>
      <td>male</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>111369</td>
      <td>30.0000</td>
      <td>C148</td>
      <td>C</td>
    </tr>
    <tr>
      <td>891</td>
      <td>0</td>
      <td>3</td>
      <td>Dooley, Mr. Patrick</td>
      <td>male</td>
      <td>32.0</td>
      <td>0</td>
      <td>0</td>
      <td>370376</td>
      <td>7.7500</td>
      <td>NaN</td>
      <td>Q</td>
    </tr>
  </tbody>
</table>
<p>891 rows × 11 columns</p>
</div>


## Process
1. Figure out which features we can safely drop/keep.
2. Encode features that need encoding (label encoding, categorical encoding).
3. Start feature engineering some new columns so we have a wider predicition set. 
4. Do feature selection to determine which features are not needed and find the best combination of features to use.
5. Research and test what models would be best for our situation and train/test different models.
6. Train and predict on the train/test sets.
7. Finally, output everything to a new CSV.

## Tools
Our current model options are: LightGBM, RandomForestRegressor, ExtraTreesRegressor.

I also want to use a pipeline to keep everything organized into various steps.


## Getting Started
### Feature Engineering
So the columns we have are:

| Variable | Definition | Key |
| ----- | --- | --- |
| survival | Survived or not | 0 = No, 1 = Yes |
| pclass | Ticket class | 1 = 1st, 2 = 2nd, 3 = 3rd |
| sex | Sex | |
| age | Age in years | |
| sibsp | Num of siblings / spouses aboard | |
| parch | Num of parents / children aboard | |
| ticket | Ticket number | |
| fare | Passengar fare | |
| cabin | Cabin number | |
| embarked | Port of embarkation |   C = Cherbourg, Q = Queenstown, S = Southampton |

Looking at these descriptions, we can probably disregard
- Name
- Ticket number

Ticket number is a ... maybe, as we aren't entirely sure how ticket numbers are handed out.

Let's get to engineering.
 

## Model Createion Steps
1. [x] Choose feature cols based on feature table and relevant data
2. [x] Split into train/valid/test sets
3. [ ] Generate features
    1. Interactions
    2. ...
4. [ ] Setup pipeline
    1. [x] Imputation to fill in N/A values
    2. [x] Categorical encoding, CatBoost
    4. [x] Standardize values
    5. [ ] Feature Selection
5. [ ] Train

Feature Generation / Engineering


```python
# 1. Choose our feature cols based on feature table above
numerical_cols = ['Age', 'SibSp', 'Parch', 'Fare']
categorical_cols = ['Pclass', 'Sex', 'Cabin', 'Embarked']
target_col = 'Survived'
# Let's make some features
from itertools import combinations

interactions = pd.DataFrame(index=train_data.index)
for comb in combinations(categorical_cols, 2):
    new_feat = comb[0] + "_" + comb[1]
    interactions[new_feat] = train_data[comb[0]].astype(str) + "_" + train_data[comb[1]].astype(str)
    categorical_cols.append(new_feat)
train_data = train_data.join(interactions)
display(train_data)
# 2. Split sets
train, valid, _ = get_splits(train_data)
X_train = train.drop([target_col], axis=1)
y_train = train[target_col]
X_valid = valid.drop([target_col], axis=1)
y_valid = valid[target_col]
display(X_train)

```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
      <th>Pclass_Sex</th>
      <th>Pclass_Cabin</th>
      <th>Pclass_Embarked</th>
      <th>Sex_Cabin</th>
      <th>Sex_Embarked</th>
      <th>Cabin_Embarked</th>
    </tr>
    <tr>
      <th>PassengerId</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>NaN</td>
      <td>S</td>
      <td>3_male</td>
      <td>3_nan</td>
      <td>3_S</td>
      <td>male_nan</td>
      <td>male_S</td>
      <td>nan_S</td>
    </tr>
    <tr>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
      <td>1_female</td>
      <td>1_C85</td>
      <td>1_C</td>
      <td>female_C85</td>
      <td>female_C</td>
      <td>C85_C</td>
    </tr>
    <tr>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>NaN</td>
      <td>S</td>
      <td>3_female</td>
      <td>3_nan</td>
      <td>3_S</td>
      <td>female_nan</td>
      <td>female_S</td>
      <td>nan_S</td>
    </tr>
    <tr>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>113803</td>
      <td>53.1000</td>
      <td>C123</td>
      <td>S</td>
      <td>1_female</td>
      <td>1_C123</td>
      <td>1_S</td>
      <td>female_C123</td>
      <td>female_S</td>
      <td>C123_S</td>
    </tr>
    <tr>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>Allen, Mr. William Henry</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>373450</td>
      <td>8.0500</td>
      <td>NaN</td>
      <td>S</td>
      <td>3_male</td>
      <td>3_nan</td>
      <td>3_S</td>
      <td>male_nan</td>
      <td>male_S</td>
      <td>nan_S</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>887</td>
      <td>0</td>
      <td>2</td>
      <td>Montvila, Rev. Juozas</td>
      <td>male</td>
      <td>27.0</td>
      <td>0</td>
      <td>0</td>
      <td>211536</td>
      <td>13.0000</td>
      <td>NaN</td>
      <td>S</td>
      <td>2_male</td>
      <td>2_nan</td>
      <td>2_S</td>
      <td>male_nan</td>
      <td>male_S</td>
      <td>nan_S</td>
    </tr>
    <tr>
      <td>888</td>
      <td>1</td>
      <td>1</td>
      <td>Graham, Miss. Margaret Edith</td>
      <td>female</td>
      <td>19.0</td>
      <td>0</td>
      <td>0</td>
      <td>112053</td>
      <td>30.0000</td>
      <td>B42</td>
      <td>S</td>
      <td>1_female</td>
      <td>1_B42</td>
      <td>1_S</td>
      <td>female_B42</td>
      <td>female_S</td>
      <td>B42_S</td>
    </tr>
    <tr>
      <td>889</td>
      <td>0</td>
      <td>3</td>
      <td>Johnston, Miss. Catherine Helen "Carrie"</td>
      <td>female</td>
      <td>NaN</td>
      <td>1</td>
      <td>2</td>
      <td>W./C. 6607</td>
      <td>23.4500</td>
      <td>NaN</td>
      <td>S</td>
      <td>3_female</td>
      <td>3_nan</td>
      <td>3_S</td>
      <td>female_nan</td>
      <td>female_S</td>
      <td>nan_S</td>
    </tr>
    <tr>
      <td>890</td>
      <td>1</td>
      <td>1</td>
      <td>Behr, Mr. Karl Howell</td>
      <td>male</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>111369</td>
      <td>30.0000</td>
      <td>C148</td>
      <td>C</td>
      <td>1_male</td>
      <td>1_C148</td>
      <td>1_C</td>
      <td>male_C148</td>
      <td>male_C</td>
      <td>C148_C</td>
    </tr>
    <tr>
      <td>891</td>
      <td>0</td>
      <td>3</td>
      <td>Dooley, Mr. Patrick</td>
      <td>male</td>
      <td>32.0</td>
      <td>0</td>
      <td>0</td>
      <td>370376</td>
      <td>7.7500</td>
      <td>NaN</td>
      <td>Q</td>
      <td>3_male</td>
      <td>3_nan</td>
      <td>3_Q</td>
      <td>male_nan</td>
      <td>male_Q</td>
      <td>nan_Q</td>
    </tr>
  </tbody>
</table>
<p>891 rows × 17 columns</p>
</div>



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
      <th>Pclass_Sex</th>
      <th>Pclass_Cabin</th>
      <th>Pclass_Embarked</th>
      <th>Sex_Cabin</th>
      <th>Sex_Embarked</th>
      <th>Cabin_Embarked</th>
    </tr>
    <tr>
      <th>PassengerId</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>NaN</td>
      <td>S</td>
      <td>3_male</td>
      <td>3_nan</td>
      <td>3_S</td>
      <td>male_nan</td>
      <td>male_S</td>
      <td>nan_S</td>
    </tr>
    <tr>
      <td>2</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
      <td>1_female</td>
      <td>1_C85</td>
      <td>1_C</td>
      <td>female_C85</td>
      <td>female_C</td>
      <td>C85_C</td>
    </tr>
    <tr>
      <td>3</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>NaN</td>
      <td>S</td>
      <td>3_female</td>
      <td>3_nan</td>
      <td>3_S</td>
      <td>female_nan</td>
      <td>female_S</td>
      <td>nan_S</td>
    </tr>
    <tr>
      <td>4</td>
      <td>1</td>
      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>113803</td>
      <td>53.1000</td>
      <td>C123</td>
      <td>S</td>
      <td>1_female</td>
      <td>1_C123</td>
      <td>1_S</td>
      <td>female_C123</td>
      <td>female_S</td>
      <td>C123_S</td>
    </tr>
    <tr>
      <td>5</td>
      <td>3</td>
      <td>Allen, Mr. William Henry</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>373450</td>
      <td>8.0500</td>
      <td>NaN</td>
      <td>S</td>
      <td>3_male</td>
      <td>3_nan</td>
      <td>3_S</td>
      <td>male_nan</td>
      <td>male_S</td>
      <td>nan_S</td>
    </tr>
    <tr>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <td>709</td>
      <td>1</td>
      <td>Cleaver, Miss. Alice</td>
      <td>female</td>
      <td>22.0</td>
      <td>0</td>
      <td>0</td>
      <td>113781</td>
      <td>151.5500</td>
      <td>NaN</td>
      <td>S</td>
      <td>1_female</td>
      <td>1_nan</td>
      <td>1_S</td>
      <td>female_nan</td>
      <td>female_S</td>
      <td>nan_S</td>
    </tr>
    <tr>
      <td>710</td>
      <td>3</td>
      <td>Moubarek, Master. Halim Gonios ("William George")</td>
      <td>male</td>
      <td>NaN</td>
      <td>1</td>
      <td>1</td>
      <td>2661</td>
      <td>15.2458</td>
      <td>NaN</td>
      <td>C</td>
      <td>3_male</td>
      <td>3_nan</td>
      <td>3_C</td>
      <td>male_nan</td>
      <td>male_C</td>
      <td>nan_C</td>
    </tr>
    <tr>
      <td>711</td>
      <td>1</td>
      <td>Mayne, Mlle. Berthe Antonine ("Mrs de Villiers")</td>
      <td>female</td>
      <td>24.0</td>
      <td>0</td>
      <td>0</td>
      <td>PC 17482</td>
      <td>49.5042</td>
      <td>C90</td>
      <td>C</td>
      <td>1_female</td>
      <td>1_C90</td>
      <td>1_C</td>
      <td>female_C90</td>
      <td>female_C</td>
      <td>C90_C</td>
    </tr>
    <tr>
      <td>712</td>
      <td>1</td>
      <td>Klaber, Mr. Herman</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>113028</td>
      <td>26.5500</td>
      <td>C124</td>
      <td>S</td>
      <td>1_male</td>
      <td>1_C124</td>
      <td>1_S</td>
      <td>male_C124</td>
      <td>male_S</td>
      <td>C124_S</td>
    </tr>
    <tr>
      <td>713</td>
      <td>1</td>
      <td>Taylor, Mr. Elmer Zebley</td>
      <td>male</td>
      <td>48.0</td>
      <td>1</td>
      <td>0</td>
      <td>19996</td>
      <td>52.0000</td>
      <td>C126</td>
      <td>S</td>
      <td>1_male</td>
      <td>1_C126</td>
      <td>1_S</td>
      <td>male_C126</td>
      <td>male_S</td>
      <td>C126_S</td>
    </tr>
  </tbody>
</table>
<p>713 rows × 16 columns</p>
</div>


### Pipeline


```python
# machine learning
from sklearn import feature_selection
from sklearn import preprocessing
# Pipeline code originally copied from 3_intermediate_training_summary
from helpers.helper import PipelineFS
from sklearn.impute import SimpleImputer
from sklearn.compose import ColumnTransformer
from sklearn.metrics import mean_absolute_error
from sklearn.preprocessing import StandardScaler
# conda install -c conda-forge category_encoders
from category_encoders import CatBoostEncoder
# conda install -c conda-forge lightgbm
import lightgbm as lgb
from sklearn.model_selection import cross_val_score

```


```python
def get_lgb_pipeline_score(X, y, params={'n_estimators':10,'num_leaves':64,'rate':0.1,'early_stopping_rounds':10}):
    """
    Run LightBGM pipeline on the provided parameters. 
    Scores based on cross_validation with 5 folds.

    params: Python object of params with the following keys
        n_estimators: number of estimators to use in pipeline, Default: 10
        num_leaves: num_leaves in lgb model, Default: 64
        rate: learning rate, Default: 0.1
        early_stopping_rounds: how many rounds to stop after if low variance, Default: 10
    """
    # Preprocessing for numerical data (fill in NA)
    numerical_transformer = PipelineFS(
        steps=[
            ('imputer', SimpleImputer(strategy='mean')),
            ('scaler', StandardScaler())
        ]
    )

    # Preprocessing for categorical data
    categorical_transformer = PipelineFS(
        steps=[
            ('imputer', SimpleImputer(strategy='most_frequent')),
            ('catboost', CatBoostEncoder())
        ]
    )

    # Bundle preprocessing for numerical and categorical data
    preprocessor = ColumnTransformer(
        transformers=[
            ('num', numerical_transformer, numerical_cols),
            ('cat', categorical_transformer, categorical_cols)
        ]
    )

    model = lgb.LGBMClassifier(n_estimators=params['n_estimators'], num_leaves=params['num_leaves'], learning_rate=params['rate'])

    # Bundle preprocessing and modeling code in a pipeline
    my_pipeline = PipelineFS(
        steps=[
            ('preprocessor', preprocessor),
            ('model', model)
        ],
        verbose=False
    )
    # Preprocessing of training data, fit model 
    # my_pipeline.fit(X_train, y_train)
    # cprint('Fit!', 'green')
    # Preprocessing of validation data, get predictions
    scores = cross_val_score(my_pipeline, X, y, cv=5)
    return scores.mean()
```


```python
# get_lgb_pipeline_score(X_train, y_train)

# results = {}
# params={'n_estimators':10,'num_leaves':64,'rate':0.1,'early_stopping_rounds':10}
# for i in range(50, 1001, 50):
#     params['n_estimators'] = i
#     results[i] = get_lgb_pipeline_score(X_train, y_train, params=params)
# print(results)
```

### Basic steps for the future
- Deal with missing values
- Deal with zeroes
- Encoding categorical variables
- Creating new features
    - Interactions
- Transforming features
    - Normalization/Outliers
- Feature Analysis
    - Feature types
    - How feature correlates to target
    - Graphs Graphs Graphs
- Model fitting
    - outlier removal
    - optimization

Also, should we use **Pipelines** from now on?
- [ ] Yes
- [ ] No

# Notes from example kernel
So far so good, but compared to others...not great. Below are some notes after looking at some example kernels.

- Combine the train and test data sets into a single dataframe to make feature transformation easier.
    - Get the ids (indexes) of both the test and train sets so that they can be extracted again later.
- When filling in N/A values, be sure to figure out what N/A ACTUALLY means. Do we need to persist that value as something else? ie, in housing data, if garage is N/A, that just means the house doesn't have a garage, which is data we want to persist 
- When creating features, it takes a little brain/common sense to figure out interactions. One way is to yes, just create a ton, but for finding INTERESTING interactions, you really gotta sit and think.
    - For example, with the housing data, instead of just comparing different area values. Large properties will have large areas with high sale prices by default. But what is more intersting maybe is the *fraction* of the base that is of each different room type.
- *GRAPHS*
    - USE SCATTERPLOTS. They are amazing for visualizing the correlation of a feature vs target.
    - Same as above, GRAPH GRAPH GRAPH. Another useful graph is graphing the different means of the target. This can help to find outliers.
    - Correlation. How do the current features correlate to the target?
- Optmization tricks: 
    - use a model to predict values for filling in NaN

















```python

```
