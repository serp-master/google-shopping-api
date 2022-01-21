<h1 align="center">
Google Shopping API
</h1> 

## Table of Contents 

- [Intro](#intro)
- [Google Shopping API: Why Should You Use It](#google-shopping-api-why-should-you-use-it)
- [Getting Started With Google Shopping API](#getting-started-with-google-shopping-api)
- [Installation](#installation)
- [Features](#features) 
- [Output](#output)
- [How to Use](#how-to-use)
  - [Example 1: Scraping Google Shopping API for Product Data](#example-1-scraping-google-shopping-api-for-product-data)
  - [Example 2: Scraping for Product Data With Free Delivery](#example-2-scraping-for-product-data-with-free-delivery)
- [Conclusion](#conclusion)

## Intro 

Google Shopping API is a tool that extracts product and vendor data from Google Shopping sites in any country or region. 

Google Shopping Search API is a valuable resource for accessing numerous sources of information on products. They can be very useful if you harness them and format them for the intended purpose. This tutorial will demonstrate how to scrape Google Shopping using the Google Product API with a custom scraping tool, specifically scraping Google merchant API listings.

The custom scraper tool is a search API for scraping Google Search Engine Result Pages (SERPs) results. The API will allow users to scrape Google for product details such as price, descriptions, reviews, titles, price comparisons to other sites, and more.

In this article, you’ll learn to scrape Google Shopping API for product data. You can easily scrape information about product listings, sellers, and prices from Google Shopping listings.

## Google Shopping API: Why Should You Use It

Understanding how Google Shopping works is the first step in optimizing your Google Shopping campaign. For most e-Commerce companies and marketing campaigns, it’s crucial to understand the competitive market price and core keywords for better listings. This is because Google Shopping has an abundantly high conversion rate.

Let’s take a look at some of the reasons why you’ll need a Google Shopping API:

- Improve CTR: In some cases, you may not know how to design an effective marketing campaign. With that in mind, you can start by watching how others—especially your competitors— handle Google Shopping.

- Offering competitive prices: Google Shopping is more likely to show your products for a particular search if your price is competitive. This impression will further increase your CTR. There is, however, a limit to how low you can set your price. Always be aware of what the competition is charging so you can stay competitive.

- Observe promotional campaigns: You're likely to see your conversion rate drop if other companies run promotional campaigns. Promotional texts tend to drive higher conversions. In that regard, seasonal promotions such as “Winter Blowout Sale” can help get more customers. You can also offer “free shipping on all orders” to attract more customers.
 
In the next section, we’ll take a look at how to set up a Google Shopping scraper. 

## Getting Started With Google Shopping API
 
In this article, we’ll be using a custom Google Shopping scraper. This is an open-source Python package designed for scraping and parsing Google Shopping data. Be sure to create a folder for the Google Shopping scraper. 

## Installation

In the folder, you’ll need to install the Beautiful Soup package. It is a Python library that helps programmers scrape data from the web. It extends a standard HTML or XML parser by providing Pythonic methods for iterating, searching, and altering the parse tree. 

Run the following command in your terminal to install Beautiful Soup:

```pip install beautifulsoup4``` 

### Imports

To get started, you’ll import the python package into your script. In addition to this, you’ll need to save the Google Shopping results into a JSON format.  Let’s take a look at the import code for this project: 

```
import requests, json
from bs4 import BeautifulSoup
from outputs import output_json
import re
```
### Input

A typical input format will take the following form:
 
```
params = {
   "q": "lenovo thinkpad",
   "hl": "en",
   'gl': 'us',
   'tbm': 'shop'
}
```

The parameter ‘q’ represents the keyword you want to search for. However, to scrape Google shopping information, you’ll need to set the tbm parameter to “shop”.
 
 
## Features
 

| Parameter | What it does |
| ----------- | ----------- |
| URL | The search engine endpoint you’re attempting to access. |
| TBM | Search segments/sections such as images, books, shopping, etc. |
| Q | Search keyword |
| gl | Location | 
| hl | Language | 

## Output

```
{
            "title": "Lenovo ThinkPad T495 20NJ0004US 14\" Touchscreen Ryzen 5 Pro 3500u 8GB 256GB SSD",
            "link": "https://www.google.com/shopping/product/4271910433351955908?q=lenovo+thinkpad&hl=en&gl=us&prds=eto:6287799078056332790_0&sa=X&ved=0ahUKEwjQ5-rMqLj1AhXCds0KHRRPCgwQ8wII4BE",
            "source": "Chicago Computer Supply",
            "price": "$976.99",
            "rating": "3.9",
            "reviews": "14",
            "delivery": "Free delivery by Thu, Jan 20"
        }
```

## How to use 

This tutorial will also require saving the search results to JSON. To do this, let’s create a new python file, say outputs.py. Here, we will import JSON and create a method to help filter and save search results based on search keywords.
 
```
# outputs.py
import os
import json

def output_json(data, name):
  if 'json' not in os.listdir():
    os.mkdir('json')
  with open(f'json/{name.replace(" ","-").replace("+", "-")}.json', 'w') as f:
    json.dump(data, f, indent=4)
```

Once that is done, we’ll import this file into our main python script for scraping Google Shopping lists.
 
```
from outputs import output_json
```

### Example 1: Scraping Google Shopping API for Product Data

In this example, we’ll search through Google Shopping APIs with the keyword “lenovo thinkpad”. First, create a file and name it main.py.

To better understand the core information needed for each search query, we’ve added the main parameters into params. The params are specific search query parameters that you can modify depending on the use case or need.

Additionally, you may want to separate the inline shopping results from the other shopping results. We've included this in our example code. The following script scrapes for the top 5000 results on Google Shopping API with our supplied keyword.

```
import requests, json
from bs4 import BeautifulSoup
from outputs import output_json
import re

# CONFIGURATION
headers = {
    "User-Agent":
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.102 Safari/537.36 Edge/18.19582"
}
query = "lenovo thinkpad"
params = {"q": query, "hl": "en", 'gl': 'us', 'tbm': 'shop'}
freeDelivery = False
url = 'https://www.google.com/search'

# REQUEST
response = requests.get(url,
                        params=params,
                        headers=headers)

soup = BeautifulSoup(response.text, 'lxml')

# PARSING RESPONSE
shopping_data_dict = {}
inline_results = []
shopping_results = []

for inline_result in soup.select('.sh-np__click-target'):
    inline_shopping_title = inline_result.select_one('.sh-np__product-title').text
    inline_shopping_link = f"https://google.com{inline_result['href']}"
    inline_shopping_price = inline_result.select_one('b').text
    inline_shopping_source = inline_result.select_one('.E5ocAb').text.strip()
    try:
      inline_shopping_shipping = inline_result.select_one('.U6puSd').text.strip()
    except:
      inline_shopping_shipping = None
      

    inline_results.append({
        'title': inline_shopping_title,
        'link': inline_shopping_link,
        'price': inline_shopping_price,
        'source': inline_shopping_source,
        'shipping': inline_shopping_shipping
    })

shopping_data_dict.update({"inline_shopping_results": inline_results})

for shopping_result in soup.select('.sh-dgr__content'):
    title = shopping_result.select_one('.Lq5OHe.eaGTj h4').text
    product_link = f"https://www.google.com{shopping_result.select_one('.Lq5OHe.eaGTj')['href']}"
    source = shopping_result.select_one('.IuHnof').text
    price = shopping_result.select_one('span.kHxwFf span').text

    try:
        rating = shopping_result.select_one('.Rsc7Yb').text
    except:
        rating = None

    try:
        reviews = shopping_result.select_one('.Rsc7Yb').next_sibling.next_sibling
    except:
        reviews = None

    try:
        delivery = shopping_result.select_one('.vEjMR').text
    except:
        delivery = None



    shopping_results.append({
    
            'title': title,
        'link': product_link,
        'source': source,
        'price': price,
        'rating': rating,
        'reviews': reviews,
        'delivery': delivery,
    })

shopping_data_dict.update({"shopping_results": shopping_results})

output_json(shopping_data_dict, f'{params["q"]}{" with free delivery" if freeDelivery else ""}')

print("Done!")
```

To run this script, navigate to the folder on your terminal and enter the following script:

```$ python main.py```

### Example 2: Scraping for Product Data With Free Delivery

In this example, the search results will only display vendors and products that offer free shipping. We’ll need to filter and search shopping results for specific listings with “free delivery” either in upper or lower case. To do this, we’ll use regex. In every product search result, there is a delivery section. 

The search keyword for this example is “lenovo thinkpad”. To get started, import the regex package into your file with the following script:

```import re```

Then, add the following modifications:
 
```
import requests, json
from bs4 import BeautifulSoup
from outputs import output_json
import re

# CONFIGURATION
headers = {
    "User-Agent":
    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.102 Safari/537.36 Edge/18.19582"
}
query = "lenovo thinkpad"
params = {"q": query, "hl": "en", 'gl': 'us', 'tbm': 'shop'}
freeDelivery = True
url ='https://www.google.com/search'

# REQUEST
response = requests.get(url,
                        params=params,
                        headers=headers)

soup = BeautifulSoup(response.text, 'lxml')

# PARSING RESPONSE
shopping_data_dict = {}
inline_results = []
shopping_results = []

for inline_result in soup.select('.sh-np__click-target'):
    inline_shopping_title = inline_result.select_one('.sh-np__product-title').text
    inline_shopping_link = f"https://google.com{inline_result['href']}"
    inline_shopping_price = inline_result.select_one('b').text
    inline_shopping_source = inline_result.select_one('.E5ocAb').text.strip()
    try:
      inline_shopping_shipping = inline_result.select_one('.U6puSd').text.strip()
    except:
      inline_shopping_shipping = None
      

    inline_results.append({
        'title': inline_shopping_title,
        'link': inline_shopping_link,

        'price': inline_shopping_price,
        'source': inline_shopping_source,
        'shipping': inline_shopping_shipping
    })

shopping_data_dict.update({"inline_shopping_results": inline_results})

for shopping_result in soup.select('.sh-dgr__content'):
    title = shopping_result.select_one('.Lq5OHe.eaGTj h4').text
    product_link = f"https://www.google.com{shopping_result.select_one('.Lq5OHe.eaGTj')['href']}"
    source = shopping_result.select_one('.IuHnof').text
    price = shopping_result.select_one('span.kHxwFf span').text

    try:
        rating = shopping_result.select_one('.Rsc7Yb').text
    except:
        rating = None

    try:
        reviews = shopping_result.select_one('.Rsc7Yb').next_sibling.next_sibling
    except:
        reviews = None

    try:
        delivery = shopping_result.select_one('.vEjMR').text
    except:
        delivery = None



    shopping_results.append({
        'title': title,
        'link': product_link,
        'source': source,
        'price': price,
        'rating': rating,
        'reviews': reviews,
        'delivery': delivery,
    })

shopping_data_dict.update({"shopping_results": shopping_results})


# APPLY FILTER
if "inline_shopping_results" in shopping_data_dict:
  if freeDelivery:
    filter = re.compile(r'Free shipping', re.IGNORECASE)
    filtered_inline_shopping_results = []
    for inline_result in shopping_data_dict['inline_shopping_results']:
      if "shipping" in inline_result and (re.search(filter, inline_result['shipping']) != None):

        inline_result['Free'] = True
        filtered_inline_shopping_results.append(inline_result)
    shopping_data_dict["inline_shopping_results"] = filtered_inline_shopping_results
    

if "shopping_results" in shopping_data_dict:
  if freeDelivery:
    filter = re.compile(r'Free Delivery', re.IGNORECASE)
    filtered_shopping_results = []
    for shopping_result in shopping_data_dict['shopping_results']:
      if "delivery" in shopping_result and (re.search(filter, shopping_result['delivery']) != None):
        shopping_result['Free'] = True
        filtered_shopping_results.append(shopping_result)
    shopping_data_dict['shopping_results'] = filtered_shopping_results


output_json(shopping_data_dict, f'{params["q"]}{" with free delivery" if freeDelivery else ""}')
```

When freeDelivery is set to false, the result will be saved into the path: json/lenovo-thinkpad.json. If not, the result will be stored in json/lenovo-thinkpad-with-free-delivery.json. 

Note: This is only an example demonstrating how to filter the product results for specific information. It may not work for all use cases where a linguistic or lexical context is required to comprehend a term.

## Conclusion

To remain competitive in any business, you need to always keep an eye on the market. That means monitoring competitor prices, promotions, and ranking. By scraping Google Shopping API for product information, you can better edge out competition and rank your products higher on SERPs. 




