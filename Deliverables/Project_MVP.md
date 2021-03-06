Braden Taack
### Formula 1 Regression Project MVP
#### September 28th, 2021
___
#### *Note of Project Change*
Originally, I was going to work with AllTrails trail data in an attempt to model trail popularity. I had built a successful pipeline for the trail pages but unfortunately was blocked by reCaptcha after attempting to access too many pages sequentially. I was booted from their server each time I tried to gather data so in the face of time, I transitioned to my backup project on Monday: Formula 1 racing data. 

#### MVP

The goal of this project is to model and interpret Formula 1 racing data where race points are the target. The data has been sourced from [forumla1.com](https://www.formula1.com/en/results.html/2021) via web-scrapping with Selenium and BeautifulSoup. So far, data has been scrapped for races between 2017-2021.

Formula 1 is one of the most data intensive sports on the planet; there is a stat for everything. For the purposes of this project, I am focusing in on just the data records available via the website: race results, fastest laps, pit stop summary, starting grid, qualifying, and practices 1-3. To start the model exploration, I created a simple linear model by dropping all nulls and only using numerical features. Below is the heatmap for the correlations. There are several features that indicate a correlation with the target Race_results_PTS. On the contrary, there are also features that may depict collinearity. 

![](heatmap.png)

To test assumptions and the general performance of the model, I also generated diagnostic plots. Clearly seen in the residuals plot, there is some work to be done in the data cleaning and feature engineering. The Q-Q plot and predicted target charts show promise. 

![](diag_plots.png)

#### Further Work  
With many nulls present in the data due to no finishes, crashes, etc, more data will be collected. However, as the sport has evovled over time, so have the rules. To keep things relatively simple, only data newer than 2006 will be considered so the scrapping pipeline will not need modifying and more focus can be put on modeling. 

Because only the basic linear model with numerical features has been generated thus far, extensive data cleaning and feature engineering will be performed. Categorical features such as car type (Ferrari vs Mercedes) will also be included with dummy variables. Depending on the performance of the initial linear model, I may create a standardized dataframe to work with a LASSO or Ridge model. The LASSO model could prove useful for feature engineering as well. 
