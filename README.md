# What and Where are the World's Oldest Businesses? 🏢🕰️

## 📌 Project Overview
An important part of business planning is ensuring that a company survives changing economic climates and market conditions. Some companies manage this exceptionally well, thriving for hundreds or even thousands of years. 

This project explores a dataset curated by BusinessFinancing.co.uk containing historical registry profiles for the oldest continuously operating businesses across the globe. By utilizing **PostgreSQL relational data joins, aggregations, and subqueries**, this analysis investigates business longevity, historical foundations before 1000 AD, industry category distributions, and regional trends across continents.

## 🛠️ Tech Stack & Skills Demonstrated
- **SQL Dialect:** PostgreSQL / Standard SQL
- **Data Joins:** `INNER JOIN`, `LEFT JOIN`
- **Aggregations & Grouping:** `COUNT()`, `MIN()`, `GROUP BY`, `HAVING`
- **Filtering & Ordering:** `WHERE`, `ORDER BY DESC`, `LIMIT`

## 🗄️ Database Schema
The analysis references three distinct relational tables:

### `businesses`

| Column | Type | Description |
| :--- | :--- | :--- |
| `business` | VARCHAR | The name of the company. |
| `year_founded` | INT | The year the company was established. |
| `category_code` | VARCHAR | Relational ID pointing to the industry type. |
| `country_code` | VARCHAR | Relational ID pointing to the origin nation. |

### `countries`

| Column | Type | Description |
| :--- | :--- | :--- |
| `country_code` | VARCHAR | Unique primary key code for the country. |
| `country` | VARCHAR | The common name of the country. |
| `continent` | VARCHAR | The continent where the country is located. |

### `categories`

| Column | Type | Description |
| :--- | :--- | :--- |
| `category_code` | VARCHAR | Unique primary key code for the category. |
| `category` | VARCHAR | The industry description (e.g., Banking, Construction). |

---

## 🔍 Step-by-Step Project Breakdown & Queries

### 1. The Oldest Business in the World
Locating the foundational range limits of corporate ages in our database.
```sql
SELECT 
    MIN(year_founded) AS oldest_founding_year, 
    MAX(year_founded) AS newest_founding_year
FROM businesses;
```

### 2. How Many Businesses Were Founded Before 1000?
Counting how many ancient corporate entities are recorded in our history.
```sql
SELECT COUNT(*) AS count_before_1000
FROM businesses
WHERE year_founded < 1000;
```

### 3. Which Businesses Were Founded Before 1000?
Listing the specific elite legacy companies established before 1000 AD, sorted chronologically.
```sql
SELECT business, year_founded, country_code
FROM businesses
WHERE year_founded < 1000
ORDER BY year_founded;
```

### 4. Exploring the Categories
Joining the `businesses` and `categories` tables to reveal the operating industries of these ancient companies.
```sql
SELECT b.business, b.year_founded, b.country_code, c.category
FROM businesses AS b
LEFT JOIN categories AS c 
  ON b.category_code = c.category_code
WHERE b.year_founded < 1000
ORDER BY b.year_founded;
```

### 5. Counting the Categories
Identifying the global distribution of industries to see which types of business survive the longest.
```sql
SELECT c.category, COUNT(b.business) AS n
FROM categories AS c
INNER JOIN businesses AS b 
  ON c.category_code = b.category_code
GROUP BY c.category
ORDER BY n DESC
LIMIT 10;
```

### 6. Oldest Business by Continent
Mapping the earliest founded business milestones relative to their respective geographic continent.
```sql
SELECT MIN(b.year_founded) AS oldest, c.continent
FROM businesses AS b
INNER JOIN countries AS c 
  ON b.country_code = c.country_code
GROUP BY c.continent
ORDER BY oldest;
```

### 7. Joining Everything for Further Analysis
Consolidating all three datasets into a unified foundational reporting layer.
```sql
SELECT b.business, b.year_founded, c.category, co.country, co.continent
FROM businesses AS b
INNER JOIN categories AS c 
  ON b.category_code = c.category_code
INNER JOIN countries AS co 
  ON b.country_code = co.country_code;
```

### 8. Counting Categories by Continent
An assessment of how industries concentrate geographically across global markets.
```sql
SELECT co.continent, c.category, COUNT(b.business) AS n
FROM businesses AS b
INNER JOIN categories AS c 
  ON b.category_code = c.category_code
INNER JOIN countries AS co 
  ON b.country_code = co.country_code
GROUP BY co.continent, c.category;
```

### 9. Filtering Counts by Continent and Category
Using the `HAVING` clause to target prominent, deeply embedded regional industry sectors.
```sql
SELECT co.continent, c.category, COUNT(b.business) AS n
FROM businesses AS b
INNER JOIN categories AS c 
  ON b.category_code = c.category_code
INNER JOIN countries AS co 
  ON b.country_code = co.country_code
GROUP BY co.continent, c.category
HAVING COUNT(b.business) > 5
ORDER BY n DESC;
```

---

## 💡 Summary of Key Findings
- **The Ultimate Survivor:** **Kongo Gumi**, a construction enterprise established in Japan in **578 AD**, is recognized as the oldest continuously operating business globally.
- **Resilient Industries:** Sectors handling fundamental civic or cultural goods—such as **Banking & Finance**, **Distillers/Breweries**, and **Aviation/Postal Mints**—demonstrate significantly higher structural survival rates over centuries.
- **Data Density Gaps:** Cross-referencing tracking logs reveals certain geopolitical zones where ancestral industry corporate profiles remain unrecorded or untracked.
