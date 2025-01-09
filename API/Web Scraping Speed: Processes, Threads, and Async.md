
# Web Scraping Speed: Processes, Threads, and Async

![Web Scraping Speed](https://scrapfly.io/blog/content/images/web-scraping-speed_banner_light.svg)

Speeding up web scrapers can feel like a daunting task, especially when performance bottlenecks hinder your results. In this tutorial, we’ll explore the biggest speed challenges in web scraping and how to overcome them.

While we’ll focus on **Python**, the concepts covered here can be applied to almost any programming language or web scraping toolkit. Let’s dive into optimizing for **CPU-bound** and **IO-bound tasks** using **processes**, **threads**, and **asyncio** for significant performance improvements.

---

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## The Bottlenecks: CPU-Bound vs IO-Bound Tasks

Web scraping primarily encounters two types of bottlenecks:

1. **IO-Bound Tasks**: These occur when the program interacts with external systems (e.g., making HTTP requests or saving data to a database).  
   Example:
   ```python
   # HTTP requests are IO-bound
   import requests
   from time import time
   
   _start = time()
   response = requests.get("https://scrapfly.io/blog/")
   print(f"Request finished in: {time() - _start:.2f} seconds")
   ```

2. **CPU-Bound Tasks**: These involve computationally intensive tasks, such as parsing HTML or processing JSON data.  
   Example:
   ```python
   # Parsing HTML is CPU-bound
   from parsel import Selector
   
   selector = Selector(html)
   article = "\n".join(selector.css(".post-section p").getall())
   ```

In web scraping, **IO-blocks** make up the majority of performance hits due to the high volume of external connections. However, both IO and CPU bottlenecks need optimization to achieve faster scraping speeds.

---

## Scaling Options in Python

To overcome these bottlenecks, Python offers multiple scaling technologies: **Processes**, **Threads**, and **Asyncio**.

### Understanding Processes and Threads

![Processes and Threads](https://scrapfly.io/blog/content/images/web-scraping-speed_processes-and-threads.svg)

- **Process**: An independent memory space where the program runs, with its own Python interpreter and memory.
- **Threads**: A process can contain multiple threads, which share memory but cannot run in parallel due to Python’s Global Interpreter Lock (GIL).

### Key Takeaways:
- Use **multiple processes** for **CPU-bound tasks** since they can run in parallel across CPU cores.
- Use **threads** for **IO-bound tasks** as they can take turns waiting for IO tasks to complete.
- For highly IO-intensive tasks like web scraping, **asyncio** is often the best choice due to its lightweight, single-threaded approach.

---

## Async Requests: Optimizing IO Performance

In traditional synchronous scraping, each HTTP request blocks the program while waiting for a response. For example:

```python
import requests
from time import time

_start = time()
for _ in range(50):
    requests.get("http://httpbin.org/delay/1")
print(f"Finished in: {time() - _start:.2f} seconds")
# Finished in: 52.21 seconds
```

By switching to an **asynchronous HTTP client**, we can make all requests simultaneously and wait for them together:

```python
import httpx
import asyncio
from time import time

async def main():
    _start = time()
    async with httpx.AsyncClient() as client:
        tasks = [client.get("http://httpbin.org/delay/1") for _ in range(50)]
        responses = await asyncio.gather(*tasks)
    print(f"Finished in: {time() - _start:.2f} seconds")

asyncio.run(main())
# Finished in: 3.55 seconds
```

This approach reduces the total time spent on IO-blocking tasks, offering a **10x or more speed boost** compared to synchronous programs.

---

### Combining Async and Synchronous Code

Not all libraries support asyncio. For legacy libraries, you can use `asyncio.to_thread()` to run synchronous code in a non-blocking thread:

```python
import asyncio
import requests

def sync_function(url):
    response = requests.get(url)
    return response.text

async def main():
    result = await asyncio.to_thread(sync_function, "http://httpbin.org/delay/1")
    print(result)

asyncio.run(main())
```

---

## Multi-Processing: Optimizing CPU Performance

While asyncio handles IO-bound tasks efficiently, parsing data still relies on CPU cores. By using multiple processes, we can parallelize CPU-bound tasks and achieve significant speedups:

```python
from concurrent.futures import ProcessPoolExecutor
from time import time

def heavy_task(n):
    # Simulate a CPU-heavy task
    return sum(i**2 for i in range(n))

def multi_process():
    start = time()
    with ProcessPoolExecutor() as executor:
        results = list(executor.map(heavy_task, [10**6]*12))
    print(f"Finished in: {time() - start:.2f} seconds")

multi_process()
```

### Example Results:
- **Single Process**: ~30 seconds  
- **Multi-Process (12 cores)**: ~2.5 seconds  

---

## Combining Asyncio and Multi-Processing

To fully optimize web scraping, combine asyncio for IO-bound tasks and multiprocessing for CPU-bound tasks. For example:

```python
import asyncio
from concurrent.futures import ProcessPoolExecutor
from time import time

async def fetch(url):
    async with httpx.AsyncClient() as client:
        response = await client.get(url)
        return response.text

def process_data(data):
    # Simulate a CPU-heavy data processing task
    return len(data)

async def main():
    urls = ["http://httpbin.org/delay/1"] * 100
    _start = time()

    # Fetch data asynchronously
    async with httpx.AsyncClient() as client:
        tasks = [client.get(url) for url in urls]
        responses = await asyncio.gather(*tasks)

    # Process data using multiprocessing
    with ProcessPoolExecutor() as executor:
        results = list(executor.map(process_data, [response.text for response in responses]))
    
    print(f"Finished in: {time() - _start:.2f} seconds")

asyncio.run(main())
```

This approach ensures maximum efficiency for both IO and CPU tasks.

---

## ScrapFly: Simplifying Concurrency

Handling concurrency manually can be complex, especially with added challenges like IP blocks or CAPTCHAs. **ScrapFly** simplifies this process with its built-in **concurrent_scrape()** function:

```python
from scrapfly import ScrapflyClient, ScrapeConfig

async def main():
    client = ScrapflyClient(key="YOUR-SCRAPFLY-KEY")
    tasks = [ScrapeConfig("http://httpbin.org/delay/1") for _ in range(20)]
    results = await client.concurrent_scrape(tasks)
    print(results)

asyncio.run(main())
```

ScrapFly takes care of scaling, proxy rotation, and error handling, making it an excellent choice for large-scale web scraping.

---

## Summary

In this guide, we explored how to speed up web scraping by optimizing for both IO and CPU bottlenecks:

- **Asyncio** for IO-bound tasks like HTTP requests.
- **Multi-Processing** for CPU-bound tasks like data parsing.
- Combining these techniques to achieve maximum performance.

Using these tools, you can scale your scrapers from dozens to thousands of times faster with minimal development overhead. For even greater efficiency, consider services like [ScraperAPI](https://bit.ly/Scraperapi) or ScrapFly to handle scaling and blocking challenges.

Happy scraping!
