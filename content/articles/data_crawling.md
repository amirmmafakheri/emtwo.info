+++
date = '2025-08-03T02:40:02Z'
draft = false
title = '🕷️ One Year of Data Engineering in the Field: A Practical Journey with a Startup Crawler Team'
[cover]
image = "https://amirmmafakheri.github.io/emtwo.info/images/4.jpg"
alt = ""
caption = ""
+++

## 1. A Brief Overview of Crawling and Its Role

Crawling refers to the automated process of navigating through web pages to extract targeted data. This data can include product details, prices, descriptions, images, user reviews, and even hidden API endpoints. The ultimate goal is to programmatically access structured data that would otherwise be visible only through manual interaction with a browser.

In real-world data pipelines, crawling is just one stage. It is typically followed by scraping (data extraction), parsing (data interpretation), and eventually storage or usage. For startups, crawlers play a vital role—especially in building platforms for price comparison, market intelligence, or competitive analysis.

## 2. Execution Context: Inside a Data-Centric Startup

The primary responsibility of our team was to design and implement crawler systems to extract store-related data from a variety of websites. The startup's architecture was fundamentally data-driven, and much of the required information was only accessible through unofficial or non-public channels.

The crawler team initially started with no specialized experience. However, through a structured onboarding program and phased task assignments, the members gradually gained autonomy in handling real-world crawling projects. Over time, the team became the core data provider for downstream analytics and product modules.

## 3. Technical Deep Dive: Real-World Scraping Engineering

### 3.1 Major Technical Challenges

Over the course of the project, we encountered three recurrent and technically complex challenges:

- **JavaScript-heavy dynamic content**, which required full page rendering  
- **Application-layer defenses**, such as rate limiting, CAPTCHA, and WAFs  
- **Frequent structural changes** in DOM layouts and API endpoints  

For each of these, targeted engineering solutions were designed and deployed.

### 3.2 Site Behavior Analysis and Reverse Engineering

The first step in any new project was understanding the target site’s structure. This involved:

- Inspecting HTTP traffic using **Burp Suite**  
- Comparing direct requests with browser behavior  
- Extracting required headers to bypass **initial filters**  
- Identifying API routes, especially in **SPA (Single Page Applications)**  

The goal was to allow the crawler to replicate or even outperform the browser in data extraction—without actually launching a browser.

### 3.3 Dealing with Limitations and Blocking

To bypass blocking mechanisms and rate limits:

- **IP rotation** was implemented using rotating proxies  
- Simulated human interactions in **Selenium** (mouse movement, scrolling, random delays)  
- A custom **Header Randomization Engine** was built to dynamically modify headers like `User-Agent`, helping bypass certain WAFs  
- User behavior flows were emulated on sensitive pages  

In some cases, tweaking header order or fine-tuning specific parameter values allowed us to bypass strict filters. These patterns were discovered through trial-and-error and differential HTTP response analysis.

### 3.4 Adapting to Structural Changes

Websites frequently change their HTML layouts. To maintain crawler stability:

- An **abstraction layer** was built to separate locators from parsing logic  
- Structural **diff tools** were implemented to automatically detect new element locations  
- An **alerting system** was deployed to notify the team of abnormal HTTP responses  

### 3.5 Resource Optimization and System Architecture

Efficient resource usage was crucial for stable long-term crawling:

- **Hybrid navigation**: `selenium` used only for initial navigation, with `requests` handling the faster processing phase  
- **Caching** for public content  
- **Modular architecture** enabling parallel task execution  
- **Fail-recovery logic** for handling network failures or site behavior changes  

### 3.6 The Final Boss: CAPTCHA

CAPTCHAs are the ultimate barrier—frustrating for both users and crawlers. While 99% of bots are stopped cold by CAPTCHAs, the remaining 1% typically rely on image processing and AI-based resolvers. But implementing a robust CAPTCHA solver is extremely costly—in terms of time, engineering effort, and computational resources.

## Conclusion

Crawling in real-world environments goes far beyond writing a few scripts. It demands a solid mix of behavioral analysis, network security, HTTP reverse engineering, and resilient system design. Over the past year, we combined these skills with teamwork and iterative learning to build crawler systems that now feed critical data to our products—day in, day out.

If there’s one takeaway from this journey, it’s this: Real crawling is less about "hacking" the web and more about engineering smart systems that can gracefully adapt, persist, and evolve. And yeah, every time we bypassed a particularly nasty WAF or solved a tricky CAPTCHA, it did feel a bit like winning a boss fight.
