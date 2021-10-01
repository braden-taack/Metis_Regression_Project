Braden Taack
### Formula 1 Regression
#### October 1, 2021
---

#### Abstract
  
The goal of this project is to use exploratory data analysis and linear regression to build a model of Formula 1 race performance against pre-race features. The data was scraped from the [Formula 1 website](https://www.formula1.com/en/results.html/2021/races.html). After initial data cleaning, I used feature engineering and cross validation analysis to select the best model using R^2 as the main metric. My final model had 18 features and an R2= 0.684 and a MAE = 2.187.


#### Design

This project originates from my interest in Formula 1 as the top competitive racing league in the world. If you are a fan or have watched the Netflix series, *Drive To Survive*, then you know just how much attention and detail goes into each aspect of the race. I wanted to take a look at some basic race and practice performance data to gain some insights to the racing world. The aim was to build a model to help predict the final race result given practice, qualifying, and some in-race data. From this model I hoped to see which features would be the most impactful.  

#### Data

The data set was scraped from the [Formula 1 website](https://www.formula1.com/en/results.html/2021/races.html) using Selenium and BeautifulSoup. There are a total of 4188 unique driver performance summaries taken from 2006-2021. The data was restricted to this time frame to keep the race-weekend format consistent. Other years included differring amounts of practice runs, qualifying runs, etc. These past years would be a good area to explore given more time, particularly because the data goes back to 1950! There were many intial features with several duplicates due to complications with table merging on the website. While each table had new information, some of it tended to overlap. After removing duplicates, the intial features were: 'Race_result_Pos', 'Driver', 'Race_result_Car', 'Race_result_Laps', 'Fastest_laps_Pos', 'Fastest_laps_Lap', 'Fastest_laps_Time','Fastest_laps_Avg_Speed', 'Pit_stop_summary_Stops','Pit_stop_summary_Total', 'Starting_grid_Pos', 'Qualifying_Q1', 'Qualifying_Q2', 'Qualifying_Laps', 'Practice_3_Pos', 'Practice_3_Time','Practice_3_Laps', 'Practice_2_Pos', 'Practice_2_Time','Practice_2_Laps', 'Practice_1_Pos', 'Practice_1_Time','Practice_1_Laps', 'location', and 'year'. 

#### Algorithms

*Data Cleanup* 
  
Data cleanup was an interesting process mainly because of the time based data when it came to measuring lap times. All of the lap times were on the minute magnitude but were formatted as strings from the scraping process. These strings also had different formats: 1:00.000 vs 00.000. To overcome these discrepancies, the data was converted to a full date_time format, yyyy/mm/dd H:M:S.f. A funciton was then used to calcuate a time_delta between 1900/1/1 and total the number of seconds so that all of the lap times would be in convenient float format with seconds as the unit.  
  
Another issue was duplicate data for the pit_stop_summary columns. There were nx rows for n pit stops where only the pitstop lap was unique. I decided to eliminate that column and only keep the rows where the pit stop number was a maximum. Here I was able to have the total number of pitstops in the race and the total amount of time at the pitstop without duplicating the other feature data.  
  
Further issues came into play with the target variable, Race_Result_Pos, including strings like 'DQ' for disqualified. These were converted to null values. There were many null values in Qualifying_Q2 and Qualifying_Q3 as well. This was understood as not all drivers take extra qualifying times. If a value was missing in Q2, I filled it in with the data from Q1, assuming that the driver would have performed the same. If a value was missing in Q3, I filled it in with Q3, assuming the same. Lastly, I then dropped all nulls from the table to model with a clean data set.

*Feature Engineering*
1. Remove obvious duplicate columns, hour-of-the-day data, and gap data  
  - Obvious duplicate columns included the driver number, which is a unique number to each driver. A Driver column was already incorporated so these were not necessary.  
  - I was not interested in incorporating hour-of-the-day data because its likelyhood to have a useful impact on the model was questionable. In each race, there would be the same hour-of-the-day for each finish position.  
  - Gap data was not included either because it was already captured in the respective Time columns. 
2. Drop columns based on VIF (variance inflation factor)
  - 'Starting_grid_Time','Qualifying_Q3', and 'Qualifying_Pos' had huge VIF scores so were dropped. 
3. Use a LASSO model to identify more features to be removed. 
  - Despite some Time features with coeficients -> 0 from the LASSO model, all features were initially for alternate feature engineering.
4. Attempt to normalize all time data when grouped by year and track
  - The goal of this was to normalize the time data so it would more easily be compared from track to track.  
  - While this resulted in a similar model score, it was actually *worse* than the raw linear regression model.
5. Create dummy variables for Driver and Car
  - Using the base Linear Regression model from model selection, Driver and Car (essentially team) were independently assigned dummy variables. 
  - Using Driver dummy variables with an other category including drivers with less than 25 total races made the R^2 worse.
  - Using Car (aka team) dummy variables with an other category including cars with less than 50 total races also made the R^2 worse.
  - Neither categorical feature was included. 
7. Add new interaction features
  - 'P3_P2_delta', 'P2_P1_delta', 'Q2_Q1_delta' replaced the respective qualifying and practice lap times with the differences between laps. The goal of this was to judge based on improved performance from one practice to the next rather than just time. This marginally improved the R^2 so it was kept. 
  - 'SG_Pos_Q_Laps' multiplied the starting grid postion by the number of qualifying laps
  - 'FL_avg_p_laps' multiplied the the fastest lap average speed by the total number of practice laps
  - 'P3_Pos_P3_Laps', 'P2_Pos_P2_Laps', 'P1_Pos_P1_Laps' multiplied the number of practice laps by the practice position (ranking). These were not kept. 
  - The goal of each of these interaction terms was to explore the saying: "Practice makes perfect". Theoretically, with more practice, the better the driver would perform during the actual race. These interaction terms did prove useful and increased R^2.

*Model Selection*  
4 models were tested for this project using cross validation methods via skelearn: Linear Regression, Lasso, Ridge regression, and 2nd degree polynomial. For every cross validation test, the overall data set was initially broken up into 80% train and 20% test data. The training data was then used to cross validate using the kfolds method with 5 folds and scoring based on R^2.  
  
The results all had similar R2 values with the exception of the polynomial model which did worse. I selected the basic linear regression model based on the results in order to retain the features and maintain simplicity. 

#### Tools

- [Formula 1 website](https://www.formula1.com/en/results.html/2021/races.html) for data source using Selenium and BeautifulSoup
- Pandas for data ingestion and basic exploration
- Numpy and Pandas for data manipulation
- datetime for cleaning lap time data
- sklearn for model preprocessing, cross validation, model selection, and final model training and testing
- Matplotlib and Seaborn for plotting


