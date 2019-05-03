## Introduction and Background
Enron scandal, dating back to 2001 was one of the most darkest moments in US corporate world. Some of the stats include;

<img width="610" alt="Screen Shot 2019-05-01 at 10 42 43 PM" src="https://user-images.githubusercontent.com/27310613/57054418-86763780-6c62-11e9-85bf-e626b8a6c75b.png">.

Interestingly, the senior executives saw it coming and were indifferent to the stakes of common shareholders and employees. This is evident from above figure.
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
* **Long Term Incentive**: Compensation provided to the Employees for their contribution to strategic objectives.
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
The present analysis is aimed at building a classification model, that can help to identify a POI from a non-POI. On the sidelines, we will also focus on selection of optimal threshold probability for classification.

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
Model building is obviously an iterative process. It is very common to begin with a basic model, which performs poorly on the test set. Then a new model is designed and tested, and so forth. The process continues until we achieve desired performance.

#### Regularized Logistic Regression
As the first step, we opted for L1 Regularized Logistic Regression, also called Lasso Regression. This method is helpful, in the sense that it only keeps the relevant predictors in the model while discarding the rest. Lasso Regression has a parameter, called Regularization Pramater, *C*. This parameter is a tradeoff between the performance of the model on the training set and the model complexity. Huge volumes of literature is available on this subject and can be referred to for details. An interesting as well as natural question to ask is, what should be the value of *C*? This can be calculated using some type of cross validation. Please refer to sickit-learn documentation for details.

Once the model has been trained, it is supposed to be tested on the held out data, i.e. test set. In order to compare the performance of the model, the Receiver Operator Characteristics (ROC) curve approach has been used. The Area under this ROC curve (also called AUC) is a very useful metric, and can be used in various situations. This approach has following main benefits;
* It is threshold independent, and
* It is independent of class distribution.

In adition to above benefits, ROC approach can also be used to select a suitable threshold probability for classification problems. This will be discussed in later sections in more details. Coming back to the original problem of model asessment, a higher AUC indicates a better model. Using the Regularization approach, we obtained an AUC score of 0.7. This means that there is 70 percent probability that given a pair of positive and negative example, the model will give more weightage to the positive example. The ROC curve has been used to find the threshold probability, which maximizes the difference between true positive rate and false positive rate. Since the obtained threshold was quite small, the model provided perfect recall at the cost of low precision.
In an attempt to improve the recall without sacrificing the precision, it is worthwhile attempting a better model.

#### Feature Engineering
It has been often seen that generating additional information using original set of attributes helps to increase the predictive ability of the model. For the present analysis, we have defined new features involving ratio and sum of the features related to interaction among users. The new features include,
* **ratio of from messages to received messages**: is the POI receiving more messages than he/she is sending.
* **proportion_from_poi**: what proportion of messgaes received by an employee is from POI.
* **total number of messages**: gives an overall activity of the employee
* **shared with POI prop**: Out of all messages received, what fraction was shared with POI
* **Bonus Salary Sum** : Sum of Bonus and Salary
* **Bonus Salary Ratio** : Ratio of Bonus to Salary

Since these engineered features are designed using the original attributes, it makes sense to drop the original ones. Next, as a check, are we still suffering from multicollinearity? In the correlation matrix, following pairs seem to be highly correlated;

* total payments and loan advance,
* exercised stock options and total stock value.

Since these features do not look similar in nature, it is bit strange for them to have high correlation. They are not similar on the surface, hence we shdn't drop one of them blindly. It is a better idea to use Principal Component Analysis (PCA) here.

#### Principal Component Analysis

PCA approach helps to reduce the dimensionality of the data, while preserving the important information. It is based on the concept of Singular Value Decomposition (SVD) of the data matrix. Please refer to any undegraduate textbook on Linear Algebra for details. For the present example, we apply PCA on one pair at a given time, and keep one vector (i.e. principal component). This hopefully preserves maximum variation in the data.
Combining Feature Engineering, PCA together with basic Logistic Regression we obtain perfect recall but low precision.

##### Discussion About Results

The performance of a model can be viewed as more of a business problem rather than technical problem. For instance, in a cancer classification problem, the question to ask ourselves is; are we okay with our model predicting lots of false positive cases? The answer obviously is Yes. We would not like the model to fail to detect the abnormal growth of tissue in the patient. It is acceptable for the model to predict few false positives. In this case the patient can go for more intensive medical check-up and get the correct results.

Similarly, for the present discussion, we would like our model not to miss any Person of Interest, though it missclassifies few non-POI's. This accounts for high recall but a low precision score.

#### Random Forests

Random forests have been a popular classification algorithm which is least likely to overfit because of averaging as well as low bias. On top of that, it has an in-built feature selection algorithm. To begin with, we will construct a simple random forest classifier. No feature engineering will be introduced. Only original set of features will be used.
One of the main aspects of implementing Random Forests is selection of optimal value of the hyperparameters.  For our discussion, we focus on tuning following hyper parameters; number of Decision trees(i.e. estimators), number of features selected for splitting at a tree node, and maximum depth of the tree.

##### Hyperparameter Tuning Using Novel Approach

The above mentioned hyperparameters can be tuned using cross validation together with AUC as the performance metric. AUC has been chosen as the metric because of the reasons mentioned earlier. In addition to tuning of hyperparameters, we use AUC to find the suitable value of threshold probability. This is a novel approach discussed in our analysis.

In most of the instances, we use the in-built methods, i.e. gridsearchcv or randomized search to find the optimal values for hyperparameters. The method divides the data into folds, and calculates the average of the suitable metric across all folds to choose the optimal values for hyper parameter. But if the dataset is considerably small, as in our case, dividing the dataset into several folds does not provide enough points in each validation set. Therefore, a different approach has been implemented here. Here are the main steps of this algorithm:

* For each parameter combination;
    * Perform 5-fold validation on train set,
    * Predict probabilities on each fold,
    * Concatenate the predicted probbailities to get enough sample size,
    * Use the concatenated list of predicted probabilities together with the entire set of true labels to obtain the AUC.
    
Using this approach, we get an opportunity to test the performance of a given parameter combination on significantly larger data. At the same time, it provides a smoother ROC curve. The set of parameters that provides highest AUC is chosen as the optimal set. Moreover, we plot the histogram for the predicted probabilities corresponding to value of hyper parameters. Using this histogram, we can identify the threshold probability to be used on the test set.

## Conclusion

To summarize, we explored the Enron dataset looking for relations between the attributes. Different types of graphics such as bar plot, violin plot, and scatter plot have been used to explore the relationship between the dependant variable and the predictors. Next, few modeling strategies have been included. The models provided perfect recall but a low precision score on this data. This performance is acceptable keeping in mind the problem at hand. Here, the primary focus is to identify all the POI's for sure. To this end, we use low threshold probability for classification, which gives few false positives too.

In addition, a novel approach has been presented for tuning of hyperparameters. This method is a slight variation of typical cross-validation and is more useful for very small datasets. Moreover, in conjunction with AUC, it can also be used to select the threshold probability for classifucation purposes.



