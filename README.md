# NYC Restaurant Analysis

## Table of Contents
- [Google Slides Link](#google-slides-link)
- [Reason for Selected Topic](#reason-for-selected-topic) 
- [About the Dataset](#About-the-Dataset)
- [Database](#database)
- [Questions to Answer with the Data](#questions-to-answer-with-the-data)
- [Data Exploration](#data-exploration)
- [Entity Relationship Diagram (ERD)](#Entity-Relationship-Diagram-(ERD))
- [Machine learning model](#machine-learning-model)
    - [Preprocessing the Data](#Preprocessing-the-Data)
        - [About the data](#about-the-data)
        - [Feature Engineering, Selection, and Model Tweaking](#Feature-Engineering,-Selection,-and-Model-Tweaking:)
        - [Density Plots and Binning](#Density-Plots-and-Binning)
        - [Encoding the Categorical Variables](#Encoding-the-Categorical-Variables)
        - [Splitting the Preprocessed Data](#Splitting-the-Preprocessed-Data)
        - [Standardizing the Numerical Variables](#Standardizing-the-Numerical-Variables)
    - [Compiling, Training, and Evaluating the Model](#Compiling,-Training,-and-Evaluating-the-Model)
        - [Compiling and Training the Model](#Compiling-and-Training-the-Model)
        - [Evaluating the Model](#Evaluating-the-Model)
        - [Recommendations](#Recommendations)
- [Tools Used](#tools-used)
- [Dashboard link](#dashboard-link)
- [Tableau Analysis](#tableau-analysis)
- [Reference Resources](#reference-resources)
- [Summary](#Summary )
- [Team Members](#team-members)


## Google Slides Link
[Google Slides](https://docs.google.com/presentation/d/11oTL0XQdANk1oRAHP2maSfnjSAKa4D0l4QkrQKRJAZk/edit#slide=id.g129db8187be_0_64)

## Reason for Selected Topic 
In this project, we aim to find out if income levels in an area have an effect on the health inspection grades received by the restaurants located in that area. 

### The Hypothesis
Our hypothesis states that restaurants located in higher income areas receive a better health inspection grade. 

## About the Dataset
### Data Sources
There are two datasets off of Kaggle, used in this project. The 'NYC Restaurant Inspections' data was collected by the NYC Department of Health, covers all of NYC, and spans from Jan 1, 2010 to Aug 29, 2017. The other dataset used is the 'NYC Precovid Restaurant Data'. Between these two datasets, we manage to obtain several variables of interest, such as: borough, income level (of the area), cuisine decription, grade, score, inspection date and type, violation code and description(s). 

- [NYC Restaurant Inspection](https://www.kaggle.com/datasets/new-york-city/nyc-inspections?select=DOHMH_New_York_City_Restaurant_Inspection_Results.csv)
- [NYC Precovid Restaurant Data](https://www.kaggle.com/datasets/nathaliekelley/nyc-precovid-restaurant-data?select=Pre_restaurants.csv)

### Data Cleaning and Data Joining
Using the Pandas library in Python, the two CSV files were read in, and any columns which were unnecessary dropped. Next, null values were dropped and the cleaned data exported into two CSV files. Using SQLAlchemy, the two cleaned datasets were then connected to a database in postgreSQL. The architecture within the database is outlined below:
* 'NYC Restaurant Inspection' data had columns: 'DBA', 'BOROUGH', 'STREET', 'ZIPCODE', 'CUISINE_DESCRIPTION', 'SCORE', and "GRADE'. 
* 'NYC-Precovid Restaurant' data had columns: 'DBA', 'STREET', and 'INCOME_LEVEL'.  

The two data tables were then joined via an 'inner join' on the 'DBA' and 'STREET' columns (as presented in the ERD diagram below). 

* The joined dataset had the following columns: 'DBA', 'STREET', 'INCOME_LEVEL', 'BOROUGH', 'ZIPCODE', 'CUISINE_DESCRIPTION', 'SCORE', and 'GRADE'.

After the merge, the duplicate rows were removed, and the final dataset exported into a CSV file [FINAL_NYC_restaurants_full_database](https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/Resources/FINAL_NYC_restaurants_full_database.csv).

The Entity Relationship Diagram (ERD) is as follows:

![ERD](https://github.com/Veronicaywl/Final_Project/blob/main/SQL_database/SQL_images/ERD_NYC_restaurants_final.png)


## Machine Learning Model
### Preprocessing the Data
#### About the Data:
The dataset has been preprocessed, split, and has been used to train and test supervised machine learning models in [ML_model_NormalizedStandardized_0](https://github.com/Veronicaywl/Final_Project/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_0.ipynb).

The data has categorical and numerical variables. The categorical variables include ‘DBA’, ‘STREET’, 'INCOME_LEVEL’, ‘BOROUGH’, ‘CUISINE_DESCRIPTION’, and ‘GRADE’. ‘ZIPCODE’ was converted into a ‘string’ data type, and hence became a categorical variable too. ‘SCORE’ is a numerical variable. The ‘GRADE’ variable consists of ‘A’, ‘B’, ‘C’, ‘N’, ‘P’, and ‘Z’ unique values. However, since we do not know what ‘N’ and ‘Z’ signify and ‘P’ signifies ‘pending’, we have dropped ‘N’, ‘P’, and ‘Z’ from our dataset to prepare a more reliable model.

#### Feature Engineering, Selection, and Model Tweaking:
Initially we experimented with ‘DBA’, ‘STREET’, INCOME_LEVEL’, ‘BOROUGH’, ‘CUISINE_DESCRIPTION’, ‘ZIPCODE’, and ‘SCORE’ as features variables, and ‘GRADE’ as our target variable. However, we later experimented with various combinations of features variables to determine which ones are most significant when it comes to our model. We are trying to build a model that can predict whether or not a restaurant will get a ‘high’ grade given our features variables. 

We removed 'SCORE' from our features variables since 'SCORE' is akin to 'GRADE'. 'GRADE' is created by gradations made over 'SCORE' values, which basically denotes the number of points you lose off your restaurant. 'SCORE' and 'GRADE' then are inversely related. In fact, 'SCORE' could work as a target variable too. The experiments we ran (can be found here in [ML_experiments](https://github.com/Veronicaywl/Final_Project/tree/main/ML_model/ML_experiments) folder) were: 

. [Model 2](https://github.com/Veronicaywl/Final_Project/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_2.ipynb) : 'INCOME_LEVEL’ and ‘ZIPCODE’ against ‘GRADE’; 

. [Model 3](https://github.com/Veronicaywl/Final_Project/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_3.ipynb) : 'INCOME_LEVEL’, ‘ZIPCODE’ and ‘BOROUGH’ against ‘GRADE’;

. [Model 4](https://github.com/Veronicaywl/Final_Project/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_4.ipynb) : 'INCOME_LEVEL’, ‘ZIPCODE’ and ‘CUISINE_DESCRIPTION’ against ‘GRADE’;

. [Model 5](https://github.com/Veronicaywl/Final_Project/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_5.ipynb) : 'INCOME_LEVEL’, ‘ZIPCODE’, 'BOROUGH’ and ‘CUISINE_DESCRIPTION’ against ‘GRADE’;

#### Density Plots and Binning:
The preliminary data preprocessing included normalization of the categorical variables ‘DBA’, ‘STREET’, ‘ZIPCODE’, and ‘CUISINE_DESCRIPTION’. These specific categorical variables were picked out from the rest since these have rare (or uncommon) unique values enough that if left as is, would make the dataset to wide to work with. A density plot was used for each of these variables, to identify where the value counts ‘fall off’ and the threshold thus set in that particular region. The thresholds selected for these variables are as follows: 5 for ‘DBA’, 30 for ‘STREET’, 200 for ‘ZIPCODE’, and 250 for ‘CUISINE_DESCRIPTION’. The rare values were bucketed into the ‘other’ category, to help normalize the uneven distribution. A categorical variable ‘CUISINE_DESCRITPION’s rare values and density plot prior to normalization is shown below:

!['CUISINE_DESCRIPTION' variable has rare unique values](https://github.com/Veronicaywl/Final_Project/blob/main/ML_model/Images/CUISINE_DESC_rare_values.png)

!['CUISINE_DESCRIPTION' variable has an uneven distribution prior to normalization](https://github.com/Veronicaywl/Final_Project/blob/main/ML_model/Images/CUSINE_DESC_uneven_distribution.png)

#### Encoding the Categorical Variables:
The encoding process included encoding the ‘GRADE’ variable into a ‘high’ and ‘low’ grade: grade ‘high’ comprised grades ‘A’ and ‘B’, whereas grade ‘low’ comprised grade ‘C’. This was followed by running a OneHotEncoder on all the categorical variables in our data.

As shown by the results from these model experiments too, it makes most sense to use 'INCOME_LEVEL’ and ‘ZIPCODE’ as our features variables. [ML_model_NormalizedStandardized_2](https://github.com/Veronicaywl/Final_Project/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_2.ipynb) contains our choice selection of features variables and target variable. 

Theoretically too, one can hypothesize how income level and zipcode may have an effect on the grade a restuarant receives. We choose 'ZIPCODE'' as a marker of geographical locality, and not 'STREET' or 'BOROUGH' since they are too specific or too general a markers - Zipcode captures just the right level of geographical distinctness for our purposes. 'DBA' (doing business as) denotes the restaurant or restaurant chain name, and we surmise that will not have any considerable, if any, effect on the outcomes. ‘CUISINE_DESCRIPTION’ is left out as well for we hypothesize that the type of cuisine may not have any effect on the grade that restaurant gets either.  

#### Splitting the Preprocessed Data:
The data was then split into training and testing data (75% training and 25% testing data). About 75% of the data (i.e. the training data) was used for training (or ‘fitting’) the models, and the remaining 25% data (i.e. the testing data) was used for testing each model. 

#### Standardizing the Numerical Variables:
After the data split, if there were any numerical variables in the data, they were standardized. We standardize after we split the data, and not before, because we do not want to include the testing values into the scale. 
The data we are working with is tabular and not raw (i.e. has no natural language data or images therein), so supervised machine learning models run well on it. 

### Compiling, Training, and Evaluating the Model
#### Compiling and Training the Model:
The different supervised machine learning models tried on our data include resampling and ensemble learning models. The resampling models used on the data include: Naive Random Oversampling, SMOTE Oversampling, Cluster Centroids Undersampling, and SMOTEENN which combines over- and under-sampling techniques. 

Naive Random Oversampling and SMOTE Oversampling ‘oversample’ the minority class so the data values are on par with the majority class. The resample gives us 5598 ‘high’ and 5598 ‘low’ grades to run the ML model on. We experimented with Cluster Centroids Undersampling as well, which ‘undersamples’ the majority class down to equal the number of values in the minority class: this gives us 159 ‘high’ and 159 ‘low’ grades to run the ML model on. The SMOTEENN resampling method combines over and under sampling techniques by ‘oversampling’ the minority class to equate the number of data values in the majority class, followed by ‘undersampling’ by eliminating the data values that happen to fall in the neighborhood of both classes. This technique gave us 1780 ‘high’ and 773 ‘low’ grades to run the ML model on. The ensemble learning models used on the data include Random Forest Classifier, Balanced Random Forest Classifier, and Easy Ensemble AdaBoost Classifier, each utilizing 5598 'high' and 159 'low'.


From among these models, the best model turns out to be the Random Forest Classifier, which has an accuracy score of 0.97. 

![Classification Report for Random Forest Classifier](https://github.com/Veronicaywl/Final_Project/blob/main/ML_model/Images/confusion_matrix.png)

Our model chosen is the same as last week's - Random Forest Classifier - which yields the best results. However, there is some change in the features variables used in this model: we re-selected our features variables down to only two: 'INCOME_LEVEL’ and ‘ZIPCODE’. The target variable remains same as before: 'GRADE'.
Initially we were working with ‘DBA’, ‘STREET’, 'INCOME_LEVEL’, ‘BOROUGH’, ‘CUISINE_DESCRIPTION’, ‘ZIPCODE’, and ‘SCORE’ as features variables, and ‘GRADE’ as our target variable. The re-selection of features variables is explained in detail above in the section titled "Feature engineering, selection, and model tweaking".
[ML_model_NormalizedStandardized_2](https://github.com/Veronicaywl/Final_Project/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_2.ipynb) contains our model from this week. 

#### Evaluating the Model:

Our best model is Random Forest Classifier with an accuracy score of  0.97. A Random Forest Classifier involves training each weak learner on a subset of the data and then bases its result on the consensus reached by these weak learners together. A Random Forest Classifier model can, however, miss out the variability in the data. However, if the model’s number of estimators and  the depth is sufficient, it should perform quite well. The confusion matrix for this model can be seen below:

![Confusion matrix for Random Forest Classifier](https://github.com/Veronicaywl/Final_Project/blob/main/ML_model/Images/confusion_matrix.png)

The model's precision to predict 'high' grades is 0.97, whereas its precision to predict 'low' grades is 0.00.
The model's recall (or sensitivity) for 'high' grades is 1.00, whereas recall for 'low' grades is 0.00.

#### Recommendations:
In order to improve this model, we can try binning 'GRADE' another way: instead of placing 'A' and 'B' in 'high' grade and 'C' only in 'low' grade, we can bin 'A' into 'high' grade and 'B' and 'C' into 'low' grade. Yet another way to refine our results is to use 'SCORE' instead of 'GRADE' and make multiple classes out of the target variable of 'SCORE', and then run our models against that target variable. 

## Visualization
Plot some useful maps based on income level in the areas and showing the grades that spreading amoung in the local area. 
- Creating a heatmap showing the average scores by cuisine in different borough.
- Plot a Geo map to indicate the grades that restaurants received in five borough.
- An interactive dashboard using javascript , html, css , bootstrap where we can filter New York city restaurant  data by zip code, income level, grade, borough and cuisine, separately and also in different combination. 
- Use plotly and javascript to make interactive graph to visualize mean grading score by cuisine in different zip code.
- Using plotly we visualize different ML model to see which model give us the better accuracy score.


## Tools Used:
#### Creating Database
- PostgreSQL

#### Connecting to Database
- SQLAlchemy

#### Analyzing Data
- Python (Pandas library)

#### Machine Learning
- Python (Pandas, Scikit-learn, and Imbalanced-learn libraries)


#### Dashboard
- Tableau
- Javascript
- Plotly
- HTML
- CSS
- Bootstrap


### Dashboard link:
The [Dashboard](https://docs.google.com/presentation/d/1pMOhrs9x0gpjzY27lDiT6kX4VvVU2djwglDEqmNtXrU/edit#slide=id.g11c86ef9d0d_0_0)
 provides detail about the tools and elements that we used to create the dashboad.

This is the webpage that we build after data analysit [NYC Restaurant Analysis Dashboard](https://veronicaywl.github.io/dashboard/).


### Tableau Analysis: 
- Based on the [Dashboard](https://public.tableau.com/app/profile/yawen.liang/viz/NYC_Restaurant_Analysis/Dashboard1#1) we could see that majority of high income neighborhoods are located in the borough of Manhattan. And some of the minority cuisine such as Afghan, Ethiopian and Filipino cuisines are only located in Manhattan. 

- We could also tell from the [Heat Map](https://public.tableau.com/app/profile/yawen.liang/viz/GeoMapbasedonincome/HeatMapbyCuisine?publish=yes) that the African cuisine located in Brooklyn and the Eastern cuisine located in Manhattan has the lowest inspection grade among the neighborhood. All analysis are including in the [Story](https://public.tableau.com/app/profile/yawen.liang/viz/NYC_Restaurant_Analysis/Story1#2). We also found out that the zip code 10119 has the lowest points among all zip codes in five boroughs. In which means the zip code has the highest inspection grade in high income level neighborhood located in Manhattan. The zip code 10461 has the lowest inspection grade in low income level neighborhood located in the Bronx. 


## Summary 

![ML_Accuracy](https://github.com/Veronicaywl/Final_Project/blob/Nishat_2/ML_model/Images/ML_Accuracy.png)

Accuracy score was obtained and visualize to compared which Machine Learning Model performs best with our data. Random Forest Classifier was the best model to predict grade from a specific zip code and income level.


### Reference Resources
[Letter Grading for Restaurant](https://www1.nyc.gov/site/doh/business/food-operators/letter-grading-for-restaurants.page) ; 
[Inspection Cycle Overview](https://www1.nyc.gov/assets/doh/downloads/pdf/rii/inspection-cycle-overview.pdf) ; 

### Team Members

* Jessica Berrios | [GitHub](https://github.com/jwhberrios) | [LinkedIn](https://www.linkedin.com/in/jwhberrios/)

* Joon Su Choi | [GitHub](https://github.com/Joon-Su-Choi) | [LinkedIn](https://www.linkedin.com/in/joon-su-choi-498b87227/)

* Nishat Sultana | [GitHub](https://github.com/NishatSultana3538) | [LinkedIn](https://www.linkedin.com/in/nishat78/)

* Soha Tariq | [GitHub](https://github.com/SohaT7) | [LinkedIn](https://www.linkedin.com/in/soha-tariq-5226143b/)

* Yawen Veronica Liang | [GitHub](https://github.com/Veronicaywl) | [LinkedIn](https://www.linkedin.com/in/yawen-liang/)

