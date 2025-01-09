
# Create Your First Web Scraper with ScraperAPI and Python

## Introduction

Recently, I came across a tool that simplifies many of the challenges typically encountered when scraping websites. This tool, called **ScraperAPI**, provides a simple REST API that allows you to scrape all kinds of websites (simple, JavaScript-enabled, CAPTCHA-protected, etc.) with ease. Before diving deeper, let me introduce you to **ScraperAPI**.

---

## Stop wasting time on proxies and CAPTCHAs!  
ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more.  
👉 [**Start your free trial today!**](https://bit.ly/Scraperapi)

---

## What is ScraperAPI?

According to their mission statement on their [website](https://bit.ly/Scraperapi):

> _ScraperAPI handles proxies, browsers, and CAPTCHAs, so you can get the HTML from any web page with a simple API call!_

This means that ScraperAPI takes care of all the typical challenges you face when building web scrapers, such as managing proxies, bypassing CAPTCHAs, and handling JavaScript rendering.

![ScraperAPI Overview](http://blog.adnansiddiqi.me/wp-content/uploads/2019/06/Screen-Shot-2019-06-27-at-12.25.17-PM.png)

---

## Development

ScraperAPI provides a REST API that can be used with any programming language. In this tutorial, we'll focus on using Python with the `requests` library.

### Getting Started

1. **Sign up for ScraperAPI**: First, [sign up](https://bit.ly/Scraperapi) to get your API key. ScraperAPI offers 1,000 free API calls to test their platform. For larger needs, they offer various plans from Starter to Enterprise.

2. **Install Required Libraries**: Ensure you have the Python `requests` library installed. You can install it via pip:

   ```bash
   pip install requests
   ```

### Example: Simple API Call

Below is a basic example provided in the [ScraperAPI documentation](https://bit.ly/Scraperapi):

```python
import requests

if __name__ == '__main__':
    API_KEY = '<YOUR API KEY>'
    URL_TO_SCRAPE = 'https://httpbin.org/ip'

    payload = {'api_key': API_KEY, 'url': URL_TO_SCRAPE}

    r = requests.get('http://api.scraperapi.com', params=payload, timeout=60)

    print(r.text)
```

Once you’ve registered and obtained your API key (available on the [dashboard](https://bit.ly/Scraperapi)), you can start working immediately. Running the above script will return the IP address of the proxy being used for the request.

![Proxy Example Output](http://blog.adnansiddiqi.me/wp-content/uploads/2019/06/Screen-Shot-2019-06-27-at-12.56.43-PM.png)

Each time you run the script, it returns a new IP address. Neat, right?

### Maintaining Session Consistency

In some scenarios, you might want to use the same proxy IP across multiple requests to simulate a single user browsing different parts of a website. You can do this by passing the `session_number` parameter in the `payload`.

```python
URL_TO_SCRAPE = 'https://httpbin.org/ip'
payload = {'api_key': API_KEY, 'url': URL_TO_SCRAPE, 'session_number': '123'}

r = requests.get('http://api.scraperapi.com', params=payload, timeout=60)
print(r.text)
```

This will ensure that the same proxy IP is used for the session.

![Session Proxy Output](http://blog.adnansiddiqi.me/wp-content/uploads/2019/06/Screen-Shot-2019-06-28-at-7.33.58-PM.png)

---

## Creating an OLX Scraper

For this example, let's create a simple scraper for OLX listings. The script will first extract a list of items and then scrape individual item details.

Here’s the complete code:

```python
from bs4 import BeautifulSoup
import requests
from time import sleep

API_KEY = '<YOUR API KEY>'
URL_TO_SCRAPE = 'https://www.olx.com.pk'

all_links = []
payload = {'api_key': API_KEY, 'url': URL_TO_SCRAPE, 'session_number': '123'}

r = requests.get('http://api.scraperapi.com', params=payload, timeout=60)

if r.status_code == 200:
    html = r.text.strip()
    soup = BeautifulSoup(html, 'lxml')
    links = soup.select('.EIR5N > a')

    for l in links:
        all_links.append('https://www.olx.com.pk' + l['href'])

    idx = 0

    if len(all_links) > 0:
        for link in all_links:
            sleep(5)  # Add a delay between requests
            payload = {'api_key': API_KEY, 'url': link, 'session_number': '123'}

            if idx > 1:
                break

            r = requests.get('http://api.scraperapi.com', params=payload, timeout=60)

            if r.status_code == 200:
                html = r.text.strip()
                soup = BeautifulSoup(html, 'lxml')
                price_section = soup.find('span', {'data-aut-id': 'itemPrice'})
                print(price_section.text)

            idx += 1
```

### Key Points:
- **`BeautifulSoup`** is used to parse the HTML response.
- The script extracts prices for OLX listings to demonstrate how ScraperAPI works. For a complete tutorial on scraping with Python and BeautifulSoup, check out this [guide](http://blog.adnansiddiqi.me/write-your-first-web-scraper-in-python-with-beautifulsoup/).

---

## Conclusion

This tutorial showed you how to use ScraperAPI to simplify web scraping. While you could build scrapers with traditional methods, ScraperAPI saves you time and effort by managing proxies, CAPTCHAs, and JavaScript rendering. This is particularly useful for large-scale scraping projects or scenarios requiring headless browsing, which can be cumbersome to set up on remote machines.

ScraperAPI offers affordable plans for individuals and enterprises alike. For developers and businesses, the convenience of handling everything through an API is invaluable.

---

👉 [**Start your free trial today with ScraperAPI!**](https://bit.ly/Scraperapi)  
Use the promo code **SCRAPE9837861** to unlock the full potential of ScraperAPI.

Stay tuned for more tutorials showcasing advanced features of ScraperAPI!
