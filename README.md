# Crime Prediction Model for Charlotte, North Carolina (2017-2024)

### Introduction/Background
- According to the Brennan Center for Justice at NYU Law, predictive policing a method of “using algorithms to analyze massive amount of information in order to predict and help prevent potential future crimes”.
- The Los Angeles Police Department (LAPD) was the first agency to use predictive policing models when they partnered with federal agencies in 2008.
- The following states actively use predictive policing models: 
  - California 
  - Washington
  - South Carolina 
  - Arizona
  - Tennessee
  - Illinois
  - New York
Our models work to predict:
- Type of crime
- Location/Place Description of the crime

### Technologies Used 
- Postgre SQL: We created a connection to the PostgreSQL database to extract the data from
- Pandas: To manipulate data within dataframes
- zipfile: Library used to have Pandas read a zipped file. This is needed when the file size is too large for Jupyter Notebook to handle.
- scikit-learn: Used to preprocess the data, train the data, perform the Random Forest Classifier model, and display the confusion matrix and classification report
- Jupyter Notebook: Used for creating code, visualizations, and deploying machine learning model
- Tableau Public: Used to create the visualizations of the data
- City of Charlotte Data Portal: Houses data for CMPD and other city/county agencies

### Data Cleaning Methods
- Either used the zipfile reader library or connected to PostGreSQL to extract the data.
   - To install the zipfile library in Terminal
     ``` pip install zipfile36 ```
   - In Jupyter Notebook 
     ```
     import zipfile
     import Pandas as pd
     
     #Upload the zipfile that you want to read
     zf =  zipfile.ZipFile('<<add relative path to zip file here>>')

     #Have Pandas read the .csv file in the zip file
     df = pd.read_csv(zf.open('<<file_name.csv>>'))```
- Iterated through the DataFrame to locate the null values and drop them from the data set.
- Dropped unnecessary columns.
- Mapped subcategories in the “CLEARANCE STATUS” column.
- Created function to categorize crimes based on if classified as “violent” or “nonviolent”.


## Data Observations 
### 1) Total Count of Crimes based on NIBRS Categories 
- The National Incident Based Reporting System (NIBRS) is an incident-based reporting system used by law enforcement agencies in the United States for collecting and reporting data on crimes.  Color shows count of Year. The data is filtered on Year, which ranges from 2017 to 2024.
- [Click Here to View the Full Dashboard](https://public.tableau.com/views/TotalCountofCrimesBasedonNationalIncidentBasedReportingSystemNIBRSCategories/Dashboard4?:language=en-US&:sid=&:display_count=n&:origin=viz_share_link)

### 2) Violent vs. Nonviolent Crimes: Locations
- Comparing the number of violent vs. nonviolent crimes based on the type of location.
-  [Click Here to View the Full Dashboard](https://public.tableau.com/views/WIPProject4Visualizations/Dashboard3?:language=en-US&:sid=&:display_count=n&:origin=viz_share_link)

### 3) Monthly Trends for Violent and NonViolent Crimes
- Comparing the number of violent vs. nonviolent crimes per month in a given year. 
- [Click Here to View the Full Dashboard](https://public.tableau.com/views/TotalCountofCrimesCLT-NIBRSCategories/Dashboard5?:language=en-US&:sid=&:display_count=n&:origin=viz_share_link)

- ### 4) Yearly Trends for Top 10 Crimes for Charlotte, NC
- Observing the Yearly Trends for the Top 10 Crimes for Charlotte, NC from 2017-2024
- [Click Here to View the Full Dashboard](https://public.tableau.com/views/YearlyTrendsforTop10CrimesinCharlotteNC/Dashboard6?:language=en-US&:sid=&:display_count=n&:origin=viz_share_link)

## Pre-Optimized Model 

### K Nearest Neighbors Model
- Optimizations Applied
  - Dropped unnecessary columns to reduce noise and dimensionality in the dataset.
  - Used  *LabelEncoder*  to encode the target variable.
  - Used *train_test_split* to split the dataset into training and testing sets.
  - Standardized numerical features using *StandardScaler*.
  - Trained the KNeighborsClassifier model with *n_neighbors=7*.
  - Experimented with different values for the n_neighbors parameter in the KNeighborsClassifier model. Sometimes, a different number of neighbors can lead to better performance.

### Pre-Optimized Random Forest Model 
- Created a connection to PostgreSQL to extract the data
- Encode the variables using LabelEncoder and pd.get_dummies
- Separate the features (X) and the target variable (y)
  ```
  y = crime_data_df['clearance_status']
  x = crime_data_df[['year', 'zip', 'division_id', 'npa', 'date_reported', 'place_detail_description, 'highest_nibrs_code']]

  ```
- Use sklearn, split the dataset, instantiate a StandardScalar instance, fit the training data, and transform the data
- Create a Random Forest Classifier, fit the model, make predictions from the data
- Calculate the confusion matrix and accuracy score
- Calculate feature importances from the Random Forest Model
- Visualize the feature importances

**Results**
- Only one feature was predicted, year.
- This could be due to the imbalance classes, data issues, and the variability of the content within the features. E.g. ‘zip’, ‘date_reported’

### Optimization Methods
- Separate the ETL flow from the model notebook to improve memory.
- Encode the data using OneHotEncoding and LabelEncoder and allow it to handle missing values.
- Decrease the dimensions by:
  - Reduce the ‘clearance_status’ dimensionality by dropping unfounded clearance_status crimes and Use .loc to map all statuses to 'Cleared' except for 'Open'.
  - Dropping rows with values that are not necessary for the data i.e. dropping rows where there are less than 100 instances of ‘npa’ in the dataframe
  - Limit the categorical variable ‘date_reported’ to date/month values (just this reduced the dimensionality dramatically)
  - Remove values where ‘highest_nibrs_code’ was in the 800 category since these values correspond to non-criminal incidents.
  - Iterate through the dataframe to find nulls if any

**WITHOUT SIGNIFICANT DATA LOSS, DIMENSIONALITY WAS REDUCED BY ~79%**

## Optimized Model Results 

### Classification Report
- 68% of the instances predicted as 0 are actually 0. 53% of the actual class 1 instances are correctly identified.
- 83% of the instances predicted as 1 are actually 1. 90% of the actual class 1 instances are correctly identified.
- f1-score = harmonic mean of precision
- Accuracy = 80% of the instances were correctly predicted by the model

**Overall, our model is 80% accurate and the weighted f-1 score is 0.79. This indicated a good balance between precision and recall across the dataset**

## Conclusions
- Thefts from a Motor Vehicle account for 49,901 offenses from 2017 and 2024
- Nonviolent crimes account for 87% of the total crimes committed in Charlotte, NC from 2017 to 2024.
- April 2020 saw the largest dip in nonviolent and violent crimes (likely attributed to COVID-19 lockdowns).
- Most nonviolent and violent crimes occur in residential areas.
- Motor vehicle theft saw a significant increase in 2023 compared to prior years with 4,559 offense.

## Limitations and Implications

### Limitations 
- Bias with mapping NPAs (Neighborhood Profile Area)
- Racial Bias (leads to discrimination and over-policing in areas/neighborhoods with more people of color)
- Non-reporting bias (lack of accuracy)
- Information is not specific enough (3 ‘catch-all’ categories)
- Too much ambiguous data (high chance that your model will make mistakes)
- Human-error in police reporting (misspellings, etc)

### Implications 
- Law enforcement agencies can allocate resources to areas at higher risk of crime(NPA)
- Agency officials can make better-informed decisions by using machine learning models that analyze large volumes of data.
- By predicting crime hotspots, police can take preventative measures to potentially reduce crime rates.
- With this data, we could potentially analyze the crimes solved rate between states who use predictive policing and those who do not.








    

