___
Braden Taack
###  AllTrails Linear Regression Project Proposal
#### September 22th, 2021
___
  

#### Question/need:
* What is the framing question of your analysis, or the purpose of the model/system you plan to build?   
  
  The service AllTrails helps many outdoors-people find, record, and even map their favorite trails. AllTrails already has built in services to point out their picks for top trails, but what makes those trails number 1? I aim to gather data on trails from various parks in California from the AllTrails website in order to build a model to predict trail rating (0-5 stars, non-discrete) based on trail characteristics. 

* Who benefits from exploring this question or building this model/system?  
  
  Hikers, bikers, runners, and active people all around can seek to benefit from my model. With all of the options of outside activities, even on AllTrails itself, the selection process can be difficult. The model may narrow down the most important trail characteristics to focus on. Ideally, the model could be useful to predict the rating for new trails or lesser known trails based on certain characteristics. 

#### Data Description:
* What dataset(s) do you plan to use, and how will you obtain the data?  
  
  I plan to generate my own dataset based on the trail information from the [AllTrails Website](https://www.alltrails.com/). The data will be obtained via the web scrapping packages BeautifulSoup and Selenium. Selenium will be used to navigate to the individual trail pages and BeautifulSoup will be used to parse the page for the data. 

  The dataset will aim to include 1497 different trails from the following parks in California:
  Park Name | Number of Trails
  --------- | ----------------
  Tahoe National Forest | 224
  Yosemite National Park | 275
  Seqouia National Park | 105
  Joshua Tree National Park | 122
  Mount Tomalpais State Park | 74
  Kings Canyon National Park | 68
  Lasser Volcanic National Park | 64
  Pinnacles National Park | 31
  Angeles National Forest | 277
  Topanga State Park | 55
  Death Valley National Park | 98
  Redwood National Park | 25
  Mount Diablo State Park | 79
  
* What is an individual sample/unit of analysis in this project? What characteristics/features do you expect to work with?  
  
  An individual sample will be a single trail. The y value will correspond to the overall trail rating from 0 to 5 stars. The following will be the initial features of the table:  
  * Length  
  * Elevation Gain  
  * Route Type
  * Difficulty (Easy, Medium, Hard)
  * #of Reviews
  * #of Completions
  * #5 star ratings
  * #4 star ratings
  * #3 star ratings
  * #2 star ratings
  * #1 star ratings
  * Location
  * #of photos
  * Trail Name (Not used for modeling. needed for grouping, plotting, etc.)
  * #of waypoints (number of notable things to see on hike)
  
* If modeling, what will you predict as your target? 

  I predict that this model will run into extrema bias. I think it is possible that there may be more casual hikers who prefer shorter, easier trails and thus rate those favorably. On the other end, there will be hardcore people who prefer the longest, most-difficult trails. I suspect there may be a lull in moderate expertise so the model may end up battling favoritism between the most and least challenging trails. Whichever personality is more likely to leave a review will be the one that the model will favor for a high predicted rating.   
  
#### Tools:
* How do you intend to meet the tools requirement of the project?  
  
  I plan to create my dataset utilizing both Selenium and BeautifulSoup to scrape data from AllTrails. Pandas will be used to store the data in a dataframe. Scikit-Learn and numpy will be used to analyze, fit, and model the data. Matplotlib and Seaborn will be used for initial graphical discovery of the data and to visualize results. 

#### MVP Goal:
* What would a [minimum viable product (MVP)](./mvp.md) look like for this project?  
  
  The goal for the MVP will be to have the data scrapped from the website, successfully parsed, and stored in a pandas dataframe. The data may also be locally stored as a CSV to have a local backup. The data will have also been successfully built into a model including all of the features using Scikit-Learn, even though some multicollinearity is expected. A basic model evaluation will be run on the initial model along with the common discovery charts such as pair plot from seaborn.   
#### Alternate Project Idea:

   In hoping for the best but preparing for the worst, I will alternatively work with [Formula 1 racing data](https://www.formula1.com/en/results.html/2020/races.html). The reason for this inclusion is due to potential issues with the AllTrails website including but not limited to:
  * [Stricter website access](https://www.alltrails.com/robots.txt)
  * Login Requirements
  * Large number of page visits required
  * More difficult parsing techniques required

  The Formula 1 information would be much easier to [access without restriction](https://www.formula1.com/robots.txt) and easier to parse with its general table formatting. Selenium would still be necessary to sort through the different kinds of tables and years (Races, Drivers, Teams, Fastest Lap for 1950-2021). BeautifulSoup would still be needed to sort through different table formats to pull unique information from each. The ultimate goal would be to model correlations to driver points from various features such as lap times, starting grid position, practice 1,2,3 times, fastest lap, track, etc. 
