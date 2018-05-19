## Conjoint Analysis 
![image title](https://img.shields.io/badge/work-in%20progress-blue.svg) ![image title](https://img.shields.io/badge/statsmodels-v0.8.0-blue.svg) ![Image title](https://img.shields.io/badge/sklearn-0.19.1-orange.svg) ![Image title](https://img.shields.io/badge/pandas-0.22.0-red.svg) ![Image title](https://img.shields.io/badge/numpy-1.14.2-green.svg) ![Image title](https://img.shields.io/badge/matplotlib-v2.1.2-orange.svg) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)


<p align="center">
  <a href="#basic"> Basic Assumptions </a> •
  <a href="#steps"> Steps </a> •
  <a href="#imports"> Importing stuff </a>  
</p>

<a id = 'basic'></a>
### Basic Assumptions

The basic assumptions of Conjoint Analysis are:
- Product are a bundle of attributes
- The utility of a product is a (simple) function of the utilities of each of its attributes
- Behavior such as purchases can be predicted from utilities

<a id = 'steps'></a>
### Steps

- One must first choose the attributes to be included 
- The number of levels for each attribute must also be chosen
- Definition of hypothetical products (all combinations of attribute levels would generate too many products)
- One should make sure that:
 - All combinations of levels for pairs of attributes occur in some product 
 - The subset of products should have orthogonal design i.e. the chances of finding a given level of some attribute B in a product should be the same irregardless of the level of another attribute A. 
- Estimation of utilities (usually using ordinary linear regression with dummy variables)

The linear regression model with conjoint preference data has the form:

<p align="center">
  <img src="images/conjoint_reg.png" width='175'>
</p>        
<br>

where, `R_i` is the ranking/rating assigned to product i, `X_{ij}^k` is a dummy variable which is 1 if product i has level j on attribute k and `u_j` is the utility coefficient for level j on attribute k. 


## To be continued.
