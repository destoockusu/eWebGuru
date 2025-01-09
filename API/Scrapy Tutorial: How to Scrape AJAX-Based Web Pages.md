
# Scrapy Tutorial: How to Scrape AJAX-Based Web Pages

## Introduction

It’s been a while since I last updated this tutorial series due to work commitments. Now, with some free time after switching jobs, I’m excited to dive back in and share more insights. In previous tutorials, we covered how to scrape traditional web pages. Today, we’ll explore how to scrape websites that use **AJAX for asynchronous loading**.

---

## Stop wasting time on proxies and CAPTCHAs!  
ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more.  
👉 [**Start your free trial today!**](https://bit.ly/Scraperapi)

---

## Tools and Environment

To follow along, make sure you have the following setup:

1. **Language**: Python 2.7 (can be updated to Python 3.x for compatibility)
2. **IDE**: PyCharm
3. **Browser**: Chrome
4. **Web Scraping Framework**: Scrapy 1.3.3 (or latest version)

---

## What is AJAX?

> **AJAX** stands for "Asynchronous JavaScript and XML."  
> It is a web development technique used to create dynamic and interactive web applications.

### Key Features of AJAX:
- Enables partial updates to web pages without reloading the entire page.
- Works in the background by exchanging small amounts of data with the server.
- Updates only specific parts of a webpage, making it faster and more dynamic.

---

## Two Essential Chrome Extensions

### 1. **Toggle JavaScript**
This extension helps identify which parts of a webpage load dynamically via AJAX. It allows you to disable JavaScript to test which content is rendered by AJAX.

- **Download link**: [Toggle JavaScript](https://chrome.google.com/webstore/detail/toggle-javascript/cidlcjdalomndpeagkjpnefhljffbnlo)  
  *(If blocked, search for offline download options for Chrome extensions.)*

### 2. **JSON-handle**
This extension formats JSON data, making it easier to read and analyze AJAX responses.

- **Download link**: [JSON-handle](https://chrome.google.com/webstore/detail/json-handle/iahnhfdhidomcpggpaimmmahffihkfnj)

---

## Identifying AJAX Content

For this tutorial, let’s use **Douban Movie Rankings** as an example. Visit the [Douban Action Movies Ranking Page](https://movie.douban.com/typerank?type_name=%E5%8A%A8%E4%BD%9C&type=5&interval_id=100:90&action=).

1. Open the page and scroll down. Notice how new movie information appears dynamically as you scroll. This indicates the page uses **AJAX for asynchronous loading**.
   
2. To confirm, right-click and select "View Page Source." Search for any displayed content (e.g., a movie title). If the content isn’t present in the source, it’s being loaded dynamically via AJAX.

### Using **Toggle JavaScript**
1. Install and enable the Toggle JavaScript extension.
2. Click the extension icon to disable JavaScript.  
   Refresh the page, and you’ll notice that the dynamically loaded movie information disappears.  
   This confirms that the data is loaded asynchronously using AJAX.

---

## How to Scrape AJAX-Loaded Pages

For AJAX-based pages, there are two common methods to scrape data:

1. **Intercept AJAX Requests**
   - Identify the API or endpoint that serves the dynamic content.
   - Extract data directly from the API in a clean format (usually JSON).

2. **Use a Headless Browser**
   - Tools like PhantomJS or Selenium simulate browser actions, executing JavaScript before scraping the fully rendered content.

### Why Use the First Method?
Intercepting AJAX requests is generally faster and more efficient than using headless browsers. Most AJAX responses are in JSON format, which is easy to parse. In this tutorial, we’ll focus on the first method.

---

## Finding the AJAX Request URL

1. Open the **Douban Movie Rankings** page in Chrome.
2. Press `F12` to open Developer Tools and switch to the **Network** tab.
3. Perform a scroll action to trigger a data load. Look for a network request returning a JSON response.

![Network Tab Example](https://woodenrobot.me/images/Scrapy%E7%88%AC%E8%99%AB%E6%A1%86%E6%9E%B6%E6%95%99%E7%A8%8B%EF%BC%88%E5%9B%9B%EF%BC%89--%20%E6%8A%93%E5%8F%96AJAX%E5%BC%82%E6%AD%A5%E5%8A%A0%E8%BD%BD%E7%BD%91%E9%A1%B55.png)

4. Filter for requests with **JSON** responses. Right-click on the request URL and open it in a new tab to view the JSON data.  
   Using the **JSON-handle** extension, you’ll see the JSON response in a clean, readable format.

### Key Observations:
- The API endpoint uses a GET request.
- The `start` parameter in the URL changes with each request, controlling pagination (e.g., `start=0`, `start=20`, etc.).

---

## Scrapy Spider Code Example

Here’s how to scrape data from the AJAX API using Scrapy:

```python
import scrapy
import json

class DoubanMovieSpider(scrapy.Spider):
    name = 'douban_movies'
    allowed_domains = ['douban.com']
    start_urls = ['https://movie.douban.com/j/new_search_subjects?sort=T&range=0,10&tags=&start=0']

    def parse(self, response):
        # Load JSON data from the AJAX response
        data = json.loads(response.text)
        movies = data.get('data', [])

        for movie in movies:
            yield {
                'title': movie.get('title'),
                'rating': movie.get('rate'),
                'url': movie.get('url')
            }

        # Pagination: Increment the `start` parameter to load the next page
        current_start = int(response.url.split('start=')[-1])
        next_start = current_start + 20

        if next_start < 100:  # Scrape up to 100 results
            next_page = f'https://movie.douban.com/j/new_search_subjects?sort=T&range=0,10&tags=&start={next_start}'
            yield scrapy.Request(next_page, callback=self.parse)
```

### Key Points:
- The spider extracts movie titles, ratings, and URLs from the JSON response.
- Pagination is handled by incrementing the `start` parameter in the URL.

---

## Conclusion

This tutorial focused on understanding and scraping AJAX-based web pages. While we used Douban as an example, the approach can be applied to any AJAX-loaded website. 

Instead of just scraping for content, this tutorial aimed to teach you the methodology to solve problems and approach dynamic websites effectively. With these skills, you can confidently tackle even more complex scraping challenges. Happy scraping! 😊
