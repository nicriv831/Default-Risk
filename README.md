# Credit Default Risk

<a title="Casey Serin, CC BY 2.0 &lt;https://creativecommons.org/licenses/by/2.0&gt;, via Wikimedia Commons" href="https://commons.wikimedia.org/wiki/File:Foreclosure_sign.jpeg"><img width="512" alt="Foreclosure sign" src="https://upload.wikimedia.org/wikipedia/commons/thumb/6/66/Foreclosure_sign.jpeg/512px-Foreclosure_sign.jpeg?20081011025455"></a>

## Table of Contents

1. [Introduction](#introduction)
2. [Data](#data)
3. [Process Outline](#process-outline)
   1. [Initial Observation/Clean](#clean-data)
   2. [Analyze Data](#analyze-data)
   3. [Preprocess Data for Model](#preprocess-data-for-model)
   4. [Model Analysis/Prediction](#model-prediction/analysis)
4. [Next Steps](#next-steps)
5. [References](#references)

## 1 - Introduction <a name="introduction"></a>

Home Credit is a company that strives to give loans to the unbanked. The unbanked struggle to get loans because their credit histories are insufficient or non-existent.

They want to create a model that will help predict their clients' repayment ablities. They have a large variety of data on both traditional and non-traditional udnerwriting features.

Their goal is to find a model with at least a .70 Area Under the Receiver Operating Characteristic Curve **(ROC AUC)** score.

## 2 - Data <a name="data"></a>

They provided 9 different datasets that range between millions of rows and hundreds of columns.

The datasets are too large to commit to GitHub, but they can be found and downloaded from <a href="https://www.kaggle.com/competitions/home-credit-default-risk/data">here</a>

## 3 - Process Outline <a name="process-outline"></a>

Process consisted of cleaning, analyzing, and preprocessing for model.

Due to the size of the datasets, each needed to be handled one-by-one and merged down in an appropriate way with the train sets.

The test set was provided as its own standalone sheet. Careful attention was paid regarding how data was preprocessed and merged with the test sets, to avoid leakage and to ensure train and test set features matched for the model.

### 3i - Initial Observations/Clean <a name="clean-data"></a>

*[Click here](EDA_Default_Risk.ipynb) to jump to the notebook containing Exploratory Data Analysis*


### 3ii - Analyze Data <a name="analyze-data"></a>
DEFAULT RATE IN TRAIN 8.07%

### 3iii - Preprocess Data for Model <a name="preprocess-data-for-model"></a>

### 3iv - Model Analysis/Prediction <a name="model-prediction/analysis"></a>

## 4 - Next Steps <a name="next-steps"></a>

## References <a name="references"></a>

_Foreclosure Image_ - <a href="https://commons.wikimedia.org/wiki/File:Foreclosure_sign.jpeg">Casey Serin</a>, <a href="https://creativecommons.org/licenses/by/2.0">CC BY 2.0</a>, via Wikimedia Commons

Include statsitics from mortgage bankers association

Raw files and cleaned train/test sets are too large for github. Use link to Kaggle to provide raw data that will lead to cleaned data

DAys credit
Outline that bins above 250 are all less than the averag default rate so theyve been binend togtehr

My client's business does home loans for the unbanked. They collect a variety of traditional and non-traditional data that they use to make their approval decisions. They're looking to ensure the prospective borrowers with the best capability to repay are approved.

The population the client caters to are underserved in the financial world, and our client's goal is to help that undersevred population have access to resources they may not normally have access to. They also want to ensure loans are setup in a way that decreases the chance of a default.

I will analyze the various datasets provided by the client with the goal of creating a model that accurately predicts default risk.

The client has specified they want a model with the area under the ROC curve (ROC AUC) of at least .70 between the predicted probability and the observed target.
