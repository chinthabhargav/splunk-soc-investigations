# 08 --- Web Server Logs

Web logs record HTTP requests and responses. They help analysts investigate suspicious URL requests, repeated errors, possible scanning, and unusual client behavior.

## Common fields

  Field/concept |Meaning
  :-------| :---------------
  `_time`|Request/event time
  `src_ip`|Client address, if recorded
  `http_method`|HTTP method such as GET or POST
  `uri` / `url`|Requested path or URL
  `status` / `status_code` / `http_status`|HTTP response status
  `user_agent`|Client/browser identification string
  `referer`|Referring page, if provided
  `host`|Web host or system associated with the event
  `bytes`|Response/request bytes, depending on format
  `response_time`|Request duration, if recorded

These fields are not standardized across every log source. For example, an Apache access log may need field extraction before `status` or `uri` is available.

## Common HTTP status codes

  Code        |General meaning
  ----------- |------------------------------------------------------
  200         |Successful response
  301 / 302   |Redirect
  400         |Bad request
  401         |Authentication required/failed, depending on context
  403         |Forbidden
  404         |Not found
  500         |Internal server error

## Example: count 404 responses by client

``` spl
index=web status=404
| stats count by src_ip
| sort - count
```

The field may be named `status_code` or `http_status` instead. Check your events.

## Example: inspect requests to an admin path

``` spl
index=web uri="/admin/login"
| table _time src_ip http_method uri status user_agent
| sort - _time
```

This exact-match query only finds that exact value. Paths containing extra parameters or different prefixes may require a wildcard or another condition.

## Possible suspicious patterns

-   Many requests for nonexistent paths.
-   Requests to sensitive or unexpected endpoints.
-   Unusual request rates from one client.
-   Repeated authentication failures.
-   Odd user-agent strings or unexpected HTTP methods.
-   Requests followed by server errors or unusual outbound connections.

A high number of 404s can be normal, and user-agent strings can be forged. Validate with context and related evidence.

## Analyst workflow

1.  Identify the client, path, method, status, and timestamp.
2.  Group requests by client and path.
3.  Review the sequence and request rate.
4.  Correlate with authentication, firewall, and endpoint logs.
5.  State what the evidence supports---avoid claiming an attack based on one field.

