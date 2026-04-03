# Web & Data Track

> Difficulty signal: ★ (challenging), ★★ (hard), ★★★ (genuinely difficult)

> Why this track: the core curriculum builds APIs and databases, but never
> teaches you how to get data from the outside world into your systems, or how
> to make that data searchable at scale. Web scraping is a fundamental data
> engineering skill — every ML pipeline, price comparison tool, and market
> intelligence product starts with a scraper. Search is equally ubiquitous:
> users expect instant, typo-tolerant, faceted search in every product.
>
> These labs pair naturally: scrape the data, then make it searchable.

---

### Data-A — Web Scraping: Crawlers, Parsers & Ethics ★

**Companion to**: Lab 08 (PostgreSQL), Lab 14 (Docker Compose)
**Time box**: 8 hours
**Language**: Python (Scrapy + BeautifulSoup + Playwright)
**What**: Build three scrapers of increasing complexity: a static HTML scraper,
a paginated API consumer, and a JavaScript-rendered SPA scraper. Store
everything in PostgreSQL with deduplication.

**Why Python**: Scrapy is the industry-standard crawling framework — battle-tested
at scale by companies like Scrapinghub, Zyte, and data teams everywhere.
BeautifulSoup for parsing, Playwright for JS-rendered pages. Python's ecosystem
for scraping is unmatched.

**What you'll learn**:
- HTTP fundamentals at the scraping level: headers, cookies, sessions, User-Agent rotation
- `robots.txt` and rate limiting: ethical scraping practices (and legal boundaries)
- CSS selectors and XPath for DOM traversal
- Scrapy architecture: spiders, items, pipelines, middlewares
- Playwright for JavaScript-rendered content (SPAs that defeat simple HTTP requests)
- Data pipeline: scrape → clean → deduplicate → store in PostgreSQL
- Anti-bot detection: what it is, why sites use it, how to respect it

**Done when**:
- [ ] Scraper 1: static HTML site → structured data in PostgreSQL (e.g., books.toscrape.com)
- [ ] Scraper 2: paginated REST API → all pages fetched with rate limiting and retry logic
- [ ] Scraper 3: JS-rendered SPA → Playwright extracts data after JavaScript execution
- [ ] All scrapers respect `robots.txt` and implement polite delays (≥1s between requests)
- [ ] Deduplication: re-running a scraper doesn't create duplicate rows
- [ ] Data stored in PostgreSQL with timestamps and source URL for provenance
- [ ] Docker Compose: Scrapy + PostgreSQL + optional Playwright container
- [ ] README documents ethical considerations and legal boundaries of web scraping

**Key concept to internalize**: The difference between scraping (parsing HTML a server
already rendered) and crawling (following links to discover pages). Scrapy does both.
Most "scraping" tutorials only teach parsing — the crawling strategy is where real
engineering lives.

**Testing requirement**: Unit tests for parsers (given HTML fixture, assert extracted fields).
Integration test for the full pipeline: scrape → store → query. Test that deduplication
works (run twice, assert same row count). **CI**: `ruff` → `pytest --cov` (≥70% coverage).

**AI usage note**: CSS selectors and XPath can be tricky. Use AI to explain:
*"Why does this XPath return nothing when I can see the element in the browser?"*
(Hint: it's usually JavaScript-rendered content.) Write the scraping logic yourself —
the patterns are the education.

---

### Data-B — Search Engine: Full-Text Search with Meilisearch ★

**Companion to**: Lab 03 (REST API), Lab 08 (PostgreSQL), Data-A (Web Scraping)
**Time box**: 7 hours
**Language**: TypeScript
**What**: Add instant, typo-tolerant, faceted search to a dataset. Build an
indexing pipeline that syncs PostgreSQL data into Meilisearch and a search API
that supports filters, facets, and highlighting.

**Why Meilisearch over Elasticsearch**: Meilisearch is purpose-built for
application search — instant results, typo tolerance out of the box, and a
fraction of Elasticsearch's operational complexity. It teaches the same
fundamentals (inverted indexes, relevance ranking, faceting) without requiring
a week of YAML tuning. Elasticsearch is what you graduate to when you need
cluster-scale — understanding Meilisearch first makes that transition smooth.

**What you'll learn**:
- How full-text search works: tokenization, inverted indexes, TF-IDF/BM25 basics
- Typo tolerance: Levenshtein distance and why Meilisearch's prefix search feels instant
- Indexing pipeline: PostgreSQL → transform → Meilisearch index (batch and incremental)
- Faceted search: filter by category, price range, rating — combined with text search
- Relevance tuning: searchable attributes, ranking rules, custom ranking
- Search UX patterns: highlighting, snippets, "did you mean" suggestions

**Done when**:
- [ ] Meilisearch running in Docker with a dataset indexed (10k+ documents)
- [ ] Indexing pipeline: TypeScript script syncs PostgreSQL → Meilisearch
- [ ] Search API: `/search?q=...&filter=...&facets=...` returns results in <50ms
- [ ] Typo tolerance: searching "javscript" returns "JavaScript" results
- [ ] Faceted filtering: filter by at least 2 dimensions (e.g., category + price range)
- [ ] Highlighted results: search terms highlighted in response snippets
- [ ] Incremental sync: new/updated PostgreSQL rows are indexed without full re-index
- [ ] Docker Compose: API + PostgreSQL + Meilisearch

**Testing requirement**: Integration tests for search relevance (search "X" → first result
contains "X"), facet filtering, typo tolerance. Benchmark: search latency under 50ms
for 10k documents. **CI**: `tsc --noEmit` → `eslint` → `vitest run --coverage` (≥80% coverage).

**AI usage note**: Meilisearch's API is clean and well-documented. Use AI to understand
search theory: *"How does an inverted index work and why is it faster than a SQL LIKE query?"*
The indexing pipeline and relevance tuning should be your own work.

---
