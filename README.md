# surfs_up

**Resources**
    
    - .sqlite db file
    - SQLAlchemy
    - Flask
    - Python
    - Jupyter Notebook
    
## Overview
 
Analysis of weather (temperature and precipitation) at a location in Oahu to help an investor determine whether to invest in a surf shop.  The code is written with intent to repurpose for other locations if desired.  With a sqlite file as a database, this analysis imports the ORM functionality of SQLAlchemy for querying purposes and performs an analysis in Jupyter Notebook and displays results using Flask framework.

![Surfing-Sport-Shop](https://user-images.githubusercontent.com/100544761/169579363-b79c4501-ab1b-444d-a838-e47d7ce1500c.jpeg)

## Exploratory Analysis

At first glance, querying to pandas DF and plotting the precipitatioin by day, we notice there is a fair amount of precipitation.  

![Screen Shot 2022-05-20 at 12 21 47 PM](https://user-images.githubusercontent.com/100544761/169586920-7a7e8201-5136-4f44-9e8f-fc79ffc34904.png)

However, upon further investigation, we notice several reportings for each day.  

![Screen Shot 2022-05-20 at 12 22 59 PM](https://user-images.githubusercontent.com/100544761/169586982-623f609e-33a1-438f-97a7-5b3983922d81.png)

Furthermore, upon querying a count of distinct weather station ids, we note 9 existing stations, all actively reporting rainfall accumulation.  

![Screen Shot 2022-05-20 at 1 02 08 PM](https://user-images.githubusercontent.com/100544761/169587078-cd73b3f7-3754-4eba-8aeb-cca16279a203.png)

![Screen Shot 2022-05-20 at 1 05 32 PM](https://user-images.githubusercontent.com/100544761/169587310-4f8968c9-b6eb-49b9-9cdc-96038a9851de.png)





