# 01 --- Splunk Basics

## 1. What is Splunk?

Splunk is a platform used to search, analyze, and visualize
machine-generated data such as security logs, application logs, and
network events. Security teams can use it as a SIEM to investigate
alerts and look for suspicious activity.

## 2. Key terms

  |Term         |Simple meaning|
  |------------ |---------------------------------------------------|
  |Event        |One recorded occurrence, such as a login attempt|
  |Index        |A logical place where events are stored|
  |Source       |Where the data came from, such as a file or input|
  |Sourcetype   |Label describing the data format/type|
  |Host         |Machine associated with the event|
  |Field        |A named value extracted from an event|
  |`_time`      |Event timestamp as interpreted by Splunk|
  |`_raw`       |Raw event text|

## 3. How an investigation works

1.  Choose the relevant time range.
2.  Search the appropriate index or data source.
3.  Inspect sample events.
4.  Identify useful fields.
5.  Filter and summarize the results.
6.  Compare the results with expected behavior.
7.  Document evidence and limitations.

## 4. First searches

``` spl
index=*
```

This can search across indexes you are allowed to access, but it may be
broad and expensive. Prefer a known index and a limited time range when
possible.

``` spl
index=main
```

`main` is only an example index name. Replace it with an index that
exists in your environment.

``` spl
index=main | table _time host source sourcetype _raw
```

This displays selected fields for matching events. If a field is not
present, it may be unavailable or not extracted.

## 5. Field discovery

Use the Fields sidebar in Splunk Search to see which fields appear in
the current results. You can also inspect raw events to understand the
original log format.

## 6. Important reality check

Splunk does not automatically contain every company's Windows, firewall,
DNS, or EDR logs. Data must be collected and configured. Your searches
only find evidence present in the selected data and time range.

## Quick revision

-   **Index:** where events are stored.
-   **Source:** where the data came from.
-   **Sourcetype:** what kind of data it is.
-   **Host:** machine associated with the event.
-   **Field:** searchable attribute.
-   **`_raw`:** original event text.
-   **`_time`:** event timestamp.
