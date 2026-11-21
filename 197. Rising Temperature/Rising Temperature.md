# 197. Rising Temperature

| Platform | Difficulty | Topic | Language | Status |
|----------|:----------:|-------|----------|:------:|
| [LeetCode](https://leetcode.com/problems/rising-temperature/) | 🟢 Easy | SQL, Self Join, Date Functions | MySQL | ✅ Accepted (79 ms) |

---

## 📝 Question

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

Write a solution to find all dates' `id` with **higher temperatures** compared to its **previous date** (yesterday).

Return the result table in **any order**.

### Example 1

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

**Explanation:**

- On `2015-01-02`, the temperature was higher than the day before (10 → 25).
- On `2015-01-04`, the temperature was higher than the day before (20 → 30).

---

## 💡 What Is the Question Really Asking?

Go through the days one by one and ask a simple question:

> **"Was today hotter than yesterday?"**

If yes, write down today's `id`. That's it.

The only tricky part: we have just **one table**, but we need to look at **two days at the same time** (today and yesterday). So we use the same table twice, once as "today" and once as "yesterday". This is called a **self join**.

---

## ✅ Solution (MySQL)

```sql
SELECT w1.id
FROM Weather w1
JOIN Weather w2
  ON w1.recordDate = DATE_ADD(w2.recordDate, INTERVAL 1 DAY)
WHERE w1.temperature > w2.temperature;
```

### What Each Line Does

| Line | In simple words |
|------|-----------------|
| `FROM Weather w1` | Open the table and call it **w1**. Think of w1 as **"today"**. |
| `JOIN Weather w2` | Open the **same** table again and call it **w2**. Think of w2 as **"yesterday"**. |
| `ON w1.recordDate = DATE_ADD(w2.recordDate, INTERVAL 1 DAY)` | Stick a row of w1 next to a row of w2 **only if** w1's date is exactly **one day after** w2's date. So every "today" gets paired with its real "yesterday". |
| `WHERE w1.temperature > w2.temperature` | From those pairs, keep only the ones where **today was hotter** than yesterday. |
| `SELECT w1.id` | Show the `id` of those hotter days. |

---

## 🔍 Step-by-Step Dry Run

Let's run the query on the example table by hand.

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

### Step 1: Find "yesterday" for every day (the `JOIN`)

For each row in **w1** (today), we look for a row in **w2** whose date is exactly one day earlier.

**Day 1: `2015-01-01` (id 1)** 
- Yesterday would be `2014-12-31`. 
- Is there a row with that date? **No.** 
- So day 1 has no partner, and it is **dropped**. (Makes sense: we can't say if it got hotter if we don't know yesterday.)

**Day 2: `2015-01-02` (id 2)** 
- Yesterday is `2015-01-01`. That row exists (id 1, temp 10). ✅ 
- Pair: **today = 25, yesterday = 10**

**Day 3: `2015-01-03` (id 3)**
- Yesterday is `2015-01-02`. That row exists (id 2, temp 25). ✅
- Pair: **today = 20, yesterday = 25**

**Day 4: `2015-01-04` (id 4)**
- Yesterday is `2015-01-03`. That row exists (id 3, temp 20). ✅
- Pair: **today = 30, yesterday = 20**

After the join, we have these pairs:

```
+-------+------------+-----------+-------+------------+-----------+
| w1.id | w1.date    | w1.temp   | w2.id | w2.date    | w2.temp   |
|       | (today)    | (today)   |       | (yesterday)| (yesterday)|
+-------+------------+-----------+-------+------------+-----------+
| 2     | 2015-01-02 | 25        | 1     | 2015-01-01 | 10        |
| 3     | 2015-01-03 | 20        | 2     | 2015-01-02 | 25        |
| 4     | 2015-01-04 | 30        | 3     | 2015-01-03 | 20        |
+-------+------------+-----------+-------+------------+-----------+
```

### Step 2: Keep only the hotter days (the `WHERE`)

Now check each pair: **is today's temperature bigger than yesterday's?**

| Today (id) | Today's temp | Yesterday's temp | Hotter? | Keep? |
|:----------:|:------------:|:----------------:|---------|:-----:|
| 2 | 25 | 10 | 25 > 10 → **Yes**, it got warmer | ✅ |
| 3 | 20 | 25 | 20 > 25 → **No**, it got cooler | ❌ |
| 4 | 30 | 20 | 30 > 20 → **Yes**, it got warmer | ✅ |

### Step 3: Show the ids (the `SELECT`)

The rows that survived are id **2** and id **4**.

```
+----+
| id |
+----+
| 2  |
| 4  |
+----+
```

This matches the expected output. 🎉

---

## ⚠️ Common Mistake

A lot of people write this:

```sql
-- ❌ Wrong
JOIN Weather w2 ON w1.id = w2.id + 1
```

It looks right on the example, but it's a trap. The `id` only tells you the row number, **not the date**. Imagine this table:

```
+----+------------+-------------+
| id | recordDate | temperature |
+----+------------+-------------+
| 1  | 2015-01-01 | 10          |
| 2  | 2015-01-05 | 25          |
+----+------------+-------------+
```

Using ids, the query would say id 2 is "hotter than yesterday". But `2015-01-05` is not the day after `2015-01-01`, there are **3 missing days** in between. We don't know what yesterday's temperature was, so id 2 should **not** be in the answer.

That's why we compare **dates**, never ids.

---

## 🔁 Other Ways to Write It

**MySQL with `DATEDIFF`** (difference between the two dates must be 1 day):

```sql
SELECT w1.id
FROM Weather w1
JOIN Weather w2 ON DATEDIFF(w1.recordDate, w2.recordDate) = 1
WHERE w1.temperature > w2.temperature;
```

**PostgreSQL / Oracle** (adding `1` to a date means "next day"):

```sql
SELECT w1.id
FROM Weather w1
JOIN Weather w2 ON w1.recordDate = w2.recordDate + 1
WHERE w1.temperature > w2.temperature;
```

**MS SQL Server:**

```sql
SELECT w1.id
FROM Weather w1
JOIN Weather w2 ON w1.recordDate = DATEADD(day, 1, w2.recordDate)
WHERE w1.temperature > w2.temperature;
```

**Python (Pandas):**

```python
import pandas as pd

def rising_temperature(weather: pd.DataFrame) -> pd.DataFrame:
    weather = weather.copy()
    weather["recordDate"] = pd.to_datetime(weather["recordDate"])
    weather = weather.sort_values("recordDate")

    prev = weather.shift(1) 

    is_yesterday = (weather["recordDate"] - prev["recordDate"]).dt.days == 1
    is_warmer = weather["temperature"] > prev["temperature"]

    return weather.loc[is_yesterday & is_warmer, ["id"]]
```

> Only the MySQL solution at the top was submitted and accepted on LeetCode. The others are reference versions.

---

## 🧠 Key Takeaways

- Need to compare a table with **itself**? Use a **self join** (same table, two names).
- To find "yesterday", use **date functions** like `DATE_ADD` or `DATEDIFF`.
- **Never** use `id` to decide which day comes before another.
- Days with no yesterday (like the first day) get dropped automatically by `JOIN`.

---

## 🤝 Connect With Me

<a href="https://vaibhav1.me" target="_blank">
  <img src="https://img.shields.io/badge/🌐_Portfolio-vaibhav1.me-00D4FF?style=for-the-badge" alt="Portfolio"/>
</a>
<a href="https://www.linkedin.com/in/vaibhavsingh125/" target="_blank">
  <img src="https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn"/>
</a>
<a href="mailto:singh12521vaibhav@gmail.com">
  <img src="https://img.shields.io/badge/Email-EA4335?style=for-the-badge&logo=gmail&logoColor=white" alt="Email"/>
</a>
<a href="https://leetcode.com/u/vaibhav125s/" target="_blank">
  <img src="https://img.shields.io/badge/LeetCode-FFA116?style=for-the-badge&logo=leetcode&logoColor=black" alt="LeetCode"/>
</a>

[⬅️ Back to all problems](../README.md)
