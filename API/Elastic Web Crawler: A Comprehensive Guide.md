# Elastic Web Crawler: A Comprehensive Guide

The **Elastic Web Crawler** is a native Elasticsearch solution designed to programmatically discover, extract, and index searchable content from websites and knowledge bases. In this guide, we’ll explore its features, availability, and version history while comparing it with the App Search Web Crawler.

---

> **Tired of dealing with proxies and CAPTCHAs?**  
ScraperAPI simplifies web scraping with robust API support, handling millions of requests effortlessly.  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)  
Use code **SCRAPE9837861** for exclusive benefits.

---

## What Is the Elastic Web Crawler?

The Elastic Web Crawler is a lightweight, open-code web crawler that integrates directly with Elasticsearch to create search-optimized indices. These indices are perfect for building intuitive and relevant search experiences using App Search engines and the Search UI library.

### Key Features:
- Extracts content directly from websites and syncs it into Elasticsearch.
- Natively integrated with Elasticsearch indices.
- Can be managed via the Kibana UI under **Search > Content > Web Crawlers**.

To compare the Elastic Web Crawler with the **App Search Web Crawler**, refer to the [feature comparison table](https://www.elastic.co/guide/en/enterprise-search/current/crawler.html#crawler-feature-comparison).

---

## Availability and Prerequisites

The Elastic Web Crawler was introduced in version **8.4.0** and is available for:
1. **Elastic Cloud Deployments**: Requires Elasticsearch, Kibana, and Enterprise Search services. Ensure **at least 4 GB RAM per zone** for Enterprise Search.
   - For more details, view the [deployment infrastructure requirements](https://www.elastic.co/guide/en/enterprise-search/current/deployment.html#deployment-infrastructure-requirements).

2. **Self-Managed Deployments**: Available with a valid [Elastic subscription](https://www.elastic.co/subscriptions). Check the "Elastic Search" section for specific requirements.

### Elastic Cloud IPs
If using the web crawler on Elastic Cloud, outbound data transfer (egress) may require adding static IP ranges to your firewall rules.  
Refer to the [Elastic Cloud documentation](https://www.elastic.co/guide/en/cloud/current/ec-static-ips.html) for the latest IP addresses.

---

## Using the Elastic Open Web Crawler

For a self-managed option, check out the **Elastic Open Web Crawler** (beta). It’s lightweight and doesn’t require running Enterprise Search or Kibana services.  
Start by visiting the [GitHub repository](https://github.com/elastic/crawler#readme) and follow the documentation for installation and setup.  
For further insights, read the [Elastic Open Crawler beta release blog](https://www.elastic.co/search-labs/blog/elastic-open-crawler-beta-release).

---

## Version History

### Significant Changes:
- **8.5.0**: The default ingest pipeline for newly created indices was updated to use the `body` field for binary content instead of the `body_content` field. This can affect search results if your search experiences rely on the `body_content` field.  
  **Workarounds:**
  - Update your search experiences to include the `body` field.
  - Customize the pipeline to copy content between `body` and `body_content`.
  - Verify boosts and weights for App Search engines to include the `body` field where necessary.

- **8.4.0**: The web crawler reached General Availability (GA).

---

## How to Use the Elastic Web Crawler

1. **Discover Content**: Use the crawler to extract data programmatically from websites and create Elasticsearch indices.
2. **Build Search Experiences**: Develop intuitive search interfaces using App Search engines and the Search UI library.
3. **Monitor and Manage**: Leverage the Kibana UI to create, monitor, and manage crawlers effortlessly.

---

## Why Choose Elastic Web Crawler?

The Elastic Web Crawler stands out for its native integration with Elasticsearch, making it an ideal choice for developers aiming to build scalable and efficient search systems. Whether self-managed or on Elastic Cloud, it provides flexibility and reliability for enterprise-grade search solutions.

> **Optimize your scraping workflows with ScraperAPI!**  
No more worries about IP bans or CAPTCHAs—ScraperAPI handles it all.  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)  
Promo code: **SCRAPE9837861**

