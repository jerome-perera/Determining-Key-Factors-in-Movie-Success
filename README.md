

By: Simon Mei, Walter Disharoon, Jerome Perera, Ankith Kanderi, and Himnish Kapoor

Project Proposal Recording:
[Youtube Link](https://youtu.be/j-8RxawvbOY)

Final Project Recording:
[Youtube Link](https://youtu.be/ap8NlHKaNpw)

### **Introduction:**  

Currently, the primary goal of movies is to maximize profit for the studios and companies who create them. In order to achieve this goal, movie studios want to use machine learning to maximize box office revenue.   

Nowadays, Hollywood has already become familiar with machine learning and there exist models to estimate a films’ projected box office[^1] as well as a movie’s projected rating[^2]. However, while there exist lots of predictive and generative forms of machine learning used in the industry, our project will focus more on using data to determine key factors that affect a movie’s revenue.  

Our proposed dataset is The Movies Dataset from Kaggle by Rounak Banik[^3]. This dataset contains metadata for 45,000 movies. Some of the important features of this dataset include things such as casting crew, directors, advertising budget, and, most importantly to our project, revenue. 

### **Problem Definition:**  

The goal of this project is to use both supervised and unsupervised learning to determine the various elements, and their corresponding importance, that contribute to a monetarily successful movie. More specifically, the findings of this project should give movie studios a better understanding of factors to consider when creating movies. This is different from the many other movie-related models out there that simply state whether a movie is expected to be successful or not, because the findings of this project are more about providing the movie industry with information for future decisions.  

### **Data Collection, Cleaning, and Visualization**
The movies dataset contains metadata for over 45,000 movies. Unfortunately though the dataset is provided in multiple separate csv's in a somewhat unwieldy state. The data collection and cleaning process was as follows:

1. import the csv's as pandas dataframes
2. remove entries from the csv's with duplicate movie IDs
3. Drop non-sensical features for prediction such as: alternative id, homepage, poster_path, status, tagline, etc.
4. Replace missing/empty features with NaN. This will make later training easier

Once this step is complete, many of the features that could be useful are saved in a JSON format that is not useful for training. So we:

5. Since we are trying to predict movie profitability we would want to cull features that come after the release of the movie such as voting averages and vote count; and features that are uncontrollable like "release year".
6. Extract and interpret the JSON's in the credits.csv into new features such as "Director", "Cast List", "Crew Size", "Cast Size".
7. Extract the month published from the dates column
8. Extract the output "Profit" as "Revenue" - "Budget".

Unfortunately only 5371 entries out of the initial 45000 have data for revenue and budget to extract profit, which does severly lower the dataset size. After acquiring this initial cleaned dataset the features present are:

1. Budget
2. Production Countries
3. Runtime
4. Spoken Languages
5. Cast and Crew sizes
6. Director
7. Release Month
8. List of Cast
9. List of Production Companies
10. Genres

Afterwards before any modeling or training takes place we encode the features in the following way:

1. Budget, Runtime, and Cast size, and Crew Size are encoded as real numbers
2. Production Countries, Spoken Languages, Release Month, and Directors are encoded categorically
3. Cast, Genres, and Production Companies are one-hot-encoded since they have multiple entries inside

Bar charts and histograms for the genres, cast and directors, profit, are shown below.

![Genres](Genres_Bar.png)

![Month](Month_Bar.png)

![Directors](Top_30_Directors.png)

![Histogram](Directors_Histogram.png)

![CastHisto](Cast_Histogram2.png)

![ProfitHisto](Profit_Histogram.png)

We did notice a large portion of the cast members having very few movies, so we culled the cast members with less than 20 movies, and similarly with production companies with less than 10 movies as they would not be useful for training or testing with very little entries. At this point we filled any NaN entries with the average value for runtime, and one hot encoded the cast, genres, and production companies. Finally, looking at the profit output, we can see that about 70% of the movies are profitable, while 30% are not profitable, which results in an unbalanced dataset if addressed with a binary classifier.

The correlation matrix of continuous features is shown below. The most correlated variables are Budget, Vote Counts, and Revenue. Unfortunately, revenue and vote counts come after the movie has been released so it isn't a good feature for making a profitable movie.

![CorrMatrix](Correlation_Matrix.png)

### **Methods:** 

#### **Supervised Learning: **:

##### **Naive Bayes:**
For supervised learning the first model we implemented, to see if learning is possible, was Naive Bayes (NB). NB has the benefit of relatively easily handling mixed input features. By combining the probabilistic output of a Bernoulli NB [^6] for one hot encoded features, Categorical NB [^7] for categorical features, and Gaussian NB [^5] for continuous features, it is possible to fit a model with these three feature types. The models are initially trained with the selected features and the likelihood from each is then combined as in the equation below.



![Equation](equation.png)

These are extracted using the predict_proba() function in each sk-learn NB implementation. Next steps would include trying further models such as ensemble learning, deep learning, Random Forest, and other methods. This could include applying regression instead of binary classification on the profit data to investigate if regression is more successful than binary classification for predicting profitability.


##### **Random Forest:**

The next supervised learning method we used was Random Forest [^11], a method typically used in classification. Random Forest is an ensemble approach that essentially uses a large number of individual decision trees to determine classification. In the case of our implementation, the Random Forest’s classification will use the features to predict a movie as either profitable or not.  

To implement Random Forest, we used the overall dataset (after preprocessing), and broke it up into an X and y dataset. The X dataset consisted of all the preprocessed data, except the features titled ‘revenue’, ‘profit’, ‘title’, and ‘id’. Revenue and profit were dropped, because these are essentially our target. Title and ID were removed, because they are strings that are irrelevant to generating decision trees. The y dataset consisted of simply our target variable, ‘profit’. From here, we further broke this data down into training (80%) and test (20%) sets. Finally, we used this data to fit our Random Forest Classifier. 

##### **Regression Methods:**

For regression, we used the ensemble learning method gradient boosting [^12] and multiperceptron neural networks [^13]. We expect regression to perform poorly due to the lack of correlation between usable features and profit. 

#### **Unsupervised Learning:** 

###### **Gower Distance:**
Unsupervised learning will be utilized to look for patterns in the movies utilizing clustering,  Gower distance[^4] was  used to deal with both the categorical and numerical data present. 

###### **Multidimensional Scaling:**
For unsupervised learning we used metric multidimensional spacing (MDS) [^14] which models similarity based on a distance metric. Metric MDS provides a lower dimensional view of the dataset, usually with two dimensions and works by double centering the input matrix and then performing singular value decomposition. For the distance metric, we used the gower distance which measures how different data points are.


### **Results and Discussion:**


#### **Supervised Learning: **

##### **Naive Bayes and Logistic Regression:**
Simply fitting Naive Bayes with all the features with no feature selection results can be seen in the table below. The training set was 90% of the data and the test set was 10%. Logistic Regression with an L2 regularizer was also ran on the training and test data for comparison. Precision and Recall are computed using a "weighted" average to account for the unbalanced dataset.

Type | Accuracy [^8] | Precision [^10] | Recall [^9]
--- | --- | --- | ---
Train | 0.769 | 0.769 | 0.769
Test | 0.644 | 0.644 | 0.634
Test only Categorical | 0.711 | | 
Test only Bernoulli | 0.672 | | 
Test only Gaussian | 0.591 | | 
"1" | 0.6988 | 1.0 | 0.6988 
Logistic Regression Train | 0.761 | 0.8519 | 0.761
Logistic Regression Test | 0.736 | 0.8706 | 0.736

Feature selection was done with forward feature selection on the individual models (Categorical, Bernoulli, and Gaussian) to identify the best parameters for each model. The comparison after feature selection is shown below.

Type (Post FS) | Accuracy [^8] | Precision [^10] | Recall [^9]
--- | --- | --- | ---
Train | 0.7395 | 0.854 | 0.7395
Test | 0.7138 | 0.8295 | 0.7137
Test only Categorical | 0.704 | | 
Test only Bernoulli | 0.707 | | 
Test only Gaussian | 0.698 | | 
"1" | 0.6988 | 1.0 | 0.6988 

While these results do show that learning is possible with a binary classifier, it also shows that perhaps Naive Bayes is not the best model for the data as it performs worse than logistic regression even after feature selection.

The important parameters for the types of data found by forward feature selection were:
1. Continuous - "budget" and "runtime"
2. Categorical - "production_country"
3. Bernoulli - "BBC Films", "Canal+", "Castle Rock Entertainment", "Epsilon Motion Pictures", "France 2 Cinema", "Malpaso Productions", "Millenium Films", "Morgan Creek Productions", "Regency Enterprises", "Studio Babelsberg", "Action", "Arnold Schwarzenegger", "Dustin Hoffman", "Tom Cruise", "Willem Dafoe"

When implementing other models for the final project, evaluation will be done similarly for the binary case, and mean squared error will likely be used for the regression cases.

##### **Random Forest:**
Our implementation of Random Forest yielded the following data:  

Type | Accuracy [^8] | Recall [^10] | Precision [^9]
--- | --- | --- | ---
Train | 1.0 | 1.0 | 1.0
Test | 0.72186 | 0.73959 | 0.92647

As we can see, the forest is clearly overfitting, however, the overfit forest still performs better than some of the other forms of learning that we explored.  

From here, to find the features that contribute most to this forest classification of a movie as profitable, we can examine the features that contribute most to this Random Forest Classifier: 

These features are shown here: 

![RandomForestResults](randomforestresults.png)

Thus, Random Forest Classifier suggests that the most important features in predicting a movie’s profitability are as ranked below: 

1. Budget 
2. Cast Size 
3. Crew Size 
4. Director  
5. Runtime 
6. Release Month 
7. Production Countries 
8. Spoken Languages 
9. Drama 
10. Original Language 

##### **Regression Methods:**
For predicting profit we found that gradient boosting was the best method. Gradient boosting reached an MSE[^15] of 16368 while Neural Networks had an MSE of 20579. The training and testing loss vs. training iterations for both models can be seen below. The gradient boosting is shown first, followed by the neural network. It is clear that the neural network model can fit the training data much better, but is more prone to overfitting as seen by the second plot. The gradient boosting model does not fit the training data as well, but generalizes much better to the testing data. Finally the important features were found using the Gini importance built into the sk-learn function as shown below.

The overfitting of the training model suggests that the no of features are too many and the model is complex for the data. The large mse values suggest that the features taken into consideration for predicting are not ideal and would suggest to search for other relevant ones.

![MSE_Grad](GradientBoostingMSE.png)

![MSE_NN](NeuralNetworkMSE.png)

![FI_Grad](GradientBoostingFeatureImportance.png)

#### **Unsupervised Learning:** 
We found that reducing the dimensions of the data to 2 and 3 don’t seem to visually result in better separability, though it does look like the profitable movies have a lower variance.

![UnsupervisedResults](2d_mds.png)
![UnsupervisedResults](3d_mds.png)


### **Conclusions:**

Predicting profitability of a movie without using revenue or ratings is a difficult problem. Using just features before the movie is released does not lead to great predictions. Summarizing the different model's findings, the most important features to have a profitable movie is the budget, cast size, whether it has a large production company, and whether you get Tom Cruise to act in your movie. The best model for classification of profitable movies was logistic regression, while the best model for predicting profit was gradient boosting.


### **Timeline:**

[Gantt Chart](GanttChart.zip)

![Gantt Chart](Gantt Chart_Test_image.png)

#### **Final Contribution Table:**

Walter | Ankith | Simon | Himnish | Jerome
--- | --- | --- | --- |---
 Naïve Bayes, Data Visualizations and Cleaning, Regression Visualizations, General Model Support, Presentation, Video, GitHub Page | Decision Tree Classifier and Visualizations, Presentation, Video, GitHub Page | Unsupervised Learning, Presentation, Video, GitHub Page | Regression Code, Presentation | Video, Github Page

#### **References:**

[^1]:	N. Darapaneni et al., "Movie Success Prediction Using ML," 2020 11th IEEE Annual Ubiquitous Computing, Electronics & Mobile Communication Conference (UEMCON), New York, NY, USA, 2020, pp. 0869-0874, doi: 10.1109/UEMCON51285.2020.9298145.
[^2]:	R. Dhir and A. Raj, "Movie Success Prediction using Machine Learning Algorithms and their Comparison," 2018 First International Conference on Secure Cyber Computing and Communication (ICSCCC), Jalandhar, India, 2018, pp. 385-390, doi: 10.1109/ICSCCC.2018.8703320.
[^3]:	R. Banik, 2017, “The Movies Dataset” Kaggles. [Online]. Available: https://www.kaggle.com/datasets/rounakbanik/the-movies-dataset
[^4]:	Gower, J. C. (1971). A General Coefficient of Similarity and Some of Its Properties. Biometrics, 27(4), 857–871. https://doi.org/10.2307/2528823
[^5]: scikit-learn. https://scikit-learn.org/stable/modules/generated/sklearn.naive_bayes.GaussianNB.html#sklearn.naive_bayes.GaussianNB
[^6]: scikit-learn. https://scikit-learn.org/stable/modules/generated/sklearn.naive_bayes.BernoulliNB.html#sklearn.naive_bayes.BernoulliNB
[^7]: scikit-learn. https://scikit-learn.org/stable/modules/generated/sklearn.naive_bayes.CategoricalNB.html#sklearn.naive_bayes.CategoricalNB
[^8]: scikit-learn. https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html#sklearn.metrics.accuracy_score
[^9]: scikit-learn. https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html#sklearn.metrics.recall_score
[^10]: scikit-learn. https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html#sklearn.metrics.precision_score
[^11]: scikit-learn. https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html
[^12]: scikit-learn. https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.GradientBoostingRegressor.html#sklearn.ensemble.GradientBoostingRegressor
[^13]: scikit-learn. https://scikit-learn.org/stable/modules/generated/sklearn.neural_network.MLPRegressor.html
[^14]: scikit-learn. https://scikit-learn.org/stable/modules/generated/sklearn.manifold.MDS.html#sklearn.manifold.MDS
[^15]: scikit-learn. https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html

 

 

 

 

 


 
