# Lessons Learned from Using Selenium for Scraping on a VPS

When building side projects, automation can often be key. Recently, I explored automated web scraping using **Selenium** on a **VPS** as part of a CRON job. My prior experience with web scraping was on a local machine, but scaling up to a server environment came with its own set of challenges. This article documents my journey, the obstacles I faced, and the lessons I learned along the way.

---

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Why Selenium?

Before diving in, I researched the three most popular Python libraries for web scraping, along with their pros and cons:

1. **Selenium**:  
   - Can handle JavaScript-heavy websites.  
   - Bypasses more scraper detection systems compared to other options.  
   - Significantly slower than other libraries.

2. **BeautifulSoup + Requests**:  
   - A simple solution for scraping static websites.  
   - Requires learning HTML parsing with BeautifulSoup.  
   - Not ideal for JavaScript-heavy websites.

3. **Scrapy**:  
   - Highly efficient and feature-rich for large-scale scraping.  
   - More rigid compared to other libraries, with less flexibility.

Given that the websites I needed to scrape relied heavily on JavaScript and employed some scraper detection mechanisms, Selenium seemed like the logical choice. While its slower performance wasn’t ideal, it was acceptable for my project since the CRON job wasn’t required to run frequently.

---

## Development: Local Success, Server Troubles

Creating the prototype locally was straightforward. I combined **Selenium** with **Flask** to create the scraper, and everything worked as expected—albeit slowly. Confident in the results, I moved the scraper to a VPS to set up scheduled scraping tasks.

And that’s when things fell apart.

### Problem 1: No GUI on VPS
Unlike local machines, VPS environments don’t have a graphical interface, which meant I couldn’t directly run Selenium with a visible browser. After some research, I found two solutions:

1. **Using a virtual display**: Configure the VPS to run Selenium in a virtual display using tools like [Xvfb](https://stackoverflow.com/questions/6183276/how-do-i-run-selenium-in-xvfb).  
2. **Running Selenium in headless mode**: Use a [headless browser](https://stackoverflow.com/questions/55544648/how-to-run-selenium-script-on-server) to eliminate the need for a GUI.

Initially, I opted for the virtual display approach, but persistent errors forced me to switch to the headless solution. While this resolved the GUI issue, new problems emerged.

---

### Problem 2: Frequent Crashes
The program frequently crashed during scraping tasks. To mitigate this, I implemented the following fixes:

- Added **WebDriverWait** to ensure the browser waited for elements to fully load before interacting with them.
- Wrapped risky sections of code in `try-except` blocks to handle unexpected errors gracefully.

### Problem 3: Driver Disconnections
ChromeDriver, which I initially used, proved highly unstable and frequently disconnected from the browser. Switching to GeckoDriver reduced the frequency of disconnections but didn’t eliminate the problem entirely. My workaround was to run the scraper in batches and restart the driver whenever it disconnected.

---

## Lessons Learned

After days of debugging and patching, I realized that scraping with Selenium on a VPS comes with significant challenges. Here are my key takeaways:

1. **Combining Methods is Crucial**  
   I ended up using **BeautifulSoup + Requests** for simpler websites and Selenium only for JavaScript-heavy ones. This hybrid approach saved time and reduced complexity.

2. **Scheduled Scraping is Complex**  
   Running CRON jobs for scraping on a VPS introduces additional hurdles, such as stability issues, error handling, and driver configurations.

3. **Proxies and Anti-Detection Are a Must**  
   Scraping websites at scale often leads to IP bans. While I didn’t face this issue in my side project, configuring the scraper to appear less bot-like would have been necessary for larger projects. For future use cases, I plan to use proxies or a **web scraping service** like [ScraperAPI](https://bit.ly/Scraperapi), which manages proxies and CAPTCHAs automatically.

4. **Headless Browsers Are Not Foolproof**  
   While headless browsers solve the GUI issue, they can introduce new challenges, such as crashes and increased detection by websites.

---

## When to Avoid Scraping on a VPS

For small projects or infrequent scraping needs, the time and effort required to configure a VPS scraper might outweigh the benefits. Services like [ScraperAPI](https://bit.ly/Scraperapi) offer ready-made solutions that save you from the hassle of dealing with proxy rotations, IP bans, and scraper detection.

---

## Final Thoughts

From this experience, I learned that **scraping on a VPS is not for the faint of heart**. It’s a time-consuming process riddled with unexpected obstacles, especially when using Selenium. For large-scale or commercial projects, investing in proxies or scraping services is well worth the cost.

That said, I’ll likely avoid using Selenium on a VPS in the future unless absolutely necessary.

---

### About the Project

If you’re curious about the side project I built, you can check it out [here](https://www.lnprice.com/). It’s a simple price aggregation website for [light novels](https://en.wikipedia.org/wiki/Light_novel). I plan to share weekly blogs documenting interesting findings from my side projects, and this article is the first in the series. Stay tuned for more!

![Selenium Scraping Journey](https://res.cloudinary.com/practicaldev/image/fetch/s--PB7fUPCD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/48vv98d42g6d8ys6xrt4.png)
