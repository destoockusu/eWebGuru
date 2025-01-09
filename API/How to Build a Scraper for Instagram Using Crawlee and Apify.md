
# How to Build a Scraper for Instagram Using Crawlee and Apify

Crawling public data from platforms like Instagram can open up a world of possibilities, from gathering user insights to analyzing trends. In this guide, we'll explore how to scrape Instagram profile data using **Crawlee**, an open-source web crawling library, and run it on **Apify**, a platform that simplifies crawler deployment and management.

---

> **Stop wasting time on proxies and CAPTCHAs!** ScraperAPI’s simple API handles millions of web scraping requests effortlessly. Get structured data from Instagram, Amazon, Google, and more.  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)  
Use promo code: **SCRAPE9837861**

---

## Why Use Crawlee and Apify?

- **Crawlee**: A powerful open-source library that simplifies crawling and scraping tasks, allowing you to crawl thousands of pages efficiently.
- **Apify**: A platform for running, scheduling, and managing crawlers. It also provides built-in proxy management to avoid detection and blocking.

While Apify is not mandatory for crawling, it provides a managed environment that reduces the complexity of deploying crawlers, especially for large-scale scraping.

---

## Scraping Public Instagram Data

This scraper targets **publicly available Instagram profile data**, including:
- Profile Name
- Profile Picture URL
- Bio
- Number of Posts
- Follower Count
- Following Count

### Tools Used
- **Playwright**: Handles browser automation for rendering Instagram pages.
- **Crawlee**: Provides an easy interface for crawling.
- **Apify SDK**: Enables running the scraper on Apify’s cloud platform.

---

## Setting Up the Instagram Scraper

### 1. Initialize the Crawler

Here’s the basic setup for the crawler:

```javascript
import { Actor } from 'apify';
import { PlaywrightCrawler } from 'crawlee';
import { router } from './routes.js';
import cookies from './cookies.json' with { type: "json" };

// Initialize Apify
await Actor.init();

const usernames = ["humansofny"];
const profileUrls = usernames.map((username) => `https://www.instagram.com/${username}/`);

const startUrls = await Actor.getInput() ?? profileUrls;

const proxyConfiguration = await Actor.createProxyConfiguration();

const crawler = new PlaywrightCrawler({
    proxyConfiguration,
    requestHandler: router,
    preNavigationHooks: [
        async (crawlingContext) => {
            crawlingContext.page.context().addCookies(cookies);
        },
    ],
});

await crawler.run(startUrls);
await Actor.exit();
```

### 2. Define the Router Logic

The router determines how to extract data from Instagram pages. This involves locating and parsing the desired elements on the profile page.

```javascript
import { createPlaywrightRouter } from "crawlee";

export const router = createPlaywrightRouter();

const getElementText = async (page, regex) => {
    const element = await page.getByText(regex);
    return element ? await element.textContent() : null;
};

router.addDefaultHandler(async ({ request, page, pushData }) => {
    const username = request.url.split('/').filter(Boolean).pop();

    const followersText = await getElementText(page, /[0-9,.mMkK]+ followers/);
    const followersCount = followersText ? followersText.split(" ")[0] : 0;

    const followingText = await getElementText(page, /[0-9,.mMkK]+ following/);
    const followingCount = followingText ? followingText.split(" ")[0] : 0;

    const postsText = await getElementText(page, /[0-9,.mMkK]+ posts?/);
    const postsCount = postsText ? postsText.split(" ")[0] : 0;

    const profilePicture = await page.getAttribute(`img[alt="${username}'s profile picture"]`, 'src');

    await pushData({
        username,
        followersCount,
        followingCount,
        postsCount,
        profilePicture,
    });
});
```

---

## Output Example

When executed, the crawler outputs profile data as a JSON object. Below is an example:

```json
{
    "url": "https://www.instagram.com/humansofny/",
    "username": "humansofny",
    "profilePicture": "https://instagram.profile.pic.url.jpg",
    "postsCount": "5,713",
    "followersCount": "12.8M",
    "followsCount": "413"
}
```

---

## Avoid Getting Blocked

Scraping Instagram comes with the risk of being detected as a bot and getting your IP banned. To minimize this risk:

### Use Proxies
- **Residential Proxies**: Simulate real-user traffic but are expensive.
- **Datacenter Proxies**: Cheaper but easier to detect.

Crawlee supports proxy configurations out of the box. When using Apify, proxy management is handled automatically.

```javascript
const proxyConfiguration = await Actor.createProxyConfiguration();

const crawler = new PlaywrightCrawler({
    proxyConfiguration,
    requestHandler: router,
});
```

---

## Advanced Features for Instagram Scraping

### Adding Cookies
Some Instagram content requires authentication. You can include cookies in your scraper to bypass certain restrictions.

### Handling Dynamic Changes
Instead of relying on fixed element IDs or classes, use flexible regex patterns or parent-child relationships to locate elements. This makes the scraper more resilient to changes in Instagram’s front-end code.

---

## Conclusion

Building an Instagram scraper with Crawlee and Apify provides a robust and scalable way to gather public profile data. By leveraging tools like Playwright for browser automation and proxies for avoiding bans, you can collect valuable insights from Instagram efficiently and responsibly.

---

> **Ready to scrape smarter?** With ScraperAPI, you can handle millions of scraping requests seamlessly.  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)  
Use promo code: **SCRAPE9837861**
