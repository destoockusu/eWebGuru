
# Advanced Guide to API-Based Web Crawling

Web crawling is an essential technique for extracting vast amounts of information from websites. This guide focuses on building a robust API-based crawler using an example of scraping financial news from a well-known website. We’ll walk through step-by-step processes, from identifying APIs to running and optimizing the crawler, and even discuss database deduplication strategies.

---

> **Stop wasting time on proxies and CAPTCHAs!** ScraperAPI's simple API handles millions of web scraping requests, allowing you to focus on the data. Get structured data from Amazon, Google, Walmart, and more.  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Step 1: Identifying the API

The first step in building an API-based crawler is finding the correct API endpoint to extract data. Use developer tools (F12 → Network → All) to inspect the API traffic when interacting with the website.

**Example API**:  
`https://api.jinse.com/v4/information/list?catelogue_key=news&limit=23&information_id=56630&flag=down&version=9.9.9`

### Key Parameters Explained:
- **`limit=23`**: Number of records returned per API call (23 in this case).
- **`information_id=56630`**: Fetches data greater or smaller than the given ID, depending on the direction.
- **`flag=down`**: Indicates fetching older records (IDs smaller than the given ID).

### Testing the API:
Use **Postman** or similar tools to test the API. For example:

```plaintext
https://api.jinse.com/v4/information/list?catelogue_key=news&limit=2&information_id=0&flag=down&version=9.9.9
```

This request returns the latest two records.

**Sample JSON Response**:
```json
{
  "news": 2,
  "count": 2,
  "list": [
    {
      "id": 58300,
      "title": "Crossing Bull and Bear Markets: How IT Services Help Cryptocurrency",
      "summary": "How to attract new users and funds amid liquidity challenges.",
      "published_at": 1532855806,
      "read_number": 27064,
      "source": "Jinse Finance",
      "topic_url": "https://m.jinse.com/news/blockchain/219916.html"
    },
    {
      "id": 58325,
      "title": "Blockchain Perspectives: New Opportunities for Technology",
      "summary": "Blockchain discussions have sparked global interest.",
      "published_at": 1532853425,
      "read_number": 33453,
      "source": "Sina Finance",
      "topic_url": "https://m.jinse.com/blockchain/219934.html"
    }
  ]
}
```

---

## Step 2: Scheduling and Automating Crawling

To efficiently manage data crawling, use scheduled tasks to run the crawler periodically. This ensures continuous data collection without manual intervention.

### Sample Code for a Scheduled Crawler:
```java
@Slf4j
@Service
public class CrawlerService {

    private static final int PAGE_SIZE = 15; // Number of records per API call
    private long bottomId = 0; // Used to fetch older data

    @Autowired
    private DataStorageService dataStorageService;

    @Scheduled(fixedRate = 3600000) // Runs every hour
    public void startCrawling() {
        try {
            fetchNewsData();
        } catch (Exception e) {
            log.error("Error during crawling", e);
        }
    }

    private void fetchNewsData() throws IOException {
        String requestUrl = String.format(
            "https://api.jinse.com/v4/information/list?catelogue_key=news&limit=%d&information_id=%d&flag=down&version=9.9.9",
            PAGE_SIZE, bottomId
        );

        Response response = OkHttpClient.singleton().newCall(new Request.Builder()
            .url(requestUrl)
            .get()
            .build()).execute();

        if (response.isSuccessful()) {
            String responseBody = response.body().string();
            processResponse(responseBody);
        }
    }

    private void processResponse(String json) {
        JinSePressResult result = JsonUtils.fromJson(json, JinSePressResult.class);
        bottomId = result.getBottomId(); // Update bottomId for next iteration

        for (NewsItem news : result.getList()) {
            dataStorageService.save(news); // Save news to database
        }
    }
}
```

---

## Step 3: Managing Data and Avoiding Duplication

### Deduplication Strategy:
1. **Add Unique Identifiers**: Use a combination of `information_id` or the URL as a unique identifier.
2. **Check Existing Records**: Before saving, query the database for existing records using the unique identifier.
3. **Use a Cache**: Use a temporary in-memory cache (e.g., `Map<String, Boolean>`) to avoid redundant database queries during runtime.

---

## Step 4: Parsing JSON Data Structure

When working with JSON APIs, ensure proper mapping of nested objects. For example:

### Mapping Nested JSON:
```java
@Data
@JsonIgnoreProperties(ignoreUnknown = true)
public class JinSePressResult {
    private long bottomId;
    private List<NewsItem> list;

    @Data
    @JsonIgnoreProperties(ignoreUnknown = true)
    public static class NewsItem {
        private String title;
        private String summary;
        private String topicUrl;
        private long publishedAt;
    }
}
```

**Key Considerations**:
- Ensure the JSON structure matches your object fields.
- Use libraries like Jackson or Gson for efficient deserialization.

---

## Step 5: Viewing Results

The crawler outputs the collected data in a structured format. By combining the URLs from the API, you can fetch additional details, such as full articles, using tools like **JSoup**.

**Example Output**:
- **Title**: Crossing Bull and Bear Markets
- **URL**: [https://m.jinse.com/news/blockchain/219916.html](https://m.jinse.com/news/blockchain/219916.html)
- **Published At**: 2023-01-09
- **Summary**: How to attract new users and funds amid liquidity challenges.

---

## Step 6: Database Integration for Long-Term Storage

### Best Practices for Storing Crawled Data:
- **Use Indexed Columns**: For unique identifiers like `information_id` or URLs.
- **Avoid Redundant Data**: Deduplicate records using the strategies discussed above.
- **Partition Large Datasets**: For efficient queries on large datasets.

---

## Final Thoughts

This guide covered the essentials of API-based web crawling, from identifying APIs to implementing a robust and scalable crawler. By integrating tools like **ScraperAPI**, you can bypass common challenges like proxies, CAPTCHAs, and rate limits, allowing for seamless data extraction.

---

> **Simplify Your Crawling Tasks**  
ScraperAPI handles all the complexities of web scraping, from proxy management to dynamic content rendering. Start your free trial today and unlock limitless possibilities!  
👉 [Try ScraperAPI Now](https://bit.ly/Scraperapi) (Use code **SCRAPE9837861**)

---
