## [Machine Learning Regression Models](http://nbviewer.jupyter.org/github/marcotav/machine-learning-regression-models/blob/master/retail/notebooks/retail-recommendations.ipynb) ![Image title](https://img.shields.io/badge/sklearn-0.19.1-orange.svg) ![Image title](https://img.shields.io/badge/seaborn-v0.8.1-yellow.svg) ![Image title](https://img.shields.io/badge/pandas-0.22.0-red.svg) ![Image title](https://img.shields.io/badge/matplotlib-v2.1.2-orange.svg)

### Index

* [retail-store-expansion-analysis-with-lasso-and-ridge-regressions](#retail-store-expansion-analysis-with-lasso-and-ridge-regressions)
* [conjoint-analysis](#conjoint-analysis)

### retail-store-expansion-analysis-with-lasso-and-ridge-regressions

<br/>
<p align="center">
  <img src='https://github.com/marcotav/machine-learning-regression-models/blob/master/retail/images/liquor.jpeg' width="200">
</p>
<br>



Based on a dataset containing the spirits purchase information of Iowa Class E liquor licensees by product and date of purchase this project provides recommendations on where to open new stores in the state of Iowa. To devise an expansion strategy, I first needed to understand the data and for that I conducted a thorough exploratory data analysis (EDA). With the data in hand I built multivariate regression models of total sales by county, using both Lasso and Ridge regularization, and based on these models, I made recommendations about new locations. 

I strongly recommend reading the notebook using [nbviewer](http://nbviewer.jupyter.org/github/marcotav/machine-learning-regression-models/blob/master/retail/notebooks/retail-recommendations.ipynb).

### conjoint-analysis

Conjoint analysis is a technique that allows researchers to predict consumers' choice share. The analysis can be programmed using standard question types, such as the MaxDiff variation of the Matrix Table question. Instead of directly asking the survey respondents which attributes they find most relevant, conjoint analysis asks respondents to evaluate potential product profiles which include multiple product features 

There are several ways to show to respondents the product profiles. In Choice-Based Conjoint (CBC) respondents are shown multiple product conceptsn and asked which option they would choose. By varying the features shown to the respondents and observing their responses to the product profiles, one can statistically deduce the most desired product features and which attributes have the most impact on choice. 

The end result is a set of preference scores or *part-worth utilities* for each level of each attribute.

In this notebook I show how to use Python to calculate the utilities. The notebook is heavily based on [this course](https://www.lynda.com/R-tutorials/Data-Science-Marketing/533306-2.html) and [this book](https://www.amazon.com/Marketing-Data-Science-Techniques-Predictive/dp/0133886557).
