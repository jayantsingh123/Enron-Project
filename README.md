## Introduction and Background
Enron scandal, dating back to 2001 was one of the most darkest moments in US corporate world. Some of the stats include;

<img width="610" alt="Screen Shot 2019-05-01 at 10 42 43 PM" src="https://user-images.githubusercontent.com/27310613/57054418-86763780-6c62-11e9-85bf-e626b8a6c75b.png">.

Interestingly, it has been proved that the senior executives saw it coming and were indifferent to the stakes of common shareholders and employees. This is evident from above figure.
## Description of the Data
The present dataset is a condensed version of the actual Enron dataset. It has information regarding the different employees, POI's as well as non-POI's. The attributes include;
* **Bonus**: Bonus awarded to the Employees.
* **Deferral Payment**: Payment amount deferred to a future date
* **Deferred Income**: Income deferred for services provided to a future date
* **Director Fees**: Fees supposed to be charged for rendering services as a Director 
* **Exercised Stock Options**: Purchasing the stocks as per the option agreement.
* **Expenses**: Expenses which will be reimbursed
* **From messages**: Number of messages from the employee
* **To messages**: Number of messages to the employee
* **From POI to this person**: Number of messages sent from POI to this person
* **From this Person to POI**: Number of messages sent from this person to POI
* **Loan Advances**: Loan advances made to the employee
* **Long Term Incentive**: Compensation provided to the Employees for their contribution to stratefic objectives.
* **Other**: Miscellaneous expenses
* **Restricted Stock**: Nontransferable stocks that are assigned to the leadership
* **Restricted Stock Deferred**: Restricted stock compensation that is deferred to later date
* **Salary**: Salary of the employee
* **Shared Receipt with POI** : Number of messages shared with POI
* **Total Payments**: Total payment made to the employee
* **Total Stock Value**: Stock value for the employee
* **Name of the Employee**: Name of the employee
* **Email Address of the Employee**: Email address of the employee
* **POI**: Employee is a person of interest or not.

The given dataset has 143 records and 21 features. As seen above,the features can be grouped in three categories; Stock Related Features, Payment related features, and Interaction with other employees.

## Analysis and Discussion:
The present analysis is aimed at building a classification model, that can help to identify a POI from a non-POI. 

### Exploratory Data Analysis
To begin with, some exploratory results have been presented. Since POI is acategorical variable, and other predictors are mostly numeric, box plot or violin plot seems to be suitable choice.The EDA helps to get some information regarding relationship between different predictors. For example, our analysis helped us to conclude that following variables tend to be statistically significant while predicting whether an entity is POI or not; 

* Salary,
* Bonus,
* Long Term Incentive,
* Director Fees,
* Expenses,
* Exercised Stock Options,
* Restricted Stock,
* Total Stock Value,
* Number of Messages Received,
* Number of Messages Received from POI, and
* Number of Messages Shared with POI.

In addition, we also designed a correlation matrix to identify the highly correlated variables. It was found that following pairs are highly correlated;
* Exercised Stock Options and total_stock_value, and
* to_messages and shared receipt with poi.

### Model Building
Model building is obviously an iterative process. It is very common to begin with a base model, which performs poorly on the test set. Then a new model is designed and tested, and so forth. The process continues until we achieve desired performance.

#### Regularized Logistic Regression
As the first step, we opted for L1 Regularized Logistic Regression, also called Lasso Regression. This method is helpful, in the sense that it only keeps the relevant predictors in the model while discarding the rest. Lasso Regression has a parameter, called Regularization Pramater, *C*. This parameter is a tradeoff between the performance of the model on the training set and the model complexity. Huge volumes of literature is available on this subject and can be referred to for details. An interesting as well as natural question to ask is, what should be the value of *C*? This can be calculated using some type of cross validation. Please refer to sickit learn for details.

Once the model has been trained, it is supposed to be tested on the held out data, i.e. test set. In order to compare the performance of the model, the Receiver Operator Characteristics (ROC) curve approach has been used. The ARea under this ROC curve (also called AUC) is a very useful metric and can be used in various situations. This approach has following main benefits;
* It is threshold independent, and
* It is independent of class distribution.

In adition to above benefits, ROC approach can also be used to select a suitable threshold probability for classification problems. This will be discussed in later sections in more details. Coming back to the original problem of model asessment, a higher AUC indicates a better model. Using the Regularization approach, we obtained an AUC score of 0.7. This means that there is 70 percent probability that given a pair of positive and negative example, the model will give more weightage to the positive example.
