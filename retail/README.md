# Retail Expansion Analysis with Lasso and Ridge Regressions

<p align="center">
  <img src="images/liquor.jpeg">
</p>        
<br>





&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
![image title](https://img.shields.io/badge/work-in%20progress-blue.svg) ![image title](https://img.shields.io/badge/statsmodels-v0.8.0-blue.svg) ![Image title](https://img.shields.io/badge/sklearn-0.19.1-orange.svg) ![Image title](https://img.shields.io/badge/seaborn-v0.8.1-yellow.svg) ![Image title](https://img.shields.io/badge/pandas-0.22.0-red.svg) ![Image title](https://img.shields.io/badge/numpy-1.14.2-green.svg) ![Image title](https://img.shields.io/badge/matplotlib-v2.1.2-orange.svg)
<br>



### Summary
Based on a dataset containing the spirits purchase information of Iowa Class E liquor licensees by product and date of purchase (link) this project provides recommendations on where to open new stores in the state of Iowa. To devise an expansion strategy, I first needed to understand the data and for that I conducted a thorough exploratory data analysis (EDA). With the data in hand I built multivariate regression models of total sales by county, using both Lasso and Ridge regularization, and based on these models, I made recommendations about new locations. 

### Preamble
Expansion plans traditionally use subsets of the following mix of data:
#### Demographics
In the EDA, I focused on the following quantities:
- The ratio between sales and volume for each county i.e. the amount of dollars per liter sold. If this ratio is high in a given county, the stores in that county are, on average, high-end stores. 
- Another relevant ratio is the number of stores per area. The meaning of a high value of this ratio is not so straightforward since it may indicate either that the market is saturated, or that the county is a strong market for this type of product and would welcome a new store (an example would be a county close to some major university). In contrast, a low value may indicate a market with untapped potential or a market with a population not very interested in this type of store.
- Another important ratio is consumption/population i.e. the consumption *per capita*. The knowledge of the profile of the population in the county (if they are "light" or "heavy" drinkers) would certainly help the owner decide whether to open or not a new storefront there.

#### Nearby businesses

Competition is a critical component, and can be indirectly measured by the ratio of the number of stores and the population.

#### Aggregated human ﬂow/foot traffic

For this information to be useful we would need more granular data such as e.g. apps check-ins. Population and population density will be used as proxies.

### Getting data

Three datasets were used namely:
- A dataset containing the spirits purchase information of Iowa Class “E” liquor licensees by product and date of purchase.
- A dataset with information about population per county
- A database containing information about incomes

### Data Munging and EDA

#### Checking the time span of the data and dropping 2016 data

To see how many years and months for each year are contained in the data we use the code lines below. We see that there is data for only two years, namely, 2015 and 2016 and that the data includes all 12 months of 2015 but only 3 months of 2016 (the 2016 data was therefore dropped):
```
df["date"] = pd.to_datetime(df["date"], format="%m/%d/%Y")
for yr in [2015,2016]:
    print "The months in year {}".format(yr),"are", sorted(df[df['date'].dt.year == yr]['date'].dt.month.unique())
df = df[(df['date'] < '2016-01-01')]
```
### b) Eliminating symbols in the data, dropping `NaNs` and converting objects to floats

The following data munging steps were performed: exclusion of symbols from the `DataFrame` (e.g. some of the columns included dollar signs which naturally do not allow for algebraic manipulation), conversion of columns of objects into columns of float, dropping `NaN` values from `df` and converting store numbers to strings. To exclude dollar signs for example I used:
```
for col in cols_with_dollar:
    df[col] = df[col].apply(lambda x: x.strip('$')).astype('float')
```
To look at histogram I used the following convenient function:
```
def draw_histograms(df,col,bins):
    df[col].hist(bins=bins);
    plt.title(col);
    plt.xlabel(col);
    plt.xticks(rotation=90);
    plt.show();
```
The maximum values in all columns were many standard deviations larger than the mean, indicating the presence of outliers. Keeping outliers in the analysis would inflate the predicted sales. Also, I wanted to predict the *most likely performance* for each store and did not want to use exceptionally well-performing stores to make recommendations:
```
cutoffs = (df['bottle_volume_ml'] > 0) & (df['bottles_sold'] < 50) & (df['volume_sold_liters'] < 60) & (df['sale_dollars'] < 900)
df = df[cutoffs]
```
### Mining the data

Some of steps for mining the data included: computing the total sales per county, creating a profit column, calculating profit per store and the sales per volume, dropping outliers, calculating both stores per person and alcohol consumption per person ratios.

### Refining the data

In this section I looked for any statistical relationships, correlations, or other relevant properties of the dataset. First I needed to choose the proper predictors. I looked for strong correlations between variables to avoid problems with multicollinearity. Also, variables that changed very little had little impact and they were therefore not included as predictors. I then studied correlations between predictors. I saw from the correlation matrices that `num_stores` and `stores_per_area` are highly correlated. Furthermore, both are highly correlated to the target variable `sale_dollars`. Both things also happen with `store_population_ratio` and `consumption_per_capita`.


   <img src="https://github.com/marcotav/retail-store-expansion-analysis/blob/master/hm3.png" width="500">

```
cols_to_keep = ['num_stores', 'store_population_ratio', 'consumption_per_capita', 'stores_per_area', u'per_capita_income']
df[cols_to_keep].corr()
sns.heatmap(df[cols_to_keep].corr())
```
To generate scatter plots for all the predictors (which provided similar information as the correlation matrices) we use `Seaborn`:
```
g = sns.pairplot(df[cols_to_keep])
for ax in g.axes.flatten():    # from [6]
    for tick in ax.get_xticklabels(): 
        tick.set(rotation=90);
```

   <img src="https://github.com/marcotav/retail-store-expansion-analysis/blob/master/output.png" width="900">


### Building the models

Using `scikit-learn` and `statsmodels`, I built the necessary models and valuated their fit. For that I generated all combinations of useful relevant features using the `itertools` module.
```
features = ['num_stores','population', 'store_population_ratio', \
 'consumption_per_capita',  'stores_per_area', u'per_capita_income']
# defining the predictors and the target
X,y = df_final[features], df_final['sale_dollars']
# next I do a train-test split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=.3)

# generating combinations of features
combs = []
for num in range(1,len(features)+1):
    combs.append([i[0] for i in list(itertools.combinations(features, num))])
```

After splitting the data into train and test data, I instantiated the models and tested them. The code below makes a list of `r2` combinations and finds the best predictors using `itemgetter`:
```
lr = linear_model.LinearRegression(normalize=True)
ridge = linear_model.RidgeCV(cv=5)
lasso = linear_model.LassoCV(cv=5)
models = [lr,lasso,ridge]
r2_comb_lst = []
for comb in combs:        
    for m in models:
        model = m.fit(X_train[comb],y_train)
        r2 = m.score(X_test[comb], y_test)
        r2_comb_lst.append([round(r2,3),comb,str(model).split('(')[0]])
        
r2_comb_lst.sort(key=operator.itemgetter(1))
```
The best predictors were obtained via:
```
r2_comb_lst[-1][1]
```
Dropping highly correlated predictors I redefined `X` and `y` and built a Ridge model
```
X ,y = df_final[features], df_final['sale_dollars']
ridge = linear_model.RidgeCV(cv=5)
model = ridge.fit(X,y)
```

### Plotting results

I then plotted the predictions versus the true value:

   <img src="https://github.com/marcotav/retail-store-expansion-analysis/blob/master/test.jpg" width="700">

### Conclusions and recommendations:
- Linn has higher sales which in part is because it has larger population which is not very useful information. 
- Next, ordering stores by `sales_per_litters` we obtain which counties have more high-end stores (Johnson has the higher number).
- We would recommend Johnson for a new store *if the goal of the the owner is to build new high-end stores*. 
- If the plan is to open more stores but with cheaper products, Johnson is not the place to choose. The less saturated market is Decatur. But as discussed before this information does not provide have a unique recommendation and a more thorough analysis is needed. 
- The county with weaker competition is Butler. This could provided untapped potential. However, the absence of a reasonable number of stores may indicate, as observed before, that the county's population is simply not interested in this category of product. Again, further investigation must be carried out.


