# Overview

With a couple of non-Verstappen wins in the current Formula 1 season, I tried to have a bit of fun developing a model that can predict who would win the remainder of the races starting from the Austrian Grand Prix. To do so, I used race results from the current F1 ground effect era (starting 2022) from the 1950-2023 archive in the [official F1 website](https://www.formula1.com/en/results.html/2024/races/1239/austria/race-result.html). I also grabbed data from the races in the 2024 season so far just for good measure. The packages/libraries I used for the project are as follows:

- **requests**: To grab HTML content from the website.
- **BeautifulSoup**: To parse the obtained HTML document, basically scraping the webpage.
- **pandas**: To analyse race result tables
- **scikit-learn**: To obtain machine learning algorithms like RandomForestClassifier and GridSearchCV

# Procedure

- Firstly, I needed some data to work with. Thankfully the official F1 website had all the data I needed. To obtain said data I used Requests and BeautifulSoup to scrape the webpages for the 2022, 2023 and 2024 (so far) season.
- The objects of interest were the tables containing the race results (P1-P20/DNFs, Driver, Constructor, Laps etc.). After obtaining these tables, I concatenated them into a single data frame using pandas like so.<p align = "center">![image](https://github.com/rghanty/f1_rf_prediction/assets/99227180/3553437f-c587-4373-ac7f-b27e3c61ad11)</p>
- Then, I cleaned up the data removing columns with NaN and set about preparing the data for the model.
- The "Pos" column had values like DNF (Did Not Finish) and NC (Not classified) so I assigned them a value of 0 (Usually because race engineers tell the driver to go to P0 if they don't finish).
- Then I created columns "Driver_Code", "Car_Code" and "Grand_Prix_Code" which assigned unique numbers to the drivers, constructors and the grand prix venue respectively. The reason behind this is to assign quantitative labels to the parameters of interest for the model to understand.
- I split the data into training and testing sets where the training set consisted of the first 40 races and the testing set, the remainder of the races. The main rationale behind this split was to include some GP winners other than Max in the training set before the complete and utter dominance he showed in the 2023 season.
- I used the RandomForestClassifier to fit the data and generate predictions on the testing set. My predictors were the Car_Code, Grand_Prix_Code and Laps. Later I removed laps from consideration as most drivers had the same value (i.e., number of laps in the race).
-  I evaluated the accuracy and the result was a meagre 40%. At this point I felt the same way watching Max win a race by 30 seconds, related gif below:
<p align = "center"><img src = "https://media1.tenor.com/m/p53Nyf0vv8sAAAAd/homelander-cinema-homelander.gif" width="500"></p>

- After some ***intense*** Googling, I decided to add some more predictors that would help increase the accuracy. Enter "Pos_Car", "Pos_GP" and "Pos_Driver" which contained the product of the "Pos" column and the "Car_Code", "Grand_Prix_Code" and "Driver_Code" respectively. It matters where the drivers, in their respective cars finished at which grand prix to help predict the outcome of future grands prix.
- I also enlisted the help of the GridSearchCV algorithm to determine the best parameters that will be used as input for the RandomForestClassifier. I added the respective columns above to the existing list of predictors and fit the model.
- Now, the testing accuracy went up to a decent 74%. A bit more satisfied with this result. I used this model to train the outcome of future races. I generated a dummy CSV with random values of drivers and constructors and the model populated the file with its predicted drivers for each grand prix and the results are as follows.<p align="center">![image](https://github.com/rghanty/f1_rf_prediction/assets/99227180/d2b5b2b8-689f-4d41-95f5-2a734a41f032)</p>
- A quick note, this is simply a rough prediction based on how drivers/teams have been doing so far. The model does not account for weather changes or mechanical issues.
- Despite this, I am glad to see some variation in the race winners, but I feel the model is being a bit optimistic because we all know Max is gonna dominate the remaining races like he did last year.
  <p align = "center"><img src = "https://media1.tenor.com/m/mEI0LWZRrr0AAAAC/verstappen-max-verstappen.gif" size="1100"></p>
