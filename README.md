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


## Tools Used
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


## About the Dataset
### Data Sources
There are two datasets off of Kaggle, used in this project. The 'NYC Restaurant Inspections' data was collected by the NYC Department of Health, covers all of NYC, and spans from Jan 1, 2010 to Aug 29, 2017. The other dataset used is the 'NYC Precovid Restaurant Data'. Between these two datasets, we manage to obtain several variables of interest, such as: borough, income level (of the area), cuisine decription, grade, score, inspection date and type, violation code and description(s). 

- [NYC Restaurant Inspection](https://www.kaggle.com/datasets/new-york-city/nyc-inspections?select=DOHMH_New_York_City_Restaurant_Inspection_Results.csv)
- [NYC Precovid Restaurant Data](https://www.kaggle.com/datasets/nathaliekelley/nyc-precovid-restaurant-data?select=Pre_restaurants.csv)

### Data Cleaning and Data Joining
Using the Pandas library in Python, the two CSV files were read in, and any columns which were unnecessary dropped. Next, null values were dropped and the cleaned data exported into two CSV files. Using SQLAlchemy, the two cleaned datasets were then connected to a database in postgreSQL. The architecture within the database is outlined below:
* An 'inspection' table (containing data from the 'NYC Restaurant Inspection' dataset) with the following columns: 'DBA', 'BOROUGH', 'STREET', 'ZIPCODE', 'CUISINE_DESCRIPTION', 'SCORE', and "GRADE'. 
* An 'income' table (containing data from the 'NYC-Precovid Restaurant' dataset) with the following columns: 'DBA', 'STREET', and 'INCOME_LEVEL'.  

The two data tables were then joined via an 'inner join' on the 'DBA' and 'STREET' columns, as presented in the ERD diagram below:

![ERD](https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/SQL_database/SQL_images/ERD_NYC_restaurants_final.png)

### The Final Dataset
The 'inner join' on the two datasets resulted in a final dataset containing the following columns: 'DBA', 'STREET', 'INCOME_LEVEL', 'BOROUGH', 'ZIPCODE', 'CUISINE_DESCRIPTION', 'SCORE', and 'GRADE'.

Any duplicate rows present were removed, and the final dataset exported into a CSV file [FINAL_NYC_restaurants_full_database](https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/Resources/FINAL_NYC_restaurants_full_database.csv).


## Machine Learning Model
### Preprocessing the Data
The dataset has been preprocessed, split, and has been used to train and test [supervised machine learning models](https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_2.ipynb).

#### About the Data:
The data contains categorical and numerical variables. 
 - Categorical variable(s): ‘DBA’, ‘STREET’, 'INCOME_LEVEL’, ‘BOROUGH’, ‘CUISINE_DESCRIPTION’, and ‘GRADE’. ‘ZIPCODE’ was converted into a ‘string’ data type, and hence became a categorical variable. 
 - Numerical variable(s): ‘SCORE’. 
 
The ‘GRADE’ variable consists of ‘A’, ‘B’, ‘C’, ‘N’, ‘P’, and ‘Z’ values. ‘P’ signifies ‘pending’ but 'N' and 'Z' are not explained anywhere in the data source. 'N' could also mean 'Null' and keeping rows with this value will adversely affect our model. Since we are unsure about the meaning of 'N' and 'Z', we decided to drop the rows with values 'N' and 'Z' along with 'P'.

#### Feature Engineering, Selection, and Model Tweaking:
The target variable can either be 'SCORE' or 'GRADE'. 'SCORE' signifies the number of points deducted from the total obtainable marks, if the restaurant is found lacking in any of the measures that add up to a health grade. This means 'SCORE' and 'GRADE' have an inverse relationship with one another. However, they both signify the same thing - a health grading. The higher the 'SCORE' (since it is a deduction the total obtainable marks), the lower the health 'GRADE'. Since either 'SCORE' or 'GRADE' can suffice as a target variable, we chose 'GRADE' to be the target variable.

The features variables can include: ‘DBA’, ‘STREET’, INCOME_LEVEL’, ‘BOROUGH’, ‘CUISINE_DESCRIPTION’, and ‘ZIPCODE’. We experimented with these features variables and shortlisted the features variables down to only the ones that do not have a non-significant effect on our model. Different model designs were tried out by keeping 'GRADE' as the target variable but including a different set of features variables each time (all experiment models can be found in the [ML_experiments](https://github.com/SohaT7/NYC_Restaurant_Analysis/tree/main/ML_model/ML_experiments) folder):
 - [Model 0](https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_0.ipynb): 'INCOME_LEVEL’, ‘ZIPCODE’, 'BOROUGH’, ‘CUISINE_DESCRIPTION’, 'DBA', and 'STREET'.
 - [Model 1](https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_1.ipynb): 'SCORE'
 - [Model 2](https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_2.ipynb): 'INCOME_LEVEL’ and ‘ZIPCODE’
 - [Model 3](https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_3.ipynb): 'INCOME_LEVEL’, ‘ZIPCODE’ and ‘BOROUGH’
 - [Model 4](https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_4.ipynb): 'INCOME_LEVEL’, ‘ZIPCODE’ and ‘CUISINE_DESCRIPTION’
 - [Model 5](https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_5.ipynb): 'INCOME_LEVEL’, ‘ZIPCODE’, 'BOROUGH’ and ‘CUISINE_DESCRIPTION’

The [model](https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/ML_model/ML_experiments/ML_model_NormalizedStandardized_2.ipynb) design we chose had 'GRADE' as the target variable, and 'INCOME_LEVEL’ and ‘ZIPCODE’ as the features variables. ‘ZIPCODE’ signifies the area while the 'INCOME_LEVEL’ signifies the income level of that area. 'STREET' and 'BOROUGH' were dropped for they are too specific or too broad a categorization (and hvae negligible effect on the target variable). 'DBA' (Doing Business As) denotes the restaurant or restaurant chain name, and doe snot have a considerable effect on the outcomes. Similarly for the variable 'CUISINE_DESCRIPTION’, it has a negligible effect on what health 'GRADE' a restaurant receives.

#### Density Plots and Binning:
Density plots were drawn up for the categorical variables: ‘DBA’, ‘STREET’, ‘ZIPCODE’, and ‘CUISINE_DESCRIPTION’. This is because these variables contain rare (uncommon) unique values to the extent that if left as is, these unique values will make the dataset 'too wide' to work with. Each density plot was then read to to identify where the value counts ‘fall off’ and the threshold thus set in that particular region. The thresholds selected for the variables were as follows: 5 for ‘DBA’, 30 for ‘STREET’, 200 for ‘ZIPCODE’, and 250 for ‘CUISINE_DESCRIPTION’. The rare values for each of these variable were then bucketed into the ‘other’ category. As an example, the rare unique values and density plot for the variable ‘CUISINE_DESCRITPION’ are shown below.

<img width="200" alt="rare unique values" src="https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/ML_model/Images/CUISINE_DESC_rare_values.png"> <img width="450" alt="density plot" src="https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/ML_model/Images/CUSINE_DESC_uneven_distribution.png">

#### Encoding the Categorical Variables:
The encoding process included encoding the ‘GRADE’ variable into a ‘high’ and ‘low’ grade: grade ‘high’ comprised grades ‘A’ and ‘B’, whereas grade ‘low’ comprised grade ‘C’. This was followed by running a OneHotEncoder on all the categorical variables in our data.

#### Splitting the Preprocessed Data:
The data was then split into a training and testing set. 75% of the data went into the training set, and the remaining 25% of the data into the testing set. The training set trained (or 'fitted') the model, while the testing set was used to evaluate the model. The testing set helps evaluates the model by providing actual outcome values to compare the predicted outcome values against. The farther apart the two are, the weaker the model and its predictive ability is. 

#### Standardizing the Numerical Variables:
After the data is split into the training and testing sets, the numerical values in the data are standardized. The numerical values are standardized after the data is split, and not before, so as to not include the testing values into the scale. Next, supervised machine learning models were run on the data. 

### Compiling, Training, and Evaluating the Model
#### Compiling and Training the Model:
The different supervised machine learning models run on the data included resampling techniques and ensemble learning models as well:
 - Naive Random Oversampling - oversampling technique
 - SMOTE Oversampling - oversampling technique
 - Cluster Centroids Undersampling - undersampling technique
 - SMOTEENN - combines over- and under-sampling techniques
 - Random Forest Classifier - ensemble learning model
 - Balanced Random Forest Classifier - ensemble learning model
 - Easy Ensemble AdaBoost Classifier - ensemble learning model

Naive Random Oversampling and SMOTE Oversampling ‘oversample’ the minority class so the data values are on par with the majority class. The resample gives us 5598 ‘high’ and 5598 ‘low’ grades to run the ML model on. We experimented with Cluster Centroids Undersampling as well, which ‘undersamples’ the majority class down to equal the number of values in the minority class: this gives us 159 ‘high’ and 159 ‘low’ grades to run the ML model on. The SMOTEENN resampling method combines over and under sampling techniques by ‘oversampling’ the minority class to equate the number of data values in the majority class, followed by ‘undersampling’ via eliminating the data values that happen to fall in the neighborhood of both classes. This technique gave us 1780 ‘high’ and 773 ‘low’ grades to run the ML model on. The ensemble learning models used on the data include Random Forest Classifier, Balanced Random Forest Classifier, and Easy Ensemble AdaBoost Classifier, each utilizing 5598 'high' and 159 'low' grades.

The plot below shows the accuracy scores obtained by running the different machine learning models.

<img width="700" alt="ML models compared" src="https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/ML_model/Images/ML_Accuracy.png">

The best supervised machine learning model proves to be the Random Forest Classifier, which has an accuracy score of 0.97. 

![Classification Report for Random Forest Classifier](https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/ML_model/Images/confusion_matrix.png)

#### Evaluating the Model:
Our Random Forest Classifier model had an accuracy score of 0.97. A Random Forest Classifier involves training each weak learner on a subset of the data and then bases its result on the consensus reached by these weak learners together. A Random Forest Classifier model can miss out on the variability in the data. However, if the model’s number of estimators and the depth is sufficient, it should perform quite well. The confusion matrix for this model can be seen below:

![Confusion matrix for Random Forest Classifier](https://github.com/SohaT7/NYC_Restaurant_Analysis/blob/main/ML_model/Images/CM.png)

The model's precision to predict 'high' grades is 0.97, whereas its precision to predict 'low' grades is 0.00. The model's recall (or sensitivity) for 'high' grades is 1.00, whereas recall for 'low' grades is 0.00.

#### Recommendations:
The model has weak predictive ability: apparently it can predict 'high' grades well but is never able to predict 'low' grades. This means there is something along the way that is causing an issue. In order to improve this model, we can try binning 'GRADE' another way: instead of placing 'A' and 'B' in 'high' grade and 'C' only in 'low' grade, we can bin 'A' into 'high' grade and 'B' and 'C' into 'low' grade. Yet another way to refine our results is to use 'SCORE' instead of 'GRADE' and make multiple classes out of the target variable of 'SCORE', and then run our models against that target variable. However, a note of caution, following these steps will not yield better results. This is because the problem lies not with the model but with the dataset itself. Our dataset had very few variables and even fewer that were having any considerable effect on the target variable. What we need is a better dataset with a larger number of features variables that have an effect on the target variable. Examples of variables tha tmight help predict the health 'GRADE' a restaurant receives can include: frequency of garbage disposal, frequency of garbage collection in the area, quality of water in the restaurant and the area, availability (and quality) of refrigeration of food, frequency of load shedding in the restaurant and area, expertise level of cooking staff, description of hygiene protocol at the restaurant, whether the raw food is kept seperate from the cooked food, quality of restaurants in the neighborhoood, whether drains are covered or not, flooring, location (and cleanliness) of toilets in the restaurant, any presence of insects or rodents, etc. These variables will have a much likelier chance at being able to predict the health 'GRADE' a restaurant receives based on the income level of the area it is situated in.


## Visualization
An [interactive dashboard](https://veronicaywl.github.io/dashboard/) was created where the user can filter the NYC restaurant data on the following multiple criteria simultaneously: zipcode, income level, grade, borough and cuisine description. 

Some plots were generated in order to showcase the relationships between zipcodes, cusine types, and health scores, such as:
 - A heatmap was drawn up to show the average scores received by different cuisine-type serving restaurants in NYC's different boroughs.
 - A geomap to showcase the  grades received by the restaurants in the five boroughs.
 - A bar chart, bubble chart, and gauge chart to display the top 10 cusines and the average score for particular zipcodes.

### Dashboard link
[These slides](https://docs.google.com/presentation/d/1pMOhrs9x0gpjzY27lDiT6kX4VvVU2djwglDEqmNtXrU/edit#slide=id.g11c86ef9d0d_0_0) provide details about the tools and elements used to create the dashboard.

The NYC Restaurant Analysis web page (which contains the dashboard) can be visited [here](https://veronicaywl.github.io/dashboard/).

### Tableau Analysis 
The [Dashboard made in Tableau](https://public.tableau.com/views/NYC_Restaurant_Analysis/Story1?:language=en-US&:embed=y&:embed_code_version=3&:loadOrderID=0&:display_count=y&:origin=viz_share_link) shows:
- The majority of high income neighborhoods are located in the borough of Manhattan. Moreover, some of the 'unique' cuisines, such as Afghan, Ethiopian, and Filipino cuisines, are only located in Manhattan. This could be because these 'unique' cuisines especially a better market in high-income neighborhoods in Manhattan only.
- The heat map by cuisine types shows that African cuisine in Brooklyn and Eastern cuisine in Manhattan have the lowest health inspection grades among all neighborhoods.
- The zipcode 10461 has the highest score (i.e. points deducted) and the lowest health inpsection grade among all zipcodes. This is a low-income area in Bronx. The zipcode 10119 has the lowest score (i.e. points deducted) and the highest health inpsection grade among all zipcodes. This is a high-income area in Manhattan. This suggests our hypothesis might be true (our hypothesis stated that restaurants in high-income areas receive a higher health inspection grade). However, these are just two datapoints. To be sure either way, we ran a machine learning model (discussed in the [Machine Learning Model](#Machine-Learning-Model) section above).


## Summary 
The [NYC Restaurant Analysis](https://veronicaywl.github.io/dashboard/) aims to explore the relationships between different areas in NYC, income levels of those areas, cuisine types offered by restaurants there, and the health inspection grades received by restaurants in those areas. Data visualization in the form of a dashboard helps the individual filter the NYC Restaurant data on multiple criteria simultaneously, the criteria being: zipcode, income level, grade, borough and cuisine description. Different plots generated reveal average scores by zipcodes, top 10 cuisines in a zipcode, borough with the most high-income neighborhoods, and more. The machine learning model can predict the restaurants receiving a high health inspection grade well, but not those receiving a low health inspection grade. A model will perform only as good as the dataset used to traina nd test it. The present dataset had very few features variables of interest which affected how the model performed. For the model to work well, a good dataset with a variety of features variables is required. Some examples of such features variables include those that measure a restaurant's level of cleanliness and hygiene, availability of utilities and adequate food storage facilities, frequency of garbage disposal in the area, infrastructure of the restaurant, and the likes.


### Reference Resources
[Letter Grading for Restaurant](https://www1.nyc.gov/site/doh/business/food-operators/letter-grading-for-restaurants.page) ; 
[Inspection Cycle Overview](https://www1.nyc.gov/assets/doh/downloads/pdf/rii/inspection-cycle-overview.pdf) ; 

### Team Members
* Jessica Berrios | [GitHub](https://github.com/jwhberrios) | [LinkedIn](https://www.linkedin.com/in/jwhberrios/)

* Joon Su Choi | [GitHub](https://github.com/Joon-Su-Choi) | [LinkedIn](https://www.linkedin.com/in/joon-su-choi-498b87227/)

* Nishat Sultana | [GitHub](https://github.com/NishatSultana3538) | [LinkedIn](https://www.linkedin.com/in/nishat78/)

* Soha Tariq | [GitHub](https://github.com/SohaT7) | [LinkedIn](https://www.linkedin.com/in/soha-tariq-5226143b/)

* Yawen Veronica Liang | [GitHub](https://github.com/Veronicaywl) | [LinkedIn](https://www.linkedin.com/in/yawen-liang/)

