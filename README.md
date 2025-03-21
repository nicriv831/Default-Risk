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

Due to the large numbers of columns and rows, I intially chose to only choose columns that had zero nulls. I started with the application_train sheet as this included our target and SK_ID_CURR, which is the unique identifier. It is also the most current application.

I chose to next move to the credit bureau specific datasheets - bureau and bureau_balance. My prior experience as a loan officer has taught me that the most up-to-date and accurate information comes from the credit bureaus, rather than self-reported information on applcations.

After working through the bureau sheets, I moved to previous applications. Once this sheet was cleaned and preprocessed, I was able to get a ROC AUC above .70 without needing to use any of the other spreadsheets.

Once my minimum ROC AUC was achieved, I moved on to feature engineering and model tuning to see what results different models produced.

### 3ii - Analyze Data <a name="analyze-data"></a>

#### "Default Rate" of Target in train set - 8.07%

The number of loans that had a "1" in the target, which signifies payment issues or default, was approx. 8.07%. I am using the catch-all term "default rate" for this going forward. I believe it is important to note that these were not actual defaults or charge-offs of the loans. However, they were showing probelms with payments, which are the initial steps prior to a default.

According to the Mortgage Bankers Association, Q4 2024 mortgage default rates were as follows[^1]:

_* Conventional - 2.62%_

_* FHA - 11.03%_

_* VA - 4.07%_

It makes sense that the default rate would be higher than conventional loans. It is also a good sign that the default rate is lower than government subsidized loans like FHA.

#### Days Credit

The days credit feature of the bureau spreadsheet listed how many days before the most current application teh client applied for any other type of credit.

I wanted to dig into this a little more. My reasoning was that it is usually advised that one does NOT apply for other types of credit before taking out a large loan. This is partially because it can hurt your credit score and raise the interest rate on the larger loan.

Additionally, I have the assumption that even if you are able to qualify for the large loan, attempting to get other types of credit right before could end up putting someone in over their heads with payments. Someone who is applying for multuiple types of credit close together could very well be anticipating an upcoming cash crunch and is trying to borrow money in advance to get themselves out of the cash crunch. A cash crunh can very well lead to a loan default.

I first chose to bin the days credit column in 25 day increments:

![Default Rate by Days Since Last Application](Images/DefaultbyCreditApp.png)

In line with my assumption, there was a trend toward higher default rates as the days since last credit application rose. What I found intersting was that the only section that had a higher default rate than the average for the whole set was 0-250 days since the last application.

I then chose to take another look, but this time break out 250 days and under in 25 day increments

![Default Rate by Days Since Last Application - 250 and less](Images/DefaultByCreditApp250.png)

This chart proved to show a similar distinct trend in this sample -- as applications for other credit crept closer to the loan application, the default rate went up.

There was a difference between the first chart looking at all Days Credit and this one breakingo ut 250 days and under. The default rate was above the full dataset's average (8.07%) for all increments under 250 days with the exception of 226-250.

I found this trend to be powerful enough that I included this information as an engineered feature for the model

#### Previous Applications

While combing the previous application dataset, I noticed that some clients had filled out up to 60 applications.

This number seemed quite high, so I decided to take a look at the default rate based on how many previous applications were filled out.

My assumptions for doing so were the same as the Days Credit column - that more applications, especially if they are a lot more than others, might show that someone is fishing for ways to get cash and is getting declined due to lack of creditworthiness

![Default Rate by Previous Applications](Images/DefaultRatebyPrevApps.png)

This also showed a distinct trend that was in line with my hunch - as the number of previous applications increased, so did the default rate in the dataset.

Even more interesing to me was that the default rate for 0-10 applications was just barely beow the entire dataset's average, while every other bin above 10 applications showed a higher default rate than teh entrie dataset's average.

I also chose to use this information as an engineered feature.

### 3iii - Preprocess Data for Model <a name="preprocess-data-for-model"></a>
*[Click here](Model_Testing.ipynb) to jump to the notebook containing preprocessing and model testing*

Data was preprocessed according to above outline with the intention of running through a linear regression, decision tree and random forest models.

#### Aggregation Methods/Features Added

* Bureau dataset - aggregated by Days Credit Update - most recent bureau information
* Bureau Balance dataset - aggreagated by Months Balance - most recent balance info
* Previous Application dataset - aggregated by Days Decision - most recent credit decision
* Debt-to-income - added colums dividing monthly annuity payment by monthly income
* Days Credit - binned days since last credit aplication in increments of 25 days and added column
* Previous Applications - binned count of previous applications and added column

### 3iv - Model Analysis/Prediction <a name="model-prediction/analysis"></a>

*[Click here](Final_Model_w_Feature_Selection.ipynb) to jump to the notebook containing only the final model*

I ran the data through three separate model types -- a logistic regression, a decision tree and a random forest.

I tuned the decision tree and random forest to their best depth. This was because both those models were overfitting without any depth tuning.

I have a function for the appropriate metrics in my model tuning sheet, the final results were as follows:

![Model Metrics](Images/ModelChart.png)

In this particular case, simple proved to be better as the Logistic Regression with a balanced class returned the highest ROC AUC score.

I ran all these models through the final Kaggle calculator as well. For the logistic regression, I also ran the features through a collinearity feature selection algorithm. 40 columsn were dropped. The score was just a hair lower than the non-feature selected sheet, so I used the full feature sheet as there was not a noticeable tradeoff in comutational efficiency.

In other words, the logistic regression with all the features included had the hgihest score of all 6 models I tested.

#### Final Kaggle ROC AUC for Logistic Regression with All Features  -  0.71175
#### Final Kaggle ROC AUC for Logistic Regression with All Features  -  0.70936

## 4 - Next Steps <a name="next-steps"></a>

I think I can imporve the accuracy score, especially because I have more data and spreadsheets I can incorporate.

I would also like to take some more time getting creative with feature engineering. With this much data there are all kinds of different ways we can chop the data up to reveal more information.

## References <a name="references"></a>

_Foreclosure Image_ - <a href="https://commons.wikimedia.org/wiki/File:Foreclosure_sign.jpeg">Casey Serin</a>, <a href="https://creativecommons.org/licenses/by/2.0">CC BY 2.0</a>, via Wikimedia Commons

[^1]: <a href="https://www.mba.org/news-and-research/newsroom/news/2025/02/06/mortgage-delinquencies-increase-in-the-fourth-quarter-of-2024">Mortgage Bankers Association</a>








