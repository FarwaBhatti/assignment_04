
## A PROPOSAL FOR FORD GO BIKE COMPANY

Ford Go company is running Bay Area Bikeshare. 

Business Problem: 
The company would like to increase the ridership and using analysis and insights from the data the company has available, what deals can we offer to our customers/subscribers through the mobile app.


Current Business Model:


1) A flat price for a single one-way trip

2) A day pass that allows unlimited 30-minute rides for 24 hours

3) Annual membership


---

# ANALYSIS AND ACTIONABLE INSIGHTS

Bay Area BikeShare has two types of rides - 1) subscribers and 2) customers



Overall, in our dataset, we have more subscribers than customers. However, over the last few years, between the years 2015 and 2016, the number of subscribers and members have gone down but the subscribers have decreased at a higher rate than the members.



We wanted to look deeper, and analyze the behaviors beyond just the numbers. Although the number of trips for subscribers is higher than the customers, but very interestingly, the customers have a longer duration of their trips (minutes). We can leverage this number as an opportunity, because now we know that these customers are here with us on a shorter term. They are not repeat customers and they do not take many trips, but the trips they do end up taking are longer in duration. The question is how can we use this offset between the number of trips and the duration of trips to convert these short term customers into longer term members. 


We further broke down the data by time of the day and week of the day, and interestingly again the customers showed more activity on the weekends during afternoon and evening times.


According to a recent study done to study the memberships models of different companies, one key insight was that in terms of marketing dollars, it is 7 times more expensive to acquire new customers than to retain the customers. Therefore, our business strategy should focus on how to retain our existing audience and convert them to life-time members/subscribers. 

---

# RECOMMENDATIONS

SUBSCRIBERS

Our subscribers in numbers are decreasing and hence we are in need to change our business model or a product recommendation so that they are more incentivized

Reommendation for subscribers:  Package for commuters 

To lure in the early morning and evening time commuters, the Ford Go Data Science team suggests to introduce a package for the commuters. Since their number of trips during weekdays is high but the duration is low, we can introduce "bundle pricing model for weekday" which allows these subscribers to buy packages in advance at a specific price.

CUSTOMERS

Our customers are not as many as subscribers, or they may not be taking as many rides as our subscribers in volume but the duration of their trips in higher. Therefore our pricing model should incentivize their duration of teh trips especially on the weekends where their activity is the highest. To convert these customers into loyal members/subscribers -- introduce an unlimited minutes for the weekend at a specific price which gives them an annual membership as well for no charge. Since most of these customers come on weekends, it may be safe to assume they are tourists who are not local to SF. This way at an added value/price to the company, we can sell them more minutes and successfully convert them to members at the same time.

---

# ADDITIONAL QUESTIONS THAT CAN REFINE THE ANALYSIS

How many existing customers/subscribers do we have under each of the pricing model?

What are the business models / pricing strategies of our competitors?

Do we have data on member id? It will be interesting to see how many are new/acquired subscribers/customers vs returning subscribers/customers?

Currently, what is our revenue and the Cost of Acquisition per member

---

# SQL QUERIES


```python
#QUERY 1 -- HOW MANY SUBSCRIBERS VS CUSTOMERS DOES FORD GO COMPANY HAVE?

!bq query --use_legacy_sql=FALSE --format=csv 'SELECT count(subscriber_type) Total_per_subscriber, subscriber_type FROM `bigquery-public-data.san_francisco.bikeshare_trips` GROUP BY subscriber_type' > result_sub_customers.csv
```

    Waiting on bqjob_r7f33e0ed5092ba33_0000016b3409a4ac_1 ... (0s) Current status: DONE   


```python
#QUERY 2 -- FORD GO COMPANY HAS MORE SUBSCRIBERS THAN CUSTOMERS. THIS IS GREAT!
#CHECK HOW THE NUMBERS OF SUBSCRIBERS AND CUSTOMERS HAVE CHANGED OVER THE YEARS


!bq query --use_legacy_sql=FALSE --format=csv  'SELECT  extract(year from start_date) as Year,  count(subscriber_type) Total_per_subscriber, subscriber_type FROM `bigquery-public-data.san_francisco.bikeshare_trips` GROUP BY Year, subscriber_type Order by Year, subscriber_type' > result.csv
```

    Waiting on bqjob_r1c2307125bb330d9_0000016b3409b246_1 ... (0s) Current status: DONE   


```python
#QUERY 3 -- MAPPING THE CUSTOMERS AND SUBSCRIBERS COMMITMENT TO BIKE SHARING THAN JUST RAW NUMBERS IS IMPORTANT
#CHECK HOW THE NUMBER OF TRIPS TAKEN AND THE MINUTES OF TRIPS VARY BY CUSTOMER VS SUBSCRIBER

!bq query --use_legacy_sql=FALSE --format=csv  'SELECT extract(year from start_date) as Year, count(trip_id) as Number_of_trips, subscriber_type FROM `bigquery-public-data.san_francisco.bikeshare_trips` group by Year, subscriber_type Order by Year, subscriber_type' > result1.csv

!bq query --use_legacy_sql=FALSE --format=csv  'SELECT extract(year from start_date) as Year, subscriber_type, sum(timestamp_diff(end_date, start_date, minute)) as Minutes FROM `bigquery-public-data.san_francisco.bikeshare_trips` group by Year, subscriber_type order by Year, subscriber_type, Minutes' > result2.csv
```

    Waiting on bqjob_r77c4596d614f458a_0000016b3409c1ec_1 ... (0s) Current status: DONE   


```python
#QUERY 4 -- SPLITTING THE USAGE OF THE BIKE SHARE BY DAY OF THE WEEK FOR SUBSCRIBERS VS CUSTOMERS
#USING EXTRACT FUNCTION ON SQL WHICH MAPS 1 AS SUNDAY AND SO ON

!bq query --use_legacy_sql=FALSE --format=csv  'SELECT CASE WHEN extract(DAYOFWEEK from start_date)=1 THEN "Sunday" WHEN extract(DAYOFWEEK from start_date)=2 THEN "Monday" WHEN extract(DAYOFWEEK from start_date)=3 THEN "Tuesday" WHEN extract(DAYOFWEEK from start_date)=4 THEN "Wednesday" WHEN extract(DAYOFWEEK from start_date)=5 THEN "Thursday" WHEN extract(DAYOFWEEK from start_date)=6 THEN "Friday" WHEN extract(DAYOFWEEK from start_date)=7 THEN "Saturday" END as Day_of_the_week, subscriber_type, count(trip_id) as trip, sum(timestamp_diff(end_date, start_date, minute)) as Minutes FROM `bigquery-public-data.san_francisco.bikeshare_trips` group by Day_of_the_week, subscriber_type order by Day_of_the_week, subscriber_type' > result3.csv
```

    Waiting on bqjob_r35b218441426deea_0000016b3409d2fb_1 ... (0s) Current status: DONE   


```python
#QUERY 5 -- SPLITTING THE USAGE OF THE BIKE SHARE BY DAY OF THE WEEK AND TIME OF THE DAY FOR SUBSCRIBERS VS CUSTOMERS
#USING EXTRACT FUNCTION ON SQL WHICH MAPS 0 AS 12 AM AND SO ON
#CLASSIFIED THE TIME OF THE DAY INTO 1) EARLY MORNING 2) AFTERNOON 3) EVENING 4) NIGHT 5)LATE NIGHT

!bq query --use_legacy_sql=FALSE --format=csv  'SELECT CASE WHEN extract(DAYOFWEEK from start_date)=1 THEN "Sunday" WHEN extract(DAYOFWEEK from start_date)=2 THEN "Monday" WHEN extract(DAYOFWEEK from start_date)=3 THEN "Tuesday" WHEN extract(DAYOFWEEK from start_date)=4 THEN "Wednesday" WHEN extract(DAYOFWEEK from start_date)=5 THEN "Thusday" WHEN extract(DAYOFWEEK from start_date)=6 THEN "Friday" WHEN extract(DAYOFWEEK from start_date)=7 THEN "Saturday" END as Day_of_the_week, subscriber_type, CASE WHEN extract(HOUR from start_date) >= 0 and extract(HOUR from start_date) < 6 THEN "Late Night" WHEN extract(HOUR from start_date) >= 6 and extract(HOUR from start_date) < 11 THEN "Early Morning" WHEN extract(HOUR from start_date) >= 11 and extract(HOUR from start_date) < 15 THEN "Afternoon" WHEN extract(HOUR from start_date) >= 15 and extract(HOUR from start_date) < 20 THEN "Evening" WHEN extract(HOUR from start_date) >= 20 and  extract(HOUR from start_date) <= 23 THEN "Night" END as Time_of_day, count(trip_id) as trip, sum(timestamp_diff(end_date, start_date, minute)) as Minutes FROM `bigquery-public-data.san_francisco.bikeshare_trips` group by Day_of_the_week, Time_of_day, subscriber_type order by Day_of_the_week, Time_of_day, subscriber_type' > result_final.csv
```

    Waiting on bqjob_r59297358db96894b_0000016b3409de61_1 ... (0s) Current status: DONE   

---

# PYTHON

## PIVOT TABLES AND DATA FRAMES


```python
#IMPORT THE PACKAGES
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
```


```python
data_sub_customers = pd.read_csv('result_sub_customers.csv')
data_sub_customers
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total_per_subscriber</th>
      <th>subscriber_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>136809</td>
      <td>Customer</td>
    </tr>
    <tr>
      <th>1</th>
      <td>846839</td>
      <td>Subscriber</td>
    </tr>
  </tbody>
</table>
</div>




```python
data = pd.read_csv('result.csv')
data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Year</th>
      <th>Total_per_subscriber</th>
      <th>subscriber_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2013</td>
      <td>24499</td>
      <td>Customer</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2013</td>
      <td>76064</td>
      <td>Subscriber</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2014</td>
      <td>48576</td>
      <td>Customer</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2014</td>
      <td>277763</td>
      <td>Subscriber</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2015</td>
      <td>40530</td>
      <td>Customer</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2015</td>
      <td>305722</td>
      <td>Subscriber</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2016</td>
      <td>23204</td>
      <td>Customer</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2016</td>
      <td>187290</td>
      <td>Subscriber</td>
    </tr>
  </tbody>
</table>
</div>




```python
data1 = pd.read_csv('result1.csv')
data1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Year</th>
      <th>Number_of_trips</th>
      <th>subscriber_type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2013</td>
      <td>24499</td>
      <td>Customer</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2013</td>
      <td>76064</td>
      <td>Subscriber</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2014</td>
      <td>48576</td>
      <td>Customer</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2014</td>
      <td>277763</td>
      <td>Subscriber</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2015</td>
      <td>40530</td>
      <td>Customer</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2015</td>
      <td>305722</td>
      <td>Subscriber</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2016</td>
      <td>23204</td>
      <td>Customer</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2016</td>
      <td>187290</td>
      <td>Subscriber</td>
    </tr>
  </tbody>
</table>
</div>




```python
data2 = pd.read_csv('result2.csv')
data2
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Year</th>
      <th>subscriber_type</th>
      <th>Minutes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2013</td>
      <td>Customer</td>
      <td>1445698</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2013</td>
      <td>Subscriber</td>
      <td>763387</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2014</td>
      <td>Customer</td>
      <td>3431746</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2014</td>
      <td>Subscriber</td>
      <td>2725385</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2015</td>
      <td>Customer</td>
      <td>2449913</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2015</td>
      <td>Subscriber</td>
      <td>2980766</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2016</td>
      <td>Customer</td>
      <td>1151626</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2016</td>
      <td>Subscriber</td>
      <td>1756835</td>
    </tr>
  </tbody>
</table>
</div>




```python
data3 = pd.read_csv('result3.csv')
data3
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Day_of_the_week</th>
      <th>subscriber_type</th>
      <th>trip</th>
      <th>Minutes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Friday</td>
      <td>Customer</td>
      <td>19929</td>
      <td>1205513</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Friday</td>
      <td>Subscriber</td>
      <td>140048</td>
      <td>1394510</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Monday</td>
      <td>Customer</td>
      <td>15142</td>
      <td>820564</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Monday</td>
      <td>Subscriber</td>
      <td>154795</td>
      <td>1478570</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Saturday</td>
      <td>Customer</td>
      <td>29244</td>
      <td>2228825</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Saturday</td>
      <td>Subscriber</td>
      <td>31035</td>
      <td>363815</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Sunday</td>
      <td>Customer</td>
      <td>25908</td>
      <td>1774666</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Sunday</td>
      <td>Subscriber</td>
      <td>25467</td>
      <td>288400</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Thursday</td>
      <td>Customer</td>
      <td>16612</td>
      <td>895115</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Thursday</td>
      <td>Subscriber</td>
      <td>160296</td>
      <td>1519059</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Tuesday</td>
      <td>Customer</td>
      <td>14737</td>
      <td>766228</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Tuesday</td>
      <td>Subscriber</td>
      <td>169668</td>
      <td>1601356</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Wednesday</td>
      <td>Customer</td>
      <td>15237</td>
      <td>788072</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Wednesday</td>
      <td>Subscriber</td>
      <td>165530</td>
      <td>1580663</td>
    </tr>
  </tbody>
</table>
</div>




```python
data_final = pd.read_csv('result_final.csv')
data_final
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Day_of_the_week</th>
      <th>subscriber_type</th>
      <th>Time_of_day</th>
      <th>trip</th>
      <th>Minutes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Friday</td>
      <td>Customer</td>
      <td>Afternoon</td>
      <td>6997</td>
      <td>461255</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Friday</td>
      <td>Subscriber</td>
      <td>Afternoon</td>
      <td>20918</td>
      <td>204080</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Friday</td>
      <td>Customer</td>
      <td>Early Morning</td>
      <td>3443</td>
      <td>238153</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Friday</td>
      <td>Subscriber</td>
      <td>Early Morning</td>
      <td>57205</td>
      <td>534665</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Friday</td>
      <td>Customer</td>
      <td>Evening</td>
      <td>7384</td>
      <td>368938</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Friday</td>
      <td>Subscriber</td>
      <td>Evening</td>
      <td>54424</td>
      <td>570432</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Friday</td>
      <td>Customer</td>
      <td>Late Night</td>
      <td>277</td>
      <td>30067</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Friday</td>
      <td>Subscriber</td>
      <td>Late Night</td>
      <td>1620</td>
      <td>15621</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Friday</td>
      <td>Customer</td>
      <td>Night</td>
      <td>1828</td>
      <td>107100</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Friday</td>
      <td>Subscriber</td>
      <td>Night</td>
      <td>5881</td>
      <td>69712</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Monday</td>
      <td>Customer</td>
      <td>Afternoon</td>
      <td>5119</td>
      <td>312397</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Monday</td>
      <td>Subscriber</td>
      <td>Afternoon</td>
      <td>17570</td>
      <td>167635</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Monday</td>
      <td>Customer</td>
      <td>Early Morning</td>
      <td>2835</td>
      <td>173939</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Monday</td>
      <td>Subscriber</td>
      <td>Early Morning</td>
      <td>63755</td>
      <td>603135</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Monday</td>
      <td>Customer</td>
      <td>Evening</td>
      <td>5677</td>
      <td>247475</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Monday</td>
      <td>Subscriber</td>
      <td>Evening</td>
      <td>65123</td>
      <td>626980</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Monday</td>
      <td>Customer</td>
      <td>Late Night</td>
      <td>320</td>
      <td>24233</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Monday</td>
      <td>Subscriber</td>
      <td>Late Night</td>
      <td>1327</td>
      <td>10392</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Monday</td>
      <td>Customer</td>
      <td>Night</td>
      <td>1191</td>
      <td>62520</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Monday</td>
      <td>Subscriber</td>
      <td>Night</td>
      <td>7020</td>
      <td>70428</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Saturday</td>
      <td>Customer</td>
      <td>Afternoon</td>
      <td>12063</td>
      <td>884749</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Saturday</td>
      <td>Subscriber</td>
      <td>Afternoon</td>
      <td>10088</td>
      <td>112406</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Saturday</td>
      <td>Customer</td>
      <td>Early Morning</td>
      <td>3987</td>
      <td>318534</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Saturday</td>
      <td>Subscriber</td>
      <td>Early Morning</td>
      <td>6690</td>
      <td>96511</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Saturday</td>
      <td>Customer</td>
      <td>Evening</td>
      <td>10610</td>
      <td>570121</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Saturday</td>
      <td>Subscriber</td>
      <td>Evening</td>
      <td>9949</td>
      <td>112311</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Saturday</td>
      <td>Customer</td>
      <td>Late Night</td>
      <td>569</td>
      <td>63433</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Saturday</td>
      <td>Subscriber</td>
      <td>Late Night</td>
      <td>999</td>
      <td>10039</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Saturday</td>
      <td>Customer</td>
      <td>Night</td>
      <td>2015</td>
      <td>391988</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Saturday</td>
      <td>Subscriber</td>
      <td>Night</td>
      <td>3309</td>
      <td>32548</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>40</th>
      <td>Thusday</td>
      <td>Customer</td>
      <td>Afternoon</td>
      <td>5341</td>
      <td>309522</td>
    </tr>
    <tr>
      <th>41</th>
      <td>Thusday</td>
      <td>Subscriber</td>
      <td>Afternoon</td>
      <td>21661</td>
      <td>201613</td>
    </tr>
    <tr>
      <th>42</th>
      <td>Thusday</td>
      <td>Customer</td>
      <td>Early Morning</td>
      <td>3208</td>
      <td>192910</td>
    </tr>
    <tr>
      <th>43</th>
      <td>Thusday</td>
      <td>Subscriber</td>
      <td>Early Morning</td>
      <td>65444</td>
      <td>614884</td>
    </tr>
    <tr>
      <th>44</th>
      <td>Thusday</td>
      <td>Customer</td>
      <td>Evening</td>
      <td>6416</td>
      <td>271894</td>
    </tr>
    <tr>
      <th>45</th>
      <td>Thusday</td>
      <td>Subscriber</td>
      <td>Evening</td>
      <td>63173</td>
      <td>611310</td>
    </tr>
    <tr>
      <th>46</th>
      <td>Thusday</td>
      <td>Customer</td>
      <td>Late Night</td>
      <td>269</td>
      <td>33840</td>
    </tr>
    <tr>
      <th>47</th>
      <td>Thusday</td>
      <td>Subscriber</td>
      <td>Late Night</td>
      <td>1687</td>
      <td>13035</td>
    </tr>
    <tr>
      <th>48</th>
      <td>Thusday</td>
      <td>Customer</td>
      <td>Night</td>
      <td>1378</td>
      <td>86949</td>
    </tr>
    <tr>
      <th>49</th>
      <td>Thusday</td>
      <td>Subscriber</td>
      <td>Night</td>
      <td>8331</td>
      <td>78217</td>
    </tr>
    <tr>
      <th>50</th>
      <td>Tuesday</td>
      <td>Customer</td>
      <td>Afternoon</td>
      <td>4503</td>
      <td>281785</td>
    </tr>
    <tr>
      <th>51</th>
      <td>Tuesday</td>
      <td>Subscriber</td>
      <td>Afternoon</td>
      <td>20599</td>
      <td>190337</td>
    </tr>
    <tr>
      <th>52</th>
      <td>Tuesday</td>
      <td>Customer</td>
      <td>Early Morning</td>
      <td>3020</td>
      <td>154581</td>
    </tr>
    <tr>
      <th>53</th>
      <td>Tuesday</td>
      <td>Subscriber</td>
      <td>Early Morning</td>
      <td>69932</td>
      <td>661328</td>
    </tr>
    <tr>
      <th>54</th>
      <td>Tuesday</td>
      <td>Customer</td>
      <td>Evening</td>
      <td>5762</td>
      <td>255010</td>
    </tr>
    <tr>
      <th>55</th>
      <td>Tuesday</td>
      <td>Subscriber</td>
      <td>Evening</td>
      <td>69043</td>
      <td>659750</td>
    </tr>
    <tr>
      <th>56</th>
      <td>Tuesday</td>
      <td>Customer</td>
      <td>Late Night</td>
      <td>178</td>
      <td>19687</td>
    </tr>
    <tr>
      <th>57</th>
      <td>Tuesday</td>
      <td>Subscriber</td>
      <td>Late Night</td>
      <td>1690</td>
      <td>15366</td>
    </tr>
    <tr>
      <th>58</th>
      <td>Tuesday</td>
      <td>Customer</td>
      <td>Night</td>
      <td>1274</td>
      <td>55165</td>
    </tr>
    <tr>
      <th>59</th>
      <td>Tuesday</td>
      <td>Subscriber</td>
      <td>Night</td>
      <td>8404</td>
      <td>74575</td>
    </tr>
    <tr>
      <th>60</th>
      <td>Wednesday</td>
      <td>Customer</td>
      <td>Afternoon</td>
      <td>4508</td>
      <td>270148</td>
    </tr>
    <tr>
      <th>61</th>
      <td>Wednesday</td>
      <td>Subscriber</td>
      <td>Afternoon</td>
      <td>20497</td>
      <td>188341</td>
    </tr>
    <tr>
      <th>62</th>
      <td>Wednesday</td>
      <td>Customer</td>
      <td>Early Morning</td>
      <td>3156</td>
      <td>185732</td>
    </tr>
    <tr>
      <th>63</th>
      <td>Wednesday</td>
      <td>Subscriber</td>
      <td>Early Morning</td>
      <td>67883</td>
      <td>658014</td>
    </tr>
    <tr>
      <th>64</th>
      <td>Wednesday</td>
      <td>Customer</td>
      <td>Evening</td>
      <td>5939</td>
      <td>241974</td>
    </tr>
    <tr>
      <th>65</th>
      <td>Wednesday</td>
      <td>Subscriber</td>
      <td>Evening</td>
      <td>66587</td>
      <td>634490</td>
    </tr>
    <tr>
      <th>66</th>
      <td>Wednesday</td>
      <td>Customer</td>
      <td>Late Night</td>
      <td>294</td>
      <td>30422</td>
    </tr>
    <tr>
      <th>67</th>
      <td>Wednesday</td>
      <td>Subscriber</td>
      <td>Late Night</td>
      <td>1750</td>
      <td>16035</td>
    </tr>
    <tr>
      <th>68</th>
      <td>Wednesday</td>
      <td>Customer</td>
      <td>Night</td>
      <td>1340</td>
      <td>59796</td>
    </tr>
    <tr>
      <th>69</th>
      <td>Wednesday</td>
      <td>Subscriber</td>
      <td>Night</td>
      <td>8813</td>
      <td>83783</td>
    </tr>
  </tbody>
</table>
<p>70 rows × 5 columns</p>
</div>



---

# VISUALIZATIONS AND PIVOT TABLES


```python
#PIVOT TABLE TO ANALYZE THE GROWTH OF SUBSCRIBER AND CUSTOMER OVER THE YEARS

pivoted1 = pd.pivot_table(data1, values='Number_of_trips', columns='subscriber_type', index='Year')
pivoted1
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>subscriber_type</th>
      <th>Customer</th>
      <th>Subscriber</th>
    </tr>
    <tr>
      <th>Year</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013</th>
      <td>24499</td>
      <td>76064</td>
    </tr>
    <tr>
      <th>2014</th>
      <td>48576</td>
      <td>277763</td>
    </tr>
    <tr>
      <th>2015</th>
      <td>40530</td>
      <td>305722</td>
    </tr>
    <tr>
      <th>2016</th>
      <td>23204</td>
      <td>187290</td>
    </tr>
  </tbody>
</table>
</div>




```python
#VISUALIZATION TO VISUALIZE THE CHANGE IN THE NUMBER OF TRIPS TAKEN BY SUBSCRIBERS VS CUSTOMERS

import matplotlib.pyplot as plt
fig = plt.figure(figsize=(12, 4))
ax = plt.subplot(1,1,1) 
pivoted1.plot.line(stacked=False, ax=ax)
plt.xlabel('Year')
plt.ylabel('Number of Trips')
plt.xticks(rotation=0)
plt.tight_layout()
ax.set_facecolor('xkcd:white')
plt.legend(loc = "best")
plt.title("Change in the Number of Trips by Subscribers\n Year 2013 - 2016")
plt.show()
```


![png](output_33_0.png)



```python
#PIVOT TABLE TO ANALYZE THE DURATION OF TRIPS FOR SUBSCRIBER AND CUSTOMER OVER THE YEARS

pivoted2 = pd.pivot_table(data2, values='Minutes', columns='subscriber_type', index='Year')
pivoted2
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>subscriber_type</th>
      <th>Customer</th>
      <th>Subscriber</th>
    </tr>
    <tr>
      <th>Year</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2013</th>
      <td>1445698</td>
      <td>763387</td>
    </tr>
    <tr>
      <th>2014</th>
      <td>3431746</td>
      <td>2725385</td>
    </tr>
    <tr>
      <th>2015</th>
      <td>2449913</td>
      <td>2980766</td>
    </tr>
    <tr>
      <th>2016</th>
      <td>1151626</td>
      <td>1756835</td>
    </tr>
  </tbody>
</table>
</div>




```python
#VISUALIZATION TO VISUALIZE THE CHANGE IN THE DURATION OF TRIPS TAKEN BY SUBSCRIBERS VS CUSTOMERS

import matplotlib.pyplot as plt
fig = plt.figure(figsize=(12, 4))
ax = plt.subplot(1,1,1) 
pivoted2.plot.line(stacked=False, ax=ax)
plt.xlabel('Year')
plt.ylabel('Duration of Trips (Minutes)')
plt.xticks(rotation=0)
plt.legend(loc = "best")
plt.title("Change in the Number of Trips Minutes by Subscribers\n Year 2013 - 2016")
plt.show()
```


![png](output_35_0.png)



```python
#PIVOT TABLE TO ANALYZE THE DURATION OF TRIPS FOR SUBSCRIBER AND CUSTOMER OVER DIFFERENT DAYS OF THE WEEK

pivoted3 = pd.pivot_table(data3, values='Minutes', columns='subscriber_type', index='Day_of_the_week')
pivoted3 = pivoted3.reindex(["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"])
```


```python
#VISUALIZATION TO VISUALIZE THE DURATION OF TRIPS FOR SUBSCRIBER AND CUSTOMER OVER DIFFERENT DAYS OF THE WEEK


import matplotlib.pyplot as plt
fig = plt.figure(figsize=(12, 4))
ax = plt.subplot(1,1,1) 
pivoted3.plot.bar(stacked=False, ax=ax)
plt.xlabel('Year')
plt.ylabel('Duration of Trips (Minutes)')
plt.xticks(rotation=0)
plt.legend(loc = "best")
plt.title("Duration of the Trip by Day of the Week")
plt.show()
```


![png](output_37_0.png)



```python
#PIVOT TABLE TO ANALYZE THE NUMBER OF TRIPS FOR SUBSCRIBER AND CUSTOMER OVER DIFFERENT DAYS OF THE WEEK

pivoted4 = pd.pivot_table(data3, values='trip', columns='subscriber_type', index='Day_of_the_week')
pivoted4 = pivoted4.reindex(["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"])
```


```python
#VISUALIZATION TO VISUALIZE THE NUMBER OF TRIPS FOR SUBSCRIBER AND CUSTOMER OVER DIFFERENT DAYS OF THE WEEK

import matplotlib.pyplot as plt
fig = plt.figure(figsize=(12, 4))
ax = plt.subplot(1,1,1) 
pivoted4.plot.bar(stacked=False, ax=ax)
plt.xlabel('Year')
plt.ylabel('Number of Trips')
plt.xticks(rotation=0)
plt.legend(loc = "best")
plt.title("Number of Trip by Day of the Week")
plt.show()
```


![png](output_39_0.png)



```python
#PIVOT TABLE TO ANALYZE THE NUMBER OF TRIPS FOR CUSTOMER OVER DIFFERENT DAYS OF THE WEEK AT DIFFERENT TIMES OF THE DAY

data_final_customer = data_final[data_final['subscriber_type']== "Customer"]
pivoted_final_customer = data_final_customer.groupby(['Day_of_the_week','Time_of_day'])['trip'].sum().unstack('Time_of_day').fillna(0)
pivoted_final_customer = pd.DataFrame(data = pivoted_final_customer)
pivoted_final_customer = pivoted_final_customer.reindex(["Monday", "Tuesday", "Wednesday", "Thusday", "Friday", "Saturday", "Sunday"])
pivoted_final_customer
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Time_of_day</th>
      <th>Afternoon</th>
      <th>Early Morning</th>
      <th>Evening</th>
      <th>Late Night</th>
      <th>Night</th>
    </tr>
    <tr>
      <th>Day_of_the_week</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Monday</th>
      <td>5119</td>
      <td>2835</td>
      <td>5677</td>
      <td>320</td>
      <td>1191</td>
    </tr>
    <tr>
      <th>Tuesday</th>
      <td>4503</td>
      <td>3020</td>
      <td>5762</td>
      <td>178</td>
      <td>1274</td>
    </tr>
    <tr>
      <th>Wednesday</th>
      <td>4508</td>
      <td>3156</td>
      <td>5939</td>
      <td>294</td>
      <td>1340</td>
    </tr>
    <tr>
      <th>Thusday</th>
      <td>5341</td>
      <td>3208</td>
      <td>6416</td>
      <td>269</td>
      <td>1378</td>
    </tr>
    <tr>
      <th>Friday</th>
      <td>6997</td>
      <td>3443</td>
      <td>7384</td>
      <td>277</td>
      <td>1828</td>
    </tr>
    <tr>
      <th>Saturday</th>
      <td>12063</td>
      <td>3987</td>
      <td>10610</td>
      <td>569</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>Sunday</th>
      <td>10511</td>
      <td>3996</td>
      <td>9436</td>
      <td>664</td>
      <td>1301</td>
    </tr>
  </tbody>
</table>
</div>




```python
#VISUALIZATION TO VISUALIZE THE NUMBER OF TRIPS FOR CUSTOMER OVER DIFFERENT DAYS OF THE WEEK AT DIFFERENT TIMES OF THE DAY

fig = plt.figure(figsize=(12, 4))
ax = plt.subplot(1,1,1) 
pivoted_final_customer.plot.bar(stacked=True, ax=ax)
plt.xlabel('Day of the Week')
plt.ylabel('Number of Trips')
plt.xticks(rotation=0)
plt.legend(loc = "best")
plt.title("Number of Trips by Day of the Week and Time of the Day for 'Customers'")
plt.show()
```


![png](output_41_0.png)



```python
#PIVOT TABLE TO ANALYZE THE NUMBER OF TRIPS FOR SUBSCRIBER OVER DIFFERENT DAYS OF THE WEEK AT DIFFERENT TIMES OF THE DAY

data_final_subscriber = data_final[data_final['subscriber_type']== "Subscriber"]
pivoted_final_subscriber = data_final_subscriber.groupby(['Day_of_the_week','Time_of_day'])['trip'].sum().unstack('Time_of_day').fillna(0)
pivoted_final_subscriber = pd.DataFrame(data = pivoted_final_subscriber)
pivoted_final_subscriber = pivoted_final_subscriber.reindex(["Monday", "Tuesday", "Wednesday", "Thusday", "Friday", "Saturday", "Sunday"])
pivoted_final_subscriber
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Time_of_day</th>
      <th>Afternoon</th>
      <th>Early Morning</th>
      <th>Evening</th>
      <th>Late Night</th>
      <th>Night</th>
    </tr>
    <tr>
      <th>Day_of_the_week</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Monday</th>
      <td>17570</td>
      <td>63755</td>
      <td>65123</td>
      <td>1327</td>
      <td>7020</td>
    </tr>
    <tr>
      <th>Tuesday</th>
      <td>20599</td>
      <td>69932</td>
      <td>69043</td>
      <td>1690</td>
      <td>8404</td>
    </tr>
    <tr>
      <th>Wednesday</th>
      <td>20497</td>
      <td>67883</td>
      <td>66587</td>
      <td>1750</td>
      <td>8813</td>
    </tr>
    <tr>
      <th>Thusday</th>
      <td>21661</td>
      <td>65444</td>
      <td>63173</td>
      <td>1687</td>
      <td>8331</td>
    </tr>
    <tr>
      <th>Friday</th>
      <td>20918</td>
      <td>57205</td>
      <td>54424</td>
      <td>1620</td>
      <td>5881</td>
    </tr>
    <tr>
      <th>Saturday</th>
      <td>10088</td>
      <td>6690</td>
      <td>9949</td>
      <td>999</td>
      <td>3309</td>
    </tr>
    <tr>
      <th>Sunday</th>
      <td>8548</td>
      <td>4860</td>
      <td>8800</td>
      <td>874</td>
      <td>2385</td>
    </tr>
  </tbody>
</table>
</div>




```python
#VISUALIZATION TO VISUALIZE THE NUMBER OF TRIPS FOR SUBSCRIBER OVER DIFFERENT DAYS OF THE WEEK AT DIFFERENT TIMES OF THE DAY


fig = plt.figure(figsize=(12, 4))
ax = plt.subplot(1,1,1) 
pivoted_final_subscriber.plot.bar(stacked=True, ax=ax)
plt.xlabel('Day of the Week')
plt.ylabel('Number of Trips')
plt.xticks(rotation=0)
plt.legend(loc = "best")
plt.title("Number of Trips by Day of the Week and Time of the Day for 'Subscribers'")
plt.show()
```


![png](output_43_0.png)



```python
#PIVOT TABLE TO ANALYZE THE DURATION OF TRIPS FOR CUSTOMER OVER DIFFERENT DAYS OF THE WEEK AT DIFFERENT TIMES OF THE DAY

data_final_customer_mins = data_final[data_final['subscriber_type']== "Customer"]
pivoted_final_customer_mins = data_final_customer_mins.groupby(['Day_of_the_week','Time_of_day'])['Minutes'].sum().unstack('Time_of_day').fillna(0)
pivoted_final_customer_mins = pd.DataFrame(data = pivoted_final_customer_mins)
pivoted_final_customer_mins = pivoted_final_customer_mins.reindex(["Monday", "Tuesday", "Wednesday", "Thusday", "Friday", "Saturday", "Sunday"])
pivoted_final_customer_mins
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Time_of_day</th>
      <th>Afternoon</th>
      <th>Early Morning</th>
      <th>Evening</th>
      <th>Late Night</th>
      <th>Night</th>
    </tr>
    <tr>
      <th>Day_of_the_week</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Monday</th>
      <td>312397</td>
      <td>173939</td>
      <td>247475</td>
      <td>24233</td>
      <td>62520</td>
    </tr>
    <tr>
      <th>Tuesday</th>
      <td>281785</td>
      <td>154581</td>
      <td>255010</td>
      <td>19687</td>
      <td>55165</td>
    </tr>
    <tr>
      <th>Wednesday</th>
      <td>270148</td>
      <td>185732</td>
      <td>241974</td>
      <td>30422</td>
      <td>59796</td>
    </tr>
    <tr>
      <th>Thusday</th>
      <td>309522</td>
      <td>192910</td>
      <td>271894</td>
      <td>33840</td>
      <td>86949</td>
    </tr>
    <tr>
      <th>Friday</th>
      <td>461255</td>
      <td>238153</td>
      <td>368938</td>
      <td>30067</td>
      <td>107100</td>
    </tr>
    <tr>
      <th>Saturday</th>
      <td>884749</td>
      <td>318534</td>
      <td>570121</td>
      <td>63433</td>
      <td>391988</td>
    </tr>
    <tr>
      <th>Sunday</th>
      <td>753427</td>
      <td>316107</td>
      <td>477537</td>
      <td>130017</td>
      <td>97578</td>
    </tr>
  </tbody>
</table>
</div>




```python
#VISUALIZATION TO VISUALIZE THE DURATION OF TRIPS FOR CUSTOMER OVER DIFFERENT DAYS OF THE WEEK AT DIFFERENT TIMES OF THE DAY


fig = plt.figure(figsize=(12, 4))
ax = plt.subplot(1,1,1) 
pivoted_final_customer_mins.plot.bar(stacked=True, ax=ax)
plt.xlabel('Day of the Week')
plt.ylabel('Duration of Trips')
plt.xticks(rotation=0)
plt.legend(loc = "best")
plt.title("Duration of Trip by Day of the Week and Time of the Day for 'Customers'")
plt.show()
```


![png](output_45_0.png)



```python
#PIVOT TABLE TO ANALYZE THE DURATION OF TRIPS FOR SUBSCRIBER OVER DIFFERENT DAYS OF THE WEEK AT DIFFERENT TIMES OF THE DAY

data_final_sub_mins = data_final[data_final['subscriber_type']== "Subscriber"]
pivoted_final_subs_mins = data_final_sub_mins.groupby(['Day_of_the_week','Time_of_day'])['Minutes'].sum().unstack('Time_of_day').fillna(0)
pivoted_final_sub_mins = pd.DataFrame(data = pivoted_final_customer)
pivoted_final_sub_mins = pivoted_final_sub_mins.reindex(["Monday", "Tuesday", "Wednesday", "Thusday", "Friday", "Saturday", "Sunday"])
pivoted_final_sub_mins
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>Time_of_day</th>
      <th>Afternoon</th>
      <th>Early Morning</th>
      <th>Evening</th>
      <th>Late Night</th>
      <th>Night</th>
    </tr>
    <tr>
      <th>Day_of_the_week</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Monday</th>
      <td>5119</td>
      <td>2835</td>
      <td>5677</td>
      <td>320</td>
      <td>1191</td>
    </tr>
    <tr>
      <th>Tuesday</th>
      <td>4503</td>
      <td>3020</td>
      <td>5762</td>
      <td>178</td>
      <td>1274</td>
    </tr>
    <tr>
      <th>Wednesday</th>
      <td>4508</td>
      <td>3156</td>
      <td>5939</td>
      <td>294</td>
      <td>1340</td>
    </tr>
    <tr>
      <th>Thusday</th>
      <td>5341</td>
      <td>3208</td>
      <td>6416</td>
      <td>269</td>
      <td>1378</td>
    </tr>
    <tr>
      <th>Friday</th>
      <td>6997</td>
      <td>3443</td>
      <td>7384</td>
      <td>277</td>
      <td>1828</td>
    </tr>
    <tr>
      <th>Saturday</th>
      <td>12063</td>
      <td>3987</td>
      <td>10610</td>
      <td>569</td>
      <td>2015</td>
    </tr>
    <tr>
      <th>Sunday</th>
      <td>10511</td>
      <td>3996</td>
      <td>9436</td>
      <td>664</td>
      <td>1301</td>
    </tr>
  </tbody>
</table>
</div>




```python
#VISUALIZATION TO VISUALIZE THE DURATION OF TRIPS FOR SUBSCRIBER OVER DIFFERENT DAYS OF THE WEEK AT DIFFERENT TIMES OF THE DAY


fig = plt.figure(figsize=(12, 4))
ax = plt.subplot(1,1,1) 
pivoted_final_sub_mins.plot.bar(stacked=True, ax=ax)
plt.xlabel('Day of the Week')
plt.ylabel('Duration of Trips')
plt.xticks(rotation=0)
plt.legend(loc = "best")
plt.title("Duration of Trip by Day of the Week and Time of the Day for 'Subscribers'")
plt.show()
```


![png](output_47_0.png)



```python

```
