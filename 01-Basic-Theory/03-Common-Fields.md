# 03 --- Common Security Log Fields

Field names vary by data source, vendor, parsing configuration, and
Splunk add-on. These are common names or common concepts---not a
guarantee that every dataset contains each field.

## Splunk metadata fields

|Field|Meaning|
|:-|:-|
|`index`|Index containing the event|
|`source`|Input/source identifier|
|`sourcetype`|Data format/type label|
|`host`|Host associated with the event|
|`_time`|Event timestamp|
|`_raw`|Raw event text|

## Common security concepts

 |Field/concept     |           Meaning                 |Ask yourself  |
 |----------------- |-----------------------------------|-------------------|
 | `user`           |           Account involved        | Which account?
 | `src_ip`         |           Source IP address       | Where did it originate?
 | `dest_ip`        |           Destination IP address  | Which destination?
 |`src_port`        |           Source port             | Which source port?
 | `dest_port`      |           Destination port        | Which service/port?
 | `protocol`       |           Network protocol        | TCP, UDP, ICMP, etc.?
 | `action`         |           Recorded action/result  | Allowed, blocked, failure?
 | `status`         |           Result/status           | What result was recorded?
 | `reason`         |           Reason or explanation   | Why did it fail?
 | `process_name`   |           Process image/name      | Which process?
 | `parent_process` |           Parent process          | What launched it?
 | `command_line`   |           Process command line    | What arguments were used?
 | `domain` / `query`|          Domain or DNS query     | What name was requested?
 | `uri` / `url`     |          Web path or URL         | What was requested?
 | `http_method`     |          HTTP method             | GET, POST, etc.?
 | `file_name` / `file_path`|   File details            | Which file?
 | `file_hash` / `sha256`   |   File hash               | Can this file be correlated?|

## Important distinctions

-   `src_ip` and `dest_ip` are common conventions, not universal field
    names.
-   `action=blocked`, `action=deny`, and `action=failure` are not
    interchangeable; values depend on the product.
-   A source port is often temporary, while a destination port may
    identify a service. Neither alone proves malicious activity.
-   `status` may mean HTTP status, login outcome, firewall status, or
    something else. Interpret it using the log source.
-   `user` may be named `Account_Name`, `TargetUserName`, `user_name`,
    or another field in raw or normalized logs.

## Field discovery checklist

When opening a new log source:

1.  Inspect several raw events.
2.  Check extracted fields in the Splunk Fields sidebar.
3.  Identify timestamp, host, user, source, destination, action, and
    outcome fields if present.
4.  Note the exact field names and example values.
5.  Test whether values mean what you think they mean.

## Investigation questions (Triage)

-   **Who?** `user`, account fields
-   **Where from?** `src_ip`, source host
-   **Where to?** `dest_ip`, destination host
-   **When?** `_time`
-   **What happened?** `action`, `status`, `event_ID`
-   **What ran?** `process_name`, `command_line`
-   **What was requested?** `query`, `uri`, `url`
-   **What evidence can be correlated?** IPs, domains, hashes,
    usernames, timestamps
