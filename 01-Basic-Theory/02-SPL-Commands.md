# 02 --- SPL Commands

SPL means **Search Processing Language**. It is used to search and
process events in Splunk. Because SQL concepts are familiar, some
commands will feel similar, but SPL and SQL are not identical.

## Core commands

  |SPL                     |Purpose                 |Rough SQL comparison|
  |----------------------- |----------------------- |-----------------------|
  |Search terms  |Find/filter events |`WHERE`-like filtering before the first pipe matching events|
  |`table`     | Display selected fields | `SELECT` columns|
  |`fields`    | Keep or remove fields|Selecting/omitting |
  |`sort`      |Sort results column|`ORDER BY`  sorts results by one or more|
  |`stats`     |Aggregate results|Aggregate functions / `GROUP BY`|
  |`where`     |Filter using field expressions|`WHERE` on expressions|
  |`eval`      |Create or calculate a field| Calculated expression|
  |`dedup`     |Keep one result per field value combination | Roughly `DISTINCT`, but behavior differs |
  | `rename`   |Rename fields|`AS`-like aliasing|
  |`head`      |Keep the first N results  |`LIMIT`-like behavior|
  |`top`       |Show most frequent values |Frequency summary|
  |`rare`      |Show least frequent values|Frequency summary|
  |`timechart` |Aggregate values over time|Time-bucketed aggregation|
  |`rex`       |Extract fields using regular expressions|No direct basic SQL equivalent |
  -----------------------------------------------------------------------

## Examples

### Filter events

``` spl
index=auth action=failure
```

Find events in the `auth` index where `action` equals `failure`. The
index and field/value are examples; your data may differ.

### Select fields

``` spl
index=auth 
| table _time user src_ip action
```

### Sort by count, highest first

``` spl
index=auth action=failure 
| stats count by src_ip
| sort - count
```

A minus sign sorts descending. Use `sort 10 - count` if you only need
the top 10 rows.

### Count events by user

``` spl
index=auth
| stats count by user
```

### Filter after calculating a field

``` spl
index=auth
| stats count by user
| where count > 5
```

### Create a calculated field

``` spl
index=network
| eval total_bytes=bytes_in+bytes_out
| table _time src_ip dest_ip bytes_in bytes_out total_bytes
```

This works only if the relevant fields exist and contain compatible
numeric values.

### Remove duplicate values

``` spl
index=auth
| dedup user
| table _time user src_ip
```

`dedup` keeps one event per user according to the search result order.
It is not a substitute for understanding event ordering.

### Count events over time

``` spl
index=auth
| timechart span=1h count
```

### Find common values

``` spl
index=web
| top limit=10 uri
```

### Extract a field from raw text

``` spl
index=main
| rex field=_raw "user=(?<extracted_user>[^ ]+)"
| table _time extracted_user _raw
```

This regex only fits logs containing text like `user=alice`. Change it
to match your real event format.

## Pipes

The pipe `|` passes the results of one command to the next command.

``` spl
index=auth action=failure
| stats count by src_ip
| sort - count
| head 10
```

Read it left to right: search failed events → count by source IP → sort
highest first → keep the first 10.

## Learning priority

Start with `search`, `table`, `fields`, `sort`, `stats`, `where`,
`eval`, `dedup`, `rename`, `head`, `top`, `timechart`, and `rex`. 
