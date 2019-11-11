---
title: "Web Scrapping Project"
date: 2019-11-12
tags: [web crawling, data science]
header:
  
 excerpt: "Data Wrangling, Data Science, Messy Data"
 mathjax: "true"
---




```python
#import all the required modules
import re
import requests, bs4
from requests import get
from bs4 import BeautifulSoup
from IPython.core.display import clear_output
from warnings import warn
from time import sleep
from random import randint
from time import time

#specify the header, to ensure our requests are accepted
headers = {'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.77 Safari/537.36'}

#loop through pages 0 -10 at interval of 2
pages = [str(i) for i in range(0,10,2)]

#declare lists to stored scraped data
names = []
release_dates = []
ratings= []
meta_scores =[]
user_scores = []

#prepare the monitoring loop
start_time = time()
requests = 0
        
# iterate through the pages
for page in pages:
    
    #make a get request
    movies = get('https://www.metacritic.com/browse/movies/score/metascore/all/filtered?page='+ page, headers = headers)
    
    #pause the loop for 8-20 seconds
    sleep(randint(8,20))
    
    #monitor the requests
    requests += 1
    elapsed_time = time() - start_time
    print('Request:{}; Frequency: {} requests/s'.format(requests, requests/elapsed_time))
    clear_output(wait = True)
    
    #show a warning if a non 200 status code is returned
    if movies.status_code != 200:
        warn('Request: {}; Status code: {}'.format(requests, response.status_code))
    
    #break the loop if the requests exceed 26
    if requests > 10:
        warn('Number of requests was greater than expected.')
        break
   
    #parse the  movie response content into the beautiful soup object
    movie_soup = BeautifulSoup(movies.text, 'html.parser')
    
    #find the major tag peculiar to each movie
    container = movie_soup.find_all('td', class_ = 'clamp-summary-wrap')
     
    #iterate through the major tag   
    for con in container:
        #scrape the movie names
        name = con.find('h3').text
        names.append(name)
    
        #scrape the release_dates
        release_date = con.select('div.clamp-details span')[0].text
        release_dates.append(release_date)
    
        #scrape the ratings
        rating = con.select('div.clamp-details span')[1].text
        ratings.append(rating)
    
        #scrape the meta scores
        meta_score= con.select('a.metascore_anchor div')[0].text
        meta_scores.append(meta_score)
    
        #scrape the user scores.
        user_score = con.select('a.metascore_anchor div')[2].text
        user_scores.append(user_score)


import pandas as pd
movie_df = pd.DataFrame({'Movie_names': names,
'Release_dates': release_dates,
'Ratings': ratings,
'Meta_scores': meta_scores,
'User_scores': user_scores})
print(movie_df.info())
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 500 entries, 0 to 499
    Data columns (total 5 columns):
    Meta_scores      500 non-null object
    Movie_names      500 non-null object
    Ratings          500 non-null object
    Release_dates    500 non-null object
    User_scores      500 non-null object
    dtypes: object(5)
    memory usage: 19.6+ KB
    None



```python
movie_df
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
      <th>Meta_scores</th>
      <th>Movie_names</th>
      <th>Ratings</th>
      <th>Release_dates</th>
      <th>User_scores</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>100</td>
      <td>Citizen Kane</td>
      <td>| Approved</td>
      <td>September 4, 1941</td>
      <td>8.4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>100</td>
      <td>The Godfather</td>
      <td>| R</td>
      <td>March 11, 1972</td>
      <td>9.2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>100</td>
      <td>Rear Window</td>
      <td>| TV-G</td>
      <td>September 1, 1954</td>
      <td>8.8</td>
    </tr>
    <tr>
      <th>3</th>
      <td>100</td>
      <td>Casablanca</td>
      <td>| TV-PG</td>
      <td>January 23, 1943</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>100</td>
      <td>Boyhood</td>
      <td>| R</td>
      <td>July 11, 2014</td>
      <td>7.6</td>
    </tr>
    <tr>
      <th>5</th>
      <td>100</td>
      <td>Three Colors: Red</td>
      <td>| R</td>
      <td>November 23, 1994</td>
      <td>8.7</td>
    </tr>
    <tr>
      <th>6</th>
      <td>100</td>
      <td>Vertigo</td>
      <td>| PG</td>
      <td>May 28, 1958</td>
      <td>8.7</td>
    </tr>
    <tr>
      <th>7</th>
      <td>100</td>
      <td>Notorious</td>
      <td>| Approved</td>
      <td>September 6, 1946</td>
      <td>7.9</td>
    </tr>
    <tr>
      <th>8</th>
      <td>99</td>
      <td>Singin' in the Rain</td>
      <td>| G</td>
      <td>April 11, 1952</td>
      <td>8.8</td>
    </tr>
    <tr>
      <th>9</th>
      <td>99</td>
      <td>City Lights</td>
      <td>| Passed</td>
      <td>March 7, 1931</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>10</th>
      <td>99</td>
      <td>Moonlight</td>
      <td>| Not Rated</td>
      <td>October 21, 2016</td>
      <td>7.2</td>
    </tr>
    <tr>
      <th>11</th>
      <td>99</td>
      <td>Pinocchio</td>
      <td>| Passed</td>
      <td>February 23, 1940</td>
      <td>8.3</td>
    </tr>
    <tr>
      <th>12</th>
      <td>99</td>
      <td>Touch of Evil</td>
      <td>| PG-13</td>
      <td>February 1, 1958</td>
      <td>7.8</td>
    </tr>
    <tr>
      <th>13</th>
      <td>98</td>
      <td>The Treasure of the Sierra Madre</td>
      <td>| TV-PG</td>
      <td>January 24, 1948</td>
      <td>8.5</td>
    </tr>
    <tr>
      <th>14</th>
      <td>98</td>
      <td>Pan's Labyrinth</td>
      <td>| R</td>
      <td>December 29, 2006</td>
      <td>8.7</td>
    </tr>
    <tr>
      <th>15</th>
      <td>98</td>
      <td>North by Northwest</td>
      <td>| TV-G</td>
      <td>August 6, 1959</td>
      <td>8.1</td>
    </tr>
    <tr>
      <th>16</th>
      <td>98</td>
      <td>Rashomon</td>
      <td>| Not Rated</td>
      <td>December 26, 1951</td>
      <td>8.3</td>
    </tr>
    <tr>
      <th>17</th>
      <td>98</td>
      <td>All About Eve</td>
      <td>| TV-PG</td>
      <td>October 27, 1950</td>
      <td>8.8</td>
    </tr>
    <tr>
      <th>18</th>
      <td>98</td>
      <td>Hoop Dreams</td>
      <td>| PG-13</td>
      <td>October 14, 1994</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>97</td>
      <td>My Left Foot</td>
      <td>| R</td>
      <td>March 30, 1990</td>
      <td>8.5</td>
    </tr>
    <tr>
      <th>20</th>
      <td>97</td>
      <td>The Third Man</td>
      <td>| Approved</td>
      <td>September 3, 1949</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>21</th>
      <td>97</td>
      <td>Dr. Strangelove or: How I Learned to Stop Worr...</td>
      <td>| GP</td>
      <td>January 29, 1964</td>
      <td>8.3</td>
    </tr>
    <tr>
      <th>22</th>
      <td>97</td>
      <td>Gone with the Wind</td>
      <td>| TV-PG</td>
      <td>January 17, 1940</td>
      <td>8.4</td>
    </tr>
    <tr>
      <th>23</th>
      <td>97</td>
      <td>4 Months, 3 Weeks and 2 Days</td>
      <td>| Not Rated</td>
      <td>January 23, 2008</td>
      <td>7.9</td>
    </tr>
    <tr>
      <th>24</th>
      <td>97</td>
      <td>Some Like It Hot</td>
      <td>| Approved</td>
      <td>March 29, 1959</td>
      <td>8.3</td>
    </tr>
    <tr>
      <th>25</th>
      <td>97</td>
      <td>Psycho</td>
      <td>| M</td>
      <td>September 8, 1960</td>
      <td>9.1</td>
    </tr>
    <tr>
      <th>26</th>
      <td>97</td>
      <td>American Graffiti</td>
      <td>| PG</td>
      <td>August 11, 1973</td>
      <td>8.1</td>
    </tr>
    <tr>
      <th>27</th>
      <td>96</td>
      <td>Dumbo</td>
      <td>| Approved</td>
      <td>October 31, 1941</td>
      <td>8.1</td>
    </tr>
    <tr>
      <th>28</th>
      <td>96</td>
      <td>Roma</td>
      <td>| Not Rated</td>
      <td>November 21, 2018</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>96</td>
      <td>Ran</td>
      <td>| R</td>
      <td>December 20, 1985</td>
      <td>8.4</td>
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
      <th>470</th>
      <td>82</td>
      <td>Good Bye, Dragon Inn</td>
      <td>Not Rated</td>
      <td>September 17, 2004</td>
      <td>5.9</td>
    </tr>
    <tr>
      <th>471</th>
      <td>82</td>
      <td>Safe Conduct</td>
      <td>Not Rated</td>
      <td>October 11, 2002</td>
      <td>6.2</td>
    </tr>
    <tr>
      <th>472</th>
      <td>82</td>
      <td>Leaving Las Vegas</td>
      <td>| R</td>
      <td>October 27, 1995</td>
      <td>8.9</td>
    </tr>
    <tr>
      <th>473</th>
      <td>82</td>
      <td>Maiden</td>
      <td>| Not Rated</td>
      <td>June 28, 2019</td>
      <td>7.6</td>
    </tr>
    <tr>
      <th>474</th>
      <td>82</td>
      <td>War for the Planet of the Apes</td>
      <td>| PG-13</td>
      <td>July 14, 2017</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>475</th>
      <td>82</td>
      <td>Duma</td>
      <td>| PG</td>
      <td>August 5, 2005</td>
      <td>8.5</td>
    </tr>
    <tr>
      <th>476</th>
      <td>82</td>
      <td>The Constant Gardener</td>
      <td>| R</td>
      <td>August 31, 2005</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>477</th>
      <td>82</td>
      <td>Short Term 12</td>
      <td>| R</td>
      <td>August 23, 2013</td>
      <td>8.5</td>
    </tr>
    <tr>
      <th>478</th>
      <td>82</td>
      <td>Parenthood</td>
      <td>| PG-13</td>
      <td>August 2, 1989</td>
      <td>8.5</td>
    </tr>
    <tr>
      <th>479</th>
      <td>82</td>
      <td>Star Wars: Episode V - The Empire Strikes Back</td>
      <td>| PG</td>
      <td>May 21, 1980</td>
      <td>9.0</td>
    </tr>
    <tr>
      <th>480</th>
      <td>82</td>
      <td>Our Beloved Month of August</td>
      <td>| Not Rated</td>
      <td>September 3, 2010</td>
      <td>tbd</td>
    </tr>
    <tr>
      <th>481</th>
      <td>82</td>
      <td>Sugar</td>
      <td>| R</td>
      <td>April 3, 2009</td>
      <td>7.8</td>
    </tr>
    <tr>
      <th>482</th>
      <td>82</td>
      <td>Marwencol</td>
      <td>| Not Rated</td>
      <td>October 8, 2010</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>483</th>
      <td>82</td>
      <td>The Wind That Shakes the Barley</td>
      <td>| Not Rated</td>
      <td>March 16, 2007</td>
      <td>7.9</td>
    </tr>
    <tr>
      <th>484</th>
      <td>82</td>
      <td>Face/Off</td>
      <td>| R</td>
      <td>June 27, 1997</td>
      <td>8.8</td>
    </tr>
    <tr>
      <th>485</th>
      <td>82</td>
      <td>The Lobster</td>
      <td>| R</td>
      <td>May 13, 2016</td>
      <td>7.0</td>
    </tr>
    <tr>
      <th>486</th>
      <td>82</td>
      <td>The Nightmare Before Christmas</td>
      <td>| PG</td>
      <td>October 22, 1993</td>
      <td>8.6</td>
    </tr>
    <tr>
      <th>487</th>
      <td>82</td>
      <td>2001: A Space Odyssey</td>
      <td>| G</td>
      <td>April 2, 1968</td>
      <td>8.1</td>
    </tr>
    <tr>
      <th>488</th>
      <td>82</td>
      <td>Pride &amp; Prejudice</td>
      <td>| PG</td>
      <td>November 11, 2005</td>
      <td>8.7</td>
    </tr>
    <tr>
      <th>489</th>
      <td>82</td>
      <td>The Squid and the Whale</td>
      <td>| R</td>
      <td>October 5, 2005</td>
      <td>7.4</td>
    </tr>
    <tr>
      <th>490</th>
      <td>82</td>
      <td>Winged Migration</td>
      <td>| G</td>
      <td>April 18, 2003</td>
      <td>8.7</td>
    </tr>
    <tr>
      <th>491</th>
      <td>82</td>
      <td>Quince Tree of the Sun</td>
      <td>Not Rated</td>
      <td>May 5, 2000</td>
      <td>tbd</td>
    </tr>
    <tr>
      <th>492</th>
      <td>82</td>
      <td>School of Rock</td>
      <td>| PG-13</td>
      <td>October 3, 2003</td>
      <td>8.5</td>
    </tr>
    <tr>
      <th>493</th>
      <td>82</td>
      <td>Life and Nothing More</td>
      <td>| Not Rated</td>
      <td>October 24, 2018</td>
      <td>8.2</td>
    </tr>
    <tr>
      <th>494</th>
      <td>82</td>
      <td>Star Trek</td>
      <td>| PG-13</td>
      <td>May 7, 2009</td>
      <td>7.9</td>
    </tr>
    <tr>
      <th>495</th>
      <td>82</td>
      <td>A Quiet Place</td>
      <td>| PG-13</td>
      <td>April 6, 2018</td>
      <td>7.4</td>
    </tr>
    <tr>
      <th>496</th>
      <td>82</td>
      <td>Deliverance</td>
      <td>| TV-14</td>
      <td>July 21, 1972</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>497</th>
      <td>82</td>
      <td>Frances Ha</td>
      <td>| R</td>
      <td>May 17, 2013</td>
      <td>7.7</td>
    </tr>
    <tr>
      <th>498</th>
      <td>82</td>
      <td>The Namesake</td>
      <td>| PG-13</td>
      <td>March 9, 2007</td>
      <td>8.0</td>
    </tr>
    <tr>
      <th>499</th>
      <td>82</td>
      <td>A Hijacking</td>
      <td>| R</td>
      <td>June 21, 2013</td>
      <td>7.3</td>
    </tr>
  </tbody>
</table>
<p>500 rows × 5 columns</p>
</div>


