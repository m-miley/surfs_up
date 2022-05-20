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

Next, we look at min, max, and average temperatures for the most active station 'USC00519281'.

![Screen Shot 2022-05-20 at 1 10 45 PM](https://user-images.githubusercontent.com/100544761/169588090-a4bf3eb2-25bc-4f91-a85a-f92a32fe125c.png)

With a little more granularity, a histogram will provide us with more insight about frequency of reported temps.

    # Design a query to retrieve the last 12 months of precipitation data and plot the results. 
    #Starting from the last data point in the database. 
    prev_year = dt.date(2017,8,23)

    # Calculate the date one year from the last date in data set.
    prev_year = dt.date(2017,8,23) - dt.timedelta(days=365)

    # Perform a query to retrieve the data and precipitation scores
    results = list() #variable to store query
    results = session.query(Measurement.date, Measurement.prcp).filter(Measurement.date >= prev_year)

    # Save the query results as a Pandas DataFrame and set the index to the date column
    df = pd.DataFrame(results, columns=['date','precipitation'])
    df.set_index(df['date'], inplace=True)

    # Sort the dataframe by date
    df = df.sort_index()

    # Use Pandas Plotting with Matplotlib to plot the data
    df.plot()
    plt.ylabel("Rainfall (inches)")
    plt.xlabel("Date")
    plt.title("Total Precipitation by Day")
    plt.xticks(rotation=90)

![Screen Shot 2022-05-20 at 1 40 41 PM](https://user-images.githubusercontent.com/100544761/169592160-74e980ab-6c01-429c-ad45-71cbe74fb04a.png)


## Flask App

Using Flask, with multiple routes available, an API calls and displays results of analysis.

**Welcome Page**

![Screen Shot 2022-05-20 at 12 29 56 PM](https://user-images.githubusercontent.com/100544761/169588678-bf080afd-6e59-4005-b96a-8ef1d28f9fdb.png)

**Precipitation Averages**

    @app.route("/api/v1.0/precipitation")

    def avg_daily_precipitation():
        prev_year = dt.date(2017, 8, 23) - dt.timedelta(days=365)
        precipitation = session.query(Measurement.date, func.round(func.avg(Measurement.prcp),2)).\
            filter(Measurement.date > prev_year).group_by(Measurement.date).all()
        precip = {date: prcp for date, prcp in precipitation}
        return jsonify(precip)

![Screen Shot 2022-05-20 at 12 45 28 PM](https://user-images.githubusercontent.com/100544761/169588818-2227594f-ba6f-4c6e-b07a-37e6d422bf50.png)

**Stations**
    
    @app.route("/api/v1.0/stations")

    def stations():
        # get all stations
        results = session.query(Station.station).all()
        # unravel results into one-dim array
        stations = list(np.ravel(results))
        # jsonify and return
        return jsonify(stations=stations)

![Screen Shot 2022-05-20 at 12 47 19 PM](https://user-images.githubusercontent.com/100544761/169588903-743a4f05-dfc6-4635-a8d7-5b509fead524.png)

**One Year Temperatures for Most Active Station**

    @app.route("/api/v1.0/tobs/USC00519281")

    def temp_monthly():
        prev_year = dt.date(2017,8,23) - dt.timedelta(days=365)
        results = session.query(Measurement.tobs).\
            filter(Measurement.station == 'USC00519281').\
            filter(Measurement.date >= prev_year).all()
        temps = list(np.ravel(results))
        return jsonify(temps=temps)

![Screen Shot 2022-05-20 at 1 22 10 PM](https://user-images.githubusercontent.com/100544761/169589652-ac58918f-3df2-419f-a6f9-56df566eafe2.png)

**Min, Max, Average for starting/ending date inputs**

    @app.route("/api/v1.0/temp/<start>")
    @app.route("/api/v1.0/temp/<start>/<end>")
    def stats(start=None, end=None):
        sel = [func.min(Measurement.tobs), func.avg(Measurement.tobs), func.max(Measurement.tobs)]
        if not end:
            results = session.query(*sel).\
                filter(Measurement.date >= start).all()
            temps = list(np.ravel(results))
            return jsonify(temps=temps)
        results = session.query(*sel).\
            filter(Measurement.date >= start).\
            filter(Measurement.date <= end).all()
        temps = list(np.ravel(results))
        return jsonify(temps)
 
![Screen Shot 2022-05-20 at 1 24 17 PM](https://user-images.githubusercontent.com/100544761/169589975-eb4d5f7d-0506-4db6-9bcf-f1fc84f11aba.png)


## Results

- Temperature
    - June and December have strikingly similar statistics which indicates that the temperature is fairly stable throughout the year without any major fluctuations as reflected in the mean, min, and max.  The results are favorable for investing.

![Screen Shot 2022-05-20 at 2 44 57 PM](https://user-images.githubusercontent.com/100544761/169600575-538df777-accd-4129-984a-a794e074068d.png)
![Screen Shot 2022-05-20 at 2 45 04 PM](https://user-images.githubusercontent.com/100544761/169600581-6a9186b1-e053-46e4-84c1-e1d6fbc1d030.png)

- Precipitation
    - Both June and December have records reporting rainfall for most days of the month although small, half of the days being under a tenth of an inch.  75 percent of the days have around one one hundredth of an inch which could mean cloud cover.  

![Screen Shot 2022-05-20 at 2 29 30 PM](https://user-images.githubusercontent.com/100544761/169598598-612cb563-594d-4479-9d41-5d220e089d74.png)
![Screen Shot 2022-05-20 at 2 29 49 PM](https://user-images.githubusercontent.com/100544761/169598631-03e95b43-1b2d-415f-99a4-b87dc6ad2e3d.png)

- These two major weather observations are in conflict with each other.  One suggests good temperature range and stability which means potential for higher activity in the area.  The other suggests maybe it's not so sunny all that often.  Perhaps there's a bit more cloud cover than surfers and ice cream connaisseurs would prefer.


## Conclusion

Depending on larger climate patterns throughout the day, I suggest carefully considering rainfall in regards to an investment decision.  If rain is later in the day, maybe it's not a big deal.  If it's cloudy and drizzling all day, maybe this location is not so good for a surf and icecream shop.  An analysis digging deeper into these questions is definitely possible.  It would require a different data set as this one is without time stamp.  With time stamps and multiple reportings per day, one could analyze hourly reports.  Also, I would recommend looking into some foot traffic data including tourism and nearby towns and distances.  This would give us an idea for how many customers we could reach and translate into sales.  Furthermore, from a surfing perspective, it's important to look at tide and swell and the type of terrain near the beach.  It would be helpful to collect data on how many surfers visit this area on a daily basis.  


### Contact

Email: mrmileyy@gmail.com