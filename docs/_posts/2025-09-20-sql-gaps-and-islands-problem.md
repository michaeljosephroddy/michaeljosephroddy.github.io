---
layout: post
title: "Gaps and Islands Problem | Find Consecutive Days | SQL"
categories: blog programming sql
---

# Solving the Gaps and Islands Problem: Finding Consecutive Days in MariaDB SQL

Today, we'll explore a solution to the gaps and islands problem in SQL, demonstrating how to group and identify consecutive days (islands) and the gaps between them in an SQL result set. For this demonstration, we'll be using MariaDB.

## Problem Definition

To begin, let's establish our specific problem definition, as there are numerous variations of the gaps and islands problem in SQL:

**Given a table of journal entries where each entry has a `created_at` (timestamp) column and a `mood_rating` (int) column, find all consecutive entries for a user with `user_id` (bigint) between `start_date` (date) and `end_date` (date) where `mood_rating >= 6`.**

**Note:** A user can have multiple journal entries per day, and each journal entry will have a mood rating.

## Sample Data

Here's what our initial table looks like:

```sql
+---------------------+-------------+
| created_at          | mood_rating |
+---------------------+-------------+
| 2025-08-01 21:15:00 |           6 |
| 2025-08-02 16:30:00 |           7 |
| 2025-08-03 20:30:00 |           5 |
| 2025-08-04 22:45:00 |           8 |
| 2025-08-05 20:00:00 |           4 |
| 2025-08-06 18:45:00 |           6 |
| 2025-08-07 21:45:00 |           7 |
| 2025-08-08 19:00:00 |           5 |
| 2025-08-09 20:30:00 |           7 |
| 2025-08-10 22:15:00 |           6 |
| 2025-08-11 19:30:00 |           7 |
| 2025-08-12 18:30:00 |           4 |
| 2025-08-13 20:45:00 |           6 |
| 2025-08-14 19:00:00 |           7 |
| 2025-08-15 19:45:00 |           8 |
| 2025-08-16 21:00:00 |           6 |
| 2025-08-17 21:15:00 |           7 |
| 2025-08-18 21:30:00 |           8 |
| 2025-08-19 18:00:00 |           5 |
| 2025-08-20 19:15:00 |           6 |
| 2025-08-21 18:45:00 |           7 |
| 2025-08-22 19:30:00 |           6 |
| 2025-08-23 19:00:00 |           8 |
| 2025-08-24 19:45:00 |           6 |
| 2025-08-25 21:00:00 |           7 |
| 2025-08-26 17:45:00 |           6 |
| 2025-08-27 20:00:00 |           7 |
| 2025-08-28 19:00:00 |           6 |
| 2025-08-29 19:30:00 |           7 |
| 2025-08-30 20:15:00 |           8 |
| 2025-08-31 20:45:00 |           7 |
| 2025-08-09 22:00:00 |           4 |
| 2025-08-28 11:05:00 |           3 |
+---------------------+-------------+
```

## Solution Approach

To solve this problem, we'll need to use common table expressions (CTEs) to "pipe" the result of one query into the next and perform sequential operations. This approach allows us to manipulate the data step by step to achieve our final result. We'll likely need multiple CTEs to accomplish this task.

### Step 1: Extract Daily Averages

The first query we need to create extracts all dates that satisfy our condition. We'll use the `DATE()` function to extract the date part from the timestamp, then apply our conditions and sort in ascending order.

```sql
SELECT DATE(created_at) AS date,
       AVG(mood_rating) AS avg_mood_rating
FROM   journal_entry
WHERE  user_id = 3
       AND DATE(created_at) BETWEEN '2025-08-01' AND '2025-08-31'
GROUP  BY DATE(created_at)
ORDER  BY DATE(created_at);
```

We calculate the `AVG(mood_rating)` because we're identifying consecutive days where journal entries have an average mood rating of 6 or above, rather than examining each individual entry per day. We aggregate the mood ratings and calculate the average for each day.

The resulting data from executing the above query should look like this:

```sql
+------------+-----------------+
| date       | avg_mood_rating |
+------------+-----------------+
| 2025-08-01 |          6.0000 |
| 2025-08-02 |          7.0000 |
| 2025-08-03 |          5.0000 |
| 2025-08-04 |          8.0000 |
| 2025-08-05 |          4.0000 |
| 2025-08-06 |          6.0000 |
| 2025-08-07 |          7.0000 |
| 2025-08-08 |          5.0000 |
| 2025-08-09 |          5.5000 |
| 2025-08-10 |          6.0000 |
| 2025-08-11 |          7.0000 |
| 2025-08-12 |          4.0000 |
| 2025-08-13 |          6.0000 |
| 2025-08-14 |          7.0000 |
| 2025-08-15 |          8.0000 |
| 2025-08-16 |          6.0000 |
| 2025-08-17 |          7.0000 |
| 2025-08-18 |          8.0000 |
| 2025-08-19 |          5.0000 |
| 2025-08-20 |          6.0000 |
| 2025-08-21 |          7.0000 |
| 2025-08-22 |          6.0000 |
| 2025-08-23 |          8.0000 |
| 2025-08-24 |          6.0000 |
| 2025-08-25 |          7.0000 |
| 2025-08-26 |          6.0000 |
| 2025-08-27 |          7.0000 |
| 2025-08-28 |          4.5000 |
| 2025-08-29 |          7.0000 |
| 2025-08-30 |          8.0000 |
| 2025-08-31 |          7.0000 |
+------------+-----------------+
```

### Step 2: Filter and Add Row Numbers

Now we have our base data. The next step is to filter the result set to include only journal entries where the `avg_mood_rating` is >= 6. We'll also add a column called `rn` (int), which represents the row number for each entry. The row number is crucial for solving this problem because we'll use it to logically group the gaps and islands in our result set.

We'll incorporate common table expressions from this point onwards. First, we'll define the query needed to manipulate the result set above and produce the desired output with the row number column added, then we'll organise the SQL into a CTE.

The query we need to apply to the table above is:

```sql
SELECT *,
       ROW_NUMBER() OVER (ORDER BY date) AS rn
FROM   first_query
WHERE  avg_rating >= 6
```

Let's organise our code into the following CTE:

```sql
WITH first_query AS (
    SELECT DATE(created_at) AS date,
           AVG(mood_rating) AS avg_rating
    FROM   journal_entry
    WHERE  user_id = 3
           AND DATE(created_at) BETWEEN '2025-08-01' AND '2025-08-31'
    GROUP  BY DATE(created_at)
    ORDER  BY DATE(created_at)
),
second_query AS (
    SELECT *,
           ROW_NUMBER() OVER (ORDER BY date) AS rn
    FROM   first_query
    WHERE  avg_rating >= 6
)
SELECT * FROM second_query;
```

We're using the result of `first_query` in `second_query`, which means we have access to the table columns from `first_query` and can manipulate that data in the `second_query`.

The output from running the above CTE should result in a table like this:

```sql
+------------+------------+----+
| date       | avg_rating | rn |
+------------+------------+----+
| 2025-08-01 |     6.0000 |  1 |
| 2025-08-02 |     7.0000 |  2 |
| 2025-08-04 |     8.0000 |  3 |
| 2025-08-06 |     6.0000 |  4 |
| 2025-08-07 |     7.0000 |  5 |
| 2025-08-10 |     6.0000 |  6 |
| 2025-08-11 |     7.0000 |  7 |
| 2025-08-13 |     6.0000 |  8 |
| 2025-08-14 |     7.0000 |  9 |
| 2025-08-15 |     8.0000 | 10 |
| 2025-08-16 |     6.0000 | 11 |
| 2025-08-17 |     7.0000 | 12 |
| 2025-08-18 |     8.0000 | 13 |
| 2025-08-20 |     6.0000 | 14 |
| 2025-08-21 |     7.0000 | 15 |
| 2025-08-22 |     6.0000 | 16 |
| 2025-08-23 |     8.0000 | 17 |
| 2025-08-24 |     6.0000 | 18 |
| 2025-08-25 |     7.0000 | 19 |
| 2025-08-26 |     6.0000 | 20 |
| 2025-08-27 |     7.0000 | 21 |
| 2025-08-29 |     7.0000 | 22 |
| 2025-08-30 |     8.0000 | 23 |
| 2025-08-31 |     7.0000 | 24 |
+------------+------------+----+
```

## Identifying the Gaps and Islands Pattern

Now we have a result set containing all dates where journal entries have a `mood_rating` of 6 or above. All data is sorted by date in ascending order, and we have a row number column for each entry. If you examine the table above closely, you can see we now have data that represents a classic gaps and islands problem: consecutive days (islands) where average mood ratings are >= 6, with gaps between them.

For example, here's the pattern we can identify:

**Islands:**

```
| Island # | Start Date | End Date   | Length (days) |
| -------- | ---------- | ---------- | ------------- |
| 1        | 2025-08-01 | 2025-08-02 | 2             |
| 2        | 2025-08-04 | 2025-08-04 | 1             |
| 3        | 2025-08-06 | 2025-08-07 | 2             |
| 4        | 2025-08-10 | 2025-08-11 | 2             |
| 5        | 2025-08-13 | 2025-08-18 | 6             |
| 6        | 2025-08-20 | 2025-08-27 | 8             |
| 7        | 2025-08-29 | 2025-08-31 | 3             |
```

**Gaps:**

```
| Gap # | Missing Start | Missing End | Length (days) |
| ----- | ------------- | ----------- | ------------- |
| 1     | 2025-08-03    | 2025-08-03  | 1             |
| 2     | 2025-08-05    | 2025-08-05  | 1             |
| 3     | 2025-08-08    | 2025-08-09  | 2             |
| 4     | 2025-08-12    | 2025-08-12  | 1             |
| 5     | 2025-08-19    | 2025-08-19  | 1             |
| 6     | 2025-08-28    | 2025-08-28  | 1             |
```

### Step 3: Creating Logical Groups

How do we group the gaps and islands logically? To create logical groups, we need to subtract the row number from the day part of each date. This technique allows us to group consecutive days and identify gaps between them.

Before adding this to our CTE above, let's define the query needed to produce the logical groupings:

```sql
SELECT *,
       DATE_ADD(date, INTERVAL -rn DAY) AS consec_groups
```

This query uses the `DATE_ADD()` function to subtract the row number `rn` from each date.

Adding this query to our CTE looks like this:

```sql
WITH first_query AS (
    SELECT DATE(created_at) AS date,
           AVG(mood_rating) AS avg_rating
    FROM   journal_entry
    WHERE  user_id = 3
           AND DATE(created_at) BETWEEN '2025-08-01' AND '2025-08-31'
    GROUP  BY DATE(created_at)
    ORDER  BY DATE(created_at)
),
second_query AS (
    SELECT *,
           ROW_NUMBER() OVER (ORDER BY date) AS rn
    FROM   first_query
    WHERE  avg_rating >= 6
),
third_query AS (
    SELECT *,
           DATE_ADD(date, INTERVAL -rn DAY) AS consec_groups
    FROM   second_query
)
SELECT * FROM third_query;
```

We now have three queries, using the data from `second_query` in `third_query`. The result of running the above CTE should produce a table that looks like this:

```sql
+------------+------------+----+---------------+
| date       | avg_rating | rn | consec_groups |
+------------+------------+----+---------------+
| 2025-08-01 |     6.0000 |  1 | 2025-07-31    |
| 2025-08-02 |     7.0000 |  2 | 2025-07-31    |
| 2025-08-04 |     8.0000 |  3 | 2025-08-01    |
| 2025-08-06 |     6.0000 |  4 | 2025-08-02    |
| 2025-08-07 |     7.0000 |  5 | 2025-08-02    |
| 2025-08-10 |     6.0000 |  6 | 2025-08-04    |
| 2025-08-11 |     7.0000 |  7 | 2025-08-04    |
| 2025-08-13 |     6.0000 |  8 | 2025-08-05    |
| 2025-08-14 |     7.0000 |  9 | 2025-08-05    |
| 2025-08-15 |     8.0000 | 10 | 2025-08-05    |
| 2025-08-16 |     6.0000 | 11 | 2025-08-05    |
| 2025-08-17 |     7.0000 | 12 | 2025-08-05    |
| 2025-08-18 |     8.0000 | 13 | 2025-08-05    |
| 2025-08-20 |     6.0000 | 14 | 2025-08-06    |
| 2025-08-21 |     7.0000 | 15 | 2025-08-06    |
| 2025-08-22 |     6.0000 | 16 | 2025-08-06    |
| 2025-08-23 |     8.0000 | 17 | 2025-08-06    |
| 2025-08-24 |     6.0000 | 18 | 2025-08-06    |
| 2025-08-25 |     7.0000 | 19 | 2025-08-06    |
| 2025-08-26 |     6.0000 | 20 | 2025-08-06    |
| 2025-08-27 |     7.0000 | 21 | 2025-08-06    |
| 2025-08-29 |     7.0000 | 22 | 2025-08-07    |
| 2025-08-30 |     8.0000 | 23 | 2025-08-07    |
| 2025-08-31 |     7.0000 | 24 | 2025-08-07    |
+------------+------------+----+---------------+
```

## Understanding the Solution

If you examine the `consec_groups` column, you'll see that we have successfully grouped our data into islands by subtracting the row number from the date. This technique leaves us with consecutive dates that are identified by having the same value in the `consec_groups` column.

The mechanism works by taking each date and subtracting the row number (rn) that was assigned in sequential order. When dates follow one another without gaps, this subtraction produces the same result for all rows in that streak. Consequently, each run of consecutive dates ends up sharing the same `consec_groups` value.

In other words, dates that fall into the same streak (or island) will have identical `consec_groups` values. When there's a break in the sequence (a gap), the subtraction result changes, and a new `consec_groups` value begins.

This technique is a common SQL pattern for identifying gaps and islands in time-series data and forms the foundation for many temporal analysis queries.

## Conclusion

This example demonstrates a practical approach to solving the gaps and islands problem in SQL using MariaDB. By combining CTEs, window functions, and date arithmetic, we can effectively identify and group consecutive periods that meet specific criteria, whilst also highlighting the gaps between them.

The solution provides a solid foundation that can be adapted for various time-series analysis scenarios where identifying continuous patterns is essential.

Thanks for reading,

Mike.
