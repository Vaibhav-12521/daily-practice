# 197. Rising Temperature

| Platform | Difficulty | Topic | Submitted In | Status |
|----------|------------|-------|--------------|--------|
| [LeetCode](https://leetcode.com/problems/rising-temperature/) | 🟢 Easy | Database, Self Join, Date Functions | MySQL | ✅ Accepted (79 ms) |

## Objective

In this challenge, we practice **self joins** and **date arithmetic**: comparing each row of a table with another row of the same table that is exactly one day earlier.

## Task

Write a solution to find all dates' `id` with **higher temperatures** compared to its previous calendar date (yesterday).

Return the result table in **any order**.

## Schema

Table: `Weather`

```
+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| recordDate    | date    |
| temperature   | int     |
+---------------+---------+
```

- `id` is the column with unique values for this table.
- There are no different rows with the same `recordDate`.
- This table contains information about the temperature on a certain day.

## Example

**Input:** `Weather` table

```
+----+------------+-------------+
| id | recordDate | temperature |
+----+------------+-------------+
| 1  | 2015-01-01 | 10          |
| 2  | 2015-01-02 | 25          |
| 3  | 2015-01-03 | 20          |
| 4  | 2015-01-04 | 30          |
+----+------------+-------------+
```

**Output:**

```
+----+
| id |
+----+
| 2  |
| 4  |
+----+
```

## Explanation

Compare each day with the day **before** it:

| id | recordDate | temperature | Yesterday's temperature | Warmer? |
|----|------------|-------------|-------------------------|---------|
| 1 | 2015-01-01 | 10 | no record | ❌ |
| 2 | 2015-01-02 | 25 | 10 | ✅ `25 > 10` |
| 3 | 2015-01-03 | 20 | 25 | ❌ `20 < 25` |
| 4 | 2015-01-04 | 30 | 20 | ✅ `30 > 20` |

So the answer is ids `2` and `4`.

### ⚠️ Common Pitfall

Don't join on `w1.id = w2.id + 1`. Ids are **not guaranteed** to be in date order, and dates can have **gaps** (e.g. `2015-01-01` followed by `2015-01-05`). "Yesterday" must be decided by the **date**, not by the id or by row order.

## Solutions

### MySQL ✅ (submitted)

```sql
SELECT w1.id
FROM Weather w1
JOIN Weather w2 ON w1.recordDate = DATE_ADD(w2.recordDate, INTERVAL 1 DAY)
WHERE w1.temperature > w2.temperature;
```

### MySQL: alternative with `DATEDIFF`

```sql
SELECT w1.id
FROM Weather w1
JOIN Weather w2 ON DATEDIFF(w1.recordDate, w2.recordDate) = 1
WHERE w1.temperature > w2.temperature;
```

### MySQL: alternative with the `LAG` window function

```sql
SELECT id
FROM (
    SELECT id,
           recordDate,
           temperature,
           LAG(recordDate)  OVER (ORDER BY recordDate) AS prev_date,
           LAG(temperature) OVER (ORDER BY recordDate) AS prev_temp
    FROM Weather
) t
WHERE temperature > prev_temp
  AND DATEDIFF(recordDate, prev_date) = 1;   -- previous row must really be yesterday
```

### PostgreSQL

```sql
SELECT w1.id
FROM Weather w1
JOIN Weather w2 ON w1.recordDate = w2.recordDate + 1
WHERE w1.temperature > w2.temperature;
```

### MS SQL Server

```sql
SELECT w1.id
FROM Weather w1
JOIN Weather w2 ON w1.recordDate = DATEADD(day, 1, w2.recordDate)
WHERE w1.temperature > w2.temperature;
```

### Oracle

```sql
SELECT w1.id
FROM Weather w1
JOIN Weather w2 ON w1.recordDate = w2.recordDate + 1
WHERE w1.temperature > w2.temperature;
```

### Python (Pandas)

```python
import pandas as pd

def rising_temperature(weather: pd.DataFrame) -> pd.DataFrame:
    weather = weather.copy()
    weather["recordDate"] = pd.to_datetime(weather["recordDate"])
    weather = weather.sort_values("recordDate")

    prev = weather.shift(1)  # the previous row, after sorting by date

    is_yesterday = (weather["recordDate"] - prev["recordDate"]).dt.days == 1
    is_warmer = weather["temperature"] > prev["temperature"]

    return weather.loc[is_yesterday & is_warmer, ["id"]]
```

### How the Solution Works

1. **Self join**: treat `Weather` as two tables: `w1` (today) and `w2` (yesterday).
2. **Match yesterday by date**: pair each row in `w1` with the row in `w2` whose date is exactly one day earlier (`w1.recordDate = w2.recordDate + 1 day`). The first day has no match, so the inner `JOIN` drops it automatically.
3. **Filter warmer days**: keep only pairs where `w1.temperature > w2.temperature`.
4. **Return the id**: select `w1.id`, the id of the warmer day.

The `LAG` and Pandas versions follow the same idea without a join: sort by date, look at the previous row, and **check that it really is yesterday** before comparing temperatures.

## Complexity

| Approach | Time | Space |
|----------|------|-------|
| Self join on date | `O(n log n)` with an index on `recordDate` (`O(n²)` worst case without one) | `O(n)` |
| `LAG` window function / Pandas `shift` | `O(n log n)` (sorting by date) | `O(n)` |

## Key Takeaways

- A **self join** lets you compare rows within the same table.
- Use **date functions** (`DATE_ADD`, `DATEDIFF`, `DATEADD`, `date + 1`) to find "yesterday". Never rely on `id` order.
- With window functions like `LAG`, always double-check that the previous row is the one you expect.
