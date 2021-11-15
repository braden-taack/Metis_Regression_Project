Braden Taack
### Formula 1 Regression
#### October 1, 2021
---

#### Abstract
  
The goal of this project is to use exploratory data analysis and linear regression to build a model of Formula 1 race performance against pre-race features. The data was scraped from the [Formula 1 website](https://www.formula1.com/en/results.html/2021/races.html). After initial data cleaning, I used feature engineering and cross validation analysis to select the best model using R^2 as the main metric. My final model had 18 features and an R2= 0.684 and a MAE = 2.187.


#### Design

This project originates from my interest in Formula 1 as the top competitive racing league in the world. If you are a fan or have watched the Netflix series, *Drive To Survive*, then you know just how much attention and detail goes into each aspect of the race. I wanted to take a look at some basic race and practice performance data to gain some insights to the racing world. The aim was to build a model to help predict the final race result given practice, qualifying, and some in-race data. From this model I hoped to see which features would be the most impactful.  

#### Data

The data set was scraped from the [Formula 1 website](https://www.formula1.com/en/results.html/2021/races.html) using Selenium and BeautifulSoup. There are a total of 4188 unique driver performance summaries taken from 2006-2021. The data was restricted to this time frame to keep the race-weekend format consistent. Other years included differing amounts of practice runs, qualifying runs, etc. These past years would be a good area to explore given more time, particularly because the data goes back to 1950! There were many initial features with several duplicates due to complications with table merging on the website. While each table had new information, some of it tended to overlap. After removing duplicates, the initial features were: 'Driver', 'Race_result_Car', 'Race_result_Laps', 'Fastest_laps_Pos', 'Fastest_laps_Lap', 'Fastest_laps_Time','Fastest_laps_Avg_Speed', 'Pit_stop_summary_Stops','Pit_stop_summary_Total', 'Starting_grid_Pos', 'Qualifying_Q1', 'Qualifying_Q2', 'Qualifying_Laps', 'Practice_3_Pos', 'Practice_3_Time','Practice_3_Laps', 'Practice_2_Pos', 'Practice_2_Time','Practice_2_Laps', 'Practice_1_Pos', 'Practice_1_Time','Practice_1_Laps', 'location', and 'year'. 

#### Algorithms
  
*Web Scraping*  
  
Web scraping the formula 1 site presented a unique challenge due to the large amount of interaction required with Selenium. The website separated the data for a single race in 8 different click-required tabs: 'Race_result','Fastest_laps','Pit_stop_summary','Starting_grid','Qualifying','Practice_3','Practice_2','Practice_1'.   So to get the data for one race, you have to click on the year, click on the race, and then click on each of the 8 tabs and parse through the html for each of the tabs. To better automate this process, I created several functions for clicking, parsing data, merging tables, and stacking dataframes.   
  1. Human imposter function - when called, this would randomly select a wait time between 15 seconds and user input with a default of 60 seconds. This was to give enough time for the tables and lists to load (after much trial and error) and also randomize wait times to spoof human-like interaction.
  2. get_table_data - Utilizes BeautifulSoup to find the table on the page, gather the headers, zip each row as a value to its respective index in a dictionary, and then output the resulting dictionary as a pandas dataframe. 
  3. get_records_data - first checks to make sure the tabs match the standard set of tabs and will skip the race if the tabs don't match. This functionality was added in to avoid missing data, as it would not have been useful missing so many columns. The function then runs through a for loop, using Selenium to select each of the 8 record tabs 1 by 1 and using the get_table_data to gather the table information. A list of 8 dataframes is generated from this process and then all 8 df's are merged on column 'Driver' to create one collective dataframe for that specific race. The function also takes in the name of the race location and transforms it at the end of the dataframe. This feature was particularly useful to group the data by race during feature engineering. 
  4. get_loc_data - This function uses BeautifulSoup to gather the list of race locations for the selected year. Using Selenium again, the list is looped through to capture data for each race with the get_records_data function. The function would also scroll to the top of the page after collecting the data for the race. The locations would go out of range of clickability after get_records_data because it would be scrolling itself down to access additional tabs. The data frame for each race will be stored in a dictionary. The final output is one dataframe for all races in that year after concatenating the dictionary of race record data. The function also takes in the year and transforms it in a new column at the end of the dataframe. This was done for better groupby functionality. 
  5. Lastly, as you can see functions 2,3, and 4 essentially act as nested for loops. One last for loop is added separately to gather any number of years between 2006 and 2021 desired.

I ran the notebook for several years at a time, given the *long* processing time required due to the number of waits needed for the pages to load. Each of these dataframes were saved into their own csv and then stacked together to create a master df for all race data from 2006-2021. 

*Data Cleanup* 
  
Data cleanup was an interesting process mainly because of the time based data when it came to measuring lap times. All of the lap times were on the minute magnitude but were formatted as strings from the scraping process. These strings also had different formats: 1:00.000 vs 00.000. To overcome these discrepancies, the data was converted to a full date_time format, yyyy/mm/dd H:M:S.f. A function was then used to calculate a time_delta between 1900/1/1 and total the number of seconds so that all of the lap times would be in convenient float format with seconds as the unit.  
  
Another issue was duplicate data for the pit_stop_summary columns. There were nx rows for n pit stops where only the pitstop lap was unique. So even though the initial data frame had ~11,000 rows, there were only ~5000 unique driver performances. I decided to eliminate the pitstop lap column and only keep the rows where the pit stop number was a maximum. Here I was able to save the total number of pitstops in the race and the total amount of time spent in pit lane without duplicating the other features' data.  
  
Further issues came into play with the target variable, Race_Result_Pos, including strings like 'DQ' for disqualified. These were converted to null values. There were many null values in Qualifying_Q2 and Qualifying_Q3 as well. This was understood as not all drivers take extra qualifying times. If a value was missing in Q2, I filled it in with the data from Q1, assuming that the driver would have performed the same. If a value was missing in Q3, I filled it in with Q3, assuming the same. Lastly, I then dropped all nulls from the table to model with a clean data set.

*Feature Engineering*
1. Remove obvious duplicate columns, hour-of-the-day data, and gap data  
  - Obvious duplicate columns included the driver number, which is a unique number to each driver. A Driver column was already incorporated so these were not necessary.  
  - I was not interested in incorporating hour-of-the-day data because its likelihood to have a useful impact on the model was questionable. In each race, there would be the same hour-of-the-day for each finish position.  
  - Gap data was not included either because it was already captured in the respective Time columns. 
2. Drop columns based on VIF (variance inflation factor)
  - 'Starting_grid_Time','Qualifying_Q3', and 'Qualifying_Pos' had huge VIF scores so were dropped. 
3. Use a LASSO model to identify more features to be removed. 
  - Despite some Time features with coefficients -> 0 from the LASSO model, all features were initially for alternate feature engineering.
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
  - 'SG_Pos_Q_Laps' multiplied the starting grid position by the number of qualifying laps
  - 'FL_avg_p_laps' multiplied the the fastest lap average speed by the total number of practice laps
  - 'P3_Pos_P3_Laps', 'P2_Pos_P2_Laps', 'P1_Pos_P1_Laps' multiplied the number of practice laps by the practice position (ranking). These were not kept. 
  - The goal of each of these interaction terms was to explore the saying: "Practice makes perfect". Theoretically, with more practice, the better the driver would perform during the actual race. These interaction terms did prove useful and increased R^2.

*Model Selection*  
4 models were tested for this project using cross validation methods via sklearn: Linear Regression, Lasso, Ridge regression, and 2nd degree polynomial. For every cross validation test, the overall data set was initially broken up into 80% train and 20% test data. The training data was then used to cross validate using the kfolds method with 5 folds and scoring based on R^2.  
  
The results all had similar R2 values with the exception of the polynomial model which did worse. I selected the basic linear regression model based on the results in order to retain the features and maintain simplicity. 

#### Tools

- [Formula 1 website](https://www.formula1.com/en/results.html/2021/races.html) for data source using Selenium and BeautifulSoup
- Pandas for data ingestion and basic exploration
- Numpy and Pandas for data manipulation
- datetime for cleaning lap time data
- sklearn for model preprocessing, cross validation, model selection, and final model training and testing
- Matplotlib and Seaborn for plotting

#### Conclusions  
  
My final model consisted of a Linear Regression model with the following features and coefficients. The R^2 was 0.684 and the MAE was 2.187.
  
  Intercept|-9.764
  ---------|------  
  
  Feature|coef
  -------|----
  Fastest_laps_Pos| 0.308
  Fastest_laps_Lap| 0.003
  SG_Pos_Q_Laps|0.005
  FL_avg_p_laps|-0.0003
  Fastest_laps_Avg_Speed| 0.039
  Pit_stop_summary_Stops|0.74
  Pit_stop_summary_Total|-0.001
  Starting_grid_Pos|0.272
  Qualifying_Laps|0.058
  Practice_3_Pos|0.077
  Practice_3_Laps|0.075
  Practice_2_Pos|0.063
  Practice_2_Laps|0.083
  Practice_1_Pos|0.08
  Practice_1_Laps|0.069
  P3_P2_delta| 0.006
  P2_P1_delta|0.016
  Q2_Q1_delta|0.01
