# Python API Homework Walkthrough

In class we have just finished going over working with API's and Python. In this guide we are going to walk through each step of the homework. Our goal at the end of this is to have a working app that gathers weather data from the OpenWeatherMap API.

If we take a look at the starter code from the homework, we can already see that the code is returning a list of 616 cities for us. We can see this at the end of block ```[2]``` where we see ```len(cities)```. The first block where we are supposed to start coding is ```[3]``` so lets start there.

### Initial Setup
We know we need to perform a weather check on each of the cities and we already have a list of cities so we need to gather the weather data from the API. The first thing we need to do to be able to use the API is to set up our API url. If you remember from class we have a base url that we need to add our API key to. Your API key is going to be different from everyone else's API key which is why we have to do it this way. It should look something like below.

```python
url = "http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID=" + api_key
```

As you can see we have our base url and we are appending our API key on to the end. Next, we are going to need somewhere to store the data after we retreive it. Since our data is not going to be overly complex, lets create a blank list.

```python
city_data = []
```

Since we are processing so many records, it will be very hard to keep track of them without some sort of counter. For example, reading ```Processing record 1 of set 1``` is a lot easier to read than a wall of data. So lets create those counters.

```python
record_count = 1
set_count = 1
```

### Looping Through Our Cities
Now that we have a way to store and keep track of our data, lets start looping over our cities. First, we are going to need to set up our for loop with enumerate. If you do not know what enumerate is, don't worry it is here to help and is very simple. Enumerate allows us to to loop over something (in our case a our ```cities``` list) and have an automatic counter.

```python
for i, city in enumerate(cities):
```

Next, we want to use our counter variables we created to keep track of our processing. We are going to keep the cities in sets of 50. To do this we are going to write an if statement that checks if ```i``` is divisible by 50 and if so then it will reset the record count and add 1 to our ```set_count```. The ```i >= 50``` part is just to be sure we have looped at least 50 times because we do not want to add to our ```set_count``` if we have not finished a set.

```python
if (i % 50 == 0 and i >= 50):
        set_count += 1
        record_count = 0
```

Next, we need to finish our base API url from earlier by adding a query for our city. 

```python
city_url = url + "&q=" + city
```

Lets now format a print statement using the variables we created earlier so we can see what we are processing and increment our record counter.

```python
print("Processing Record %s of Set %s | %s" % (record_count, set_count, city))
record_count += 1
```

Our output should look something like this:
```
Processing Record 1 of Set 1 | severo-kurilsk
Processing Record 2 of Set 1 | darhan
Processing Record 3 of Set 1 | tarakan
Processing Record 4 of Set 1 | komsomolskiy
Processing Record 5 of Set 1 | souillac
```
### Running the API Request
Now we are going to use everything we have just set up to make an API call, return it as JSON, parse out the data we need, and append that data to our ```city_data``` list. You may or may not remember try catch blocks, but we need to use one here. This is because the API might not be able to find some cities and we do not want it to error out if that happens.

```python
try:
        # Parse the JSON and retrieve data
        city_weather = requests.get(city_url).json()

        # Parse out the max temp, humidity, and cloudiness
        city_lat = city_weather["coord"]["lat"]
        city_lng = city_weather["coord"]["lon"]
        city_max_temp = city_weather["main"]["temp_max"]
        city_humidity = city_weather["main"]["humidity"]
        city_clouds = city_weather["clouds"]["all"]
        city_wind = city_weather["wind"]["speed"]
        city_country = city_weather["sys"]["country"]
        city_date = city_weather["dt"]

        # Append the City information into city_data list
        city_data.append({"City": city, 
                          "Lat": city_lat, 
                          "Lng": city_lng, 
                          "Max Temp": city_max_temp,
                          "Humidity": city_humidity,
                          "Cloudiness": city_clouds,
                          "Wind Speed": city_wind,
                          "Country": city_country,
                          "Date": city_date})
```

Don't forget to handle our exceptions in case the city is not found!

```python
except:
        print("City not found. Skipping...")
        pass
```

### Convert Our Newly Populated List to a DataFrame
Now that we have just filled our ```city_data``` list with 600+ cities and all of their weather data, we need to convert it to a DataFrame to be able to visualize it. So lets first create our dataframe and fill it with our ```city_data``` list.

```python
city_data_pd = pd.DataFrame(city_data)
```

We do not need every piece of data that the API gave us so lets just grab what we need.

```python
lats = city_data_pd["Lat"]
max_temps = city_data_pd["Max Temp"]
humidity = city_data_pd["Humidity"]
cloudiness = city_data_pd["Cloudiness"]
wind_speed = city_data_pd["Wind Speed"]
```

Now lets create our output file and print out our records to make sure we are still on the right track. 

```python
# Export the City_Data into a csv
city_data_pd.to_csv(output_data_file, index_label="City_ID")
# Show Record Count
city_data_pd.count()
```

You can open the output file in excel or a similar application, but our count statement should look like this:

```
City          535
Cloudiness    535
Country       535
Date          535
Humidity      535
Lat           535
Lng           535
Max Temp      535
Wind Speed    535
dtype: int64
```

### Using Matplotlib to Show Our Data
The hardest part is behind us and now we get to visualize all of that data we gathered and cleaned! There are a few parameters we need to give Matplotlib to be able to create the graph, but it is nothing we haven't already done in class.

```python
# Build scatter plot for latitude vs. temperature
plt.scatter(lats, 
            max_temps,
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Cities")

plt.title("City Latitude vs. Max Temperature (%s)" % time.strftime("%x"))
plt.ylabel("Max Temperature (F)")
plt.xlabel("Latitude")
plt.grid(True)

# Save the figure
plt.savefig("output_data/Fig1.png")

# Show plot
plt.show()
```

[If you want to have another look at the Matplotlib ```scatter``` parameters, check out the documentation](https://matplotlib.org/api/_as_gen/matplotlib.pyplot.scatter.html)

# Finish
Don't forget to add, commit, and push all of your changes to your git repo!
You have now completed the homework! Hooray!


