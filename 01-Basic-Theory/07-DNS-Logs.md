# 07 --- DNS Logs

DNS translates domain names into records such as IP addresses. DNS logs
can help analysts investigate unusual domains, suspicious lookups, and
possible command-and-control activity.

## Common fields

  Field/concept              | Meaning
  :---------------------------| :-------------------------------------------------
  `_time`                    | Query/event time
  `src_ip`                   | Client making the request, if recorded
  `dest_ip`                  | DNS server or destination, depending on the log
  `query` / `domain`         | Requested name
  `query_type`               | Record type, such as A, AAAA, MX, TXT
  `answer`                   | Returned answer, if logged
  `response_code` / `rcode`  | DNS response result
  `host`                     | Device or collector associated with the event

Field names depend on the DNS product and parser. Inspect sample events
before writing searches.

## Example: most frequently queried names

``` spl
index=dns
| stats count by query
| sort - count
| head 20
```

## Example: inspect DNS activity from one client

``` spl
index=dns src_ip="10.0.0.50"
| table _time src_ip query query_type answer response_code
| sort - _time
```

## What can look suspicious?

-   Repeated queries to a rare or newly observed domain.
-   Long or unusual subdomain strings.
-   A host contacting domains unrelated to its normal role.
-   Large volumes of failed lookups.
-   DNS activity near the time of a suspicious process or endpoint alert.

None of these alone proves malware. Software updates, CDNs, telemetry,
and legitimate applications can create unusual DNS patterns.

## Investigation workflow

1.  Identify the client and requested domain.
2.  Check frequency and timeline.
3.  Review query type and response.
4.  Correlate the domain with endpoint processes and network connections.
5.  Check reputation using approved sources and company procedures.
6.  Document the evidence and uncertainty.

