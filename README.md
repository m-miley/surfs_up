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

Although there are varying counts of station reports per day, we can look at the average per day.  It's important we don't consider the sum of precipitatioin due to station count fluctuations.

![Screen Shot 2022-05-20 at 12 23 38 PM](https://user-images.githubusercontent.com/100544761/169587873-68e2d8fd-5fea-4596-a0ff-687641405f99.png)

Next, we look at average temperatures for the most active station 'USC00519281'.

![Screen Shot 2022-05-20 at 1 10 45 PM](https://user-images.githubusercontent.com/100544761/169588090-a4bf3eb2-25bc-4f91-a85a-f92a32fe125c.png)

With a little more granularity, a histogram will provide us with more insight as to frequency of reported temps.

![Screen Shot 2022-05-20 at 1 12 12 PM](https://user-images.githubusercontent.com/100544761/169588257-929f57f7-73a3-4057-bcac-0b98f666ed78.png)


## Flask

Using Flask, with multiple routes available, we created an active API for reviewing and retrieving data results.

**Welcome Page**

![Screen Shot 2022-05-20 at 12 29 56 PM](https://user-images.githubusercontent.com/100544761/169588678-bf080afd-6e59-4005-b96a-8ef1d28f9fdb.png)

**Precipitation Averages**

# define second route, precipitation
    @app.route("/api/v1.0/precipitation")

    def avg_daily_precipitation():
        prev_year = dt.date(2017, 8, 23) - dt.timedelta(days=365)
        precipitation = session.query(Measurement.date, func.round(func.avg(Measurement.prcp),2)).\
            filter(Measurement.date > prev_year).group_by(Measurement.date).all()
        precip = {date: prcp for date, prcp in precipitation}
        return jsonify(precip)

![Screen Shot 2022-05-20 at 12 45 28 PM](https://user-images.githubusercontent.com/100544761/169588818-2227594f-ba6f-4c6e-b07a-37e6d422bf50.png)

**Stations**
![Screen Shot 2022-05-20 at 12 46 06 PM](https://user-images.githubusercontent.com/100544761/169588951-d1daa1d8-128a-4c97-bac8-9b267ed61d65.png)

![Screen Shot 2022-05-20 at 12 47 19 PM](https://user-images.githubusercontent.com/100544761/169588903-743a4f05-dfc6-4635-a8d7-5b509fead524.png)

**One Year Temperatures for Most Active Station**
![Screen Shot 2022-05-20 at 12 46 18 PM](https://user-images.githubusercontent.com/100544761/169589466-8f25f5b0-0ff0-4921-a632-2a9e3249f594.png)

![Screen Shot 2022-05-20 at 1 22 10 PM](https://user-images.githubusercontent.com/100544761/169589652-ac58918f-3df2-419f-a6f9-56df566eafe2.png)

**Min, Max, Avg for starting/ending dates**

Replace start/end with dates range
![Screen Shot 2022-05-20 at 1 22 57 PM](https://user-images.githubusercontent.com/100544761/169589811-c674a8e5-6dfc-45a9-8075-f4916a7f6ac1.png)
 
![Screen Shot 2022-05-20 at 1 24 17 PM](https://user-images.githubusercontent.com/100544761/169589975-eb4d5f7d-0506-4db6-9bcf-f1fc84f11aba.png)


## Results