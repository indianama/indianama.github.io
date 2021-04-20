---
title: Mcdonald's North and East Data Scraping
url: mcdonaldsnorth
date: 2021-04-19T10:30:03+05:30
author: Allwyn
tags: ['web scraping', 'pandas', 'beautiful soup']
cover:
   image: mcd1.png
   alt: Burger
---



<h1>Contents<span class="tocSkip"></span></h1>

<div class="toc"><ul class="toc-item"><li><span><a href="#Premise" data-toc-modified-id="Premise-1"><span class="toc-item-num">1&nbsp;&nbsp;</span>Premise</a></span></li><li><span><a href="#Imports" data-toc-modified-id="Imports-2"><span class="toc-item-num">2&nbsp;&nbsp;</span>Imports</a></span></li><li><span><a href="#Headers-and-Var-Setup" data-toc-modified-id="Headers-and-Var-Setup-3"><span class="toc-item-num">3&nbsp;&nbsp;</span>Headers and Var Setup</a></span></li><li><span><a href="#Scraping-The-Data" data-toc-modified-id="Scraping-The-Data-4"><span class="toc-item-num">4&nbsp;&nbsp;</span>Scraping The Data</a></span></li><li><span><a href="#Viewing-the-Scraped-Data" data-toc-modified-id="Viewing-the-Scraped-Data-5"><span class="toc-item-num">5&nbsp;&nbsp;</span>Viewing the Scraped Data</a></span></li><li><span><a href="#Links" data-toc-modified-id="Links-6"><span class="toc-item-num">6&nbsp;&nbsp;</span>Links</a></span></li></ul></div>

# Premise

Mcdonald's India is divided into two entities:
* Connaught Plaza Restaurants Private Limited: North & East,
* Hardcastle Restaurants Private Limited: South & West.

This notebook scrapes the data for the **North & East** from the official [website](mcdindia.com).

# Imports

We will be using BeautifulSoup to scrape the data and pandas to store the scraped data.


```python
import pandas as pd
import requests
from bs4 import BeautifulSoup
```

# Headers and Var Setup

Headers can be set via copying from Firefox or any other browser of your choice. Setting headers like this will help you to emulate the browser to the server.


```python
headers = {
    'authority': 'mk0mcdonaldsindfqrur.kinstacdn.com',
    'cache-control': 'max-age=0',
    'sec-ch-ua': '"Google Chrome";v="89", "Chromium";v="89", ";Not A Brand";v="99"',
    'sec-ch-ua-mobile': '?0',
    'upgrade-insecure-requests': '1',
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.114 Safari/537.36',
    'accept': 'image/avif,image/webp,image/apng,image/svg+xml,image/*,*/*;q=0.8',
    'sec-fetch-site': 'cross-site',
    'sec-fetch-mode': 'no-cors',
    'sec-fetch-user': '?1',
    'sec-fetch-dest': 'image',
    'accept-language': 'en-US,en;q=0.9,en-GB;q=0.8',
    'dnt': '1',
    'sec-gpc': '1',
    'Referer': 'https://mk0mcdonaldsindfqrur.kinstacdn.com/wp-content/cache/min/1/6f0793e0e20e81170d362eaa34f331d1.css',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.114 Safari/537.36',
    'Origin': 'https://mcdindia.com',
    'referer': 'https://mk0mcdonaldsindfqrur.kinstacdn.com/wp-content/cache/min/1/6f0793e0e20e81170d362eaa34f331d1.css',
    'Upgrade-Insecure-Requests': '1',
    'If-None-Match': '"7757U3yUJtOQign6O5Ji55SURec="',
}
```

# Scraping The Data

The response variable will store the contents of response received.


```python
response = requests.get('https://mcdindia.com/', headers=headers)
```


```python
html = response.text
soup = BeautifulSoup(html, 'html.parser')
```


```python
content = soup.find_all('div', {"class":"store-item Open"})
```

By using a dictionary to all the matching 'divs' we can convert to a pandas dataframe.


```python
storeList = []
for item in content:
    listingDict = {}
    listingDict['city'] = item['data-city']
    listingDict['storeType'] = item['data-storetype']
    listingDict['address'] = item.p.text
    listingDict['telephone'] = item.find_all('a')[0]['href'][4:]
    listingDict['geolocation'] = item.find_all('a')[1]['href']
    listingDict['dineIn'] = item.find_all('span',{"class":"status-text"})[0].text
    listingDict['delivery'] = item.find_all('span',{"class":"status-text"})[1].text
    storeList.append(listingDict)
```


```python
mcd_stores = pd.DataFrame(storeList)
mcd_stores.to_csv('mcd_stores_in_NE_india.csv', index=False)
```

# Viewing the Scraped Data


```python
mcd_stores.head(20)
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
      <th>city</th>
      <th>storeType</th>
      <th>address</th>
      <th>telephone</th>
      <th>geolocation</th>
      <th>dineIn</th>
      <th>delivery</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Faridabad</td>
      <td>Kiosk, Delivery</td>
      <td>1-2, SRS World, GF City Centre, Sector-12, Far...</td>
      <td>9873400698</td>
      <td>https://goo.gl/maps/teguuGjf9AkE29iz6</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Bhatinda</td>
      <td>Breakfast Store, Drive-Thru</td>
      <td>12, Village Bhucho Kalan. Tehsil Nathana, Dist...</td>
      <td>8283888099</td>
      <td>https://goo.gl/maps/CTQEoZdQCP9yizWW9</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Rohtak</td>
      <td>Delivery</td>
      <td>1238-1284 (P), Ward No. 29, Delhi Road. Opp. T...</td>
      <td>8295876611</td>
      <td>https://goo.gl/maps/47WQvEYfGGdFcmUL9</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Delhi</td>
      <td>Breakfast Store, Kiosk, Delivery</td>
      <td>15, Ground Floor, Shop no:- 3, Sector:- 5, Raj...</td>
      <td>9899730908</td>
      <td>https://goo.gl/maps/aowwMoEDAoj4Xea57</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Delhi</td>
      <td>Delivery</td>
      <td>16, Manglam Palace, DDA Central Market Sector-...</td>
      <td>9899795814</td>
      <td>https://goo.gl/maps/EuvM6yi1SyhL55eaA</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Delhi</td>
      <td>Kiosk, Delivery</td>
      <td>17A, Regal Building. Connaught Place. New Delh...</td>
      <td>9873162936</td>
      <td>https://goo.gl/maps/U9isTtTGbzppVK7m7</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Kurukshetra</td>
      <td>Delivery, Birthday Store</td>
      <td>1st Floor. Block No. A, Divine B'ness Park. Op...</td>
      <td>9996546611</td>
      <td>https://goo.gl/maps/6kSBEPKL3pDq7oot5</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Delhi</td>
      <td>Extended Hours, Delivery</td>
      <td>2, Community Centre, Saket, New Delhi- 110017</td>
      <td>9899795802</td>
      <td>https://goo.gl/maps/StvwwvrTtL3DwnTA7</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Delhi</td>
      <td>Breakfast Store, Delivery, Birthday Store</td>
      <td>23 West Punjabi Bagh. Central Market , New Del...</td>
      <td>9899795807</td>
      <td>https://goo.gl/maps/BYyqhyBX9EYxhczx6</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Aligarh</td>
      <td>Delivery, Birthday Store</td>
      <td>3/ 106A- I, Ground Floor &amp; First Floor, Hem Ch...</td>
      <td>7409885888</td>
      <td>https://goo.gl/maps/8efV881ALyzn5Mqe8</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Delhi</td>
      <td>Breakfast Store, Kiosk, Delivery</td>
      <td>30/4, 30/4A, Double Storey. Ashok Nagar, Tehar...</td>
      <td>8376902651</td>
      <td>https://goo.gl/maps/B9xkagtgRkGfUrqa6</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Dehradun</td>
      <td>Delivery, Birthday Store</td>
      <td>3rd Astley Hall, Rajpur Road, Dehradun-248001</td>
      <td>9719401785</td>
      <td>https://goo.gl/maps/WWiecYyUFS7RXbee6</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Delhi</td>
      <td>Breakfast Store, Delivery</td>
      <td>42, Janpath, Tolstoy Marg, New Delhi- 110001</td>
      <td>9711882133</td>
      <td>https://goo.gl/maps/XWXNNJjDfPjTTjNF8</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Delhi</td>
      <td>Breakfast Store, Extended Hours, Delivery</td>
      <td>47, Basant Lok, Community Centre, Vasant Vihar...</td>
      <td>9899795811</td>
      <td>https://goo.gl/maps/QBWwF3jEDFjussUH6</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Delhi</td>
      <td>Kiosk, Delivery</td>
      <td>5/76, West Ajmal Khan, Padam Singh, Karol Bagh...</td>
      <td>9899795804</td>
      <td>https://goo.gl/maps/45tmApQVQZ1b7jMB6</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Delhi</td>
      <td>Delivery</td>
      <td>6A-6B, Sonia Cinema Complex, Vikas Puri, New D...</td>
      <td>9899795810</td>
      <td>https://goo.gl/maps/6e4y761c2UtqFP9z7</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Noida</td>
      <td>Breakfast Store, Drive-Thru, Delivery</td>
      <td>A-79 A, Sector- 16, Savoy Suits, Noida-201301</td>
      <td>9899795813</td>
      <td>https://goo.gl/maps/H41Eoha6Z2gHxrPQ8</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Bhiwadi</td>
      <td>Breakfast Store, Drive-Thru, Delivery, Birthda...</td>
      <td>Aashiana Aagan Housing Society, Village Saidpu...</td>
      <td>8875022255</td>
      <td>https://goo.gl/maps/1m717zsMDVCnkPA86</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Noida</td>
      <td>Breakfast Store, Delivery</td>
      <td>Advant IT Park, Plot No.7. Sector-142, Noida- ...</td>
      <td>7838073348</td>
      <td>https://goo.gl/maps/uxmjCUsxB7qYsThD7</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Noida</td>
      <td>Kiosk, Delivery, Birthday Store</td>
      <td>AF 63/64 &amp; GF 60, Ansal Plaza, Sector Institut...</td>
      <td>9873162934</td>
      <td>https://goo.gl/maps/otNdQnbJWcoQWXZa9</td>
      <td>Open</td>
      <td>Open</td>
    </tr>
  </tbody>
</table>
</div>



Curiously, we can see that McDonald's outlets in North and East are focussed around Delhi and surrounding areas.


```python
mcd_stores['city'].value_counts()[mcd_stores['city'].value_counts()> 2]
```




    Delhi         38
    Gurugram      10
    Noida          7
    Chandigarh     5
    Ghaziabad      4
    Faridabad      3
    Jaipur         3
    Lucknow        3
    Jalandhar      3
    Name: city, dtype: int64



# Links

[Github repo for Data and IPython notebook](https://github.com/indianama/mcdonalds_stores)
