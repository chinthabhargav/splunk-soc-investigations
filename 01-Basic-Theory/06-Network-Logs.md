# 06 --- Network and Firewall Logs

Network logs describe communications between systems. Firewall logs may
also record whether traffic was allowed or blocked.

## Common fields

  Field/concept              |Meaning
  :--------------------------|:-------------------------------------------------------
  `_time`                    |Event time
  `src_ip`                   |Source address
  `src_port`                 |Source port
  `dest_ip`                  |Destination address
  `dest_port`                |Destination port
  `protocol`                 |TCP, UDP, ICMP, etc.
  `action`                   |Allow, deny, drop, or another product-specific result
  `direction`                |Inbound/outbound, if recorded
  `bytes_in` / `bytes_out`   |Traffic volume, if recorded
  `duration`                 |Connection duration, if recorded
  `host`                     |Device that logged the event

>Different products may use names such as `SourceIP`, `src`, `dst`,
>`dpt`, `src_ip`, or `dest_ip`.

## Understand a connection

Example event:

>``` text
>src_ip=192.168.1.50
>src_port=51000
>dest_ip=10.0.0.20
>dest_port=22
>protocol=TCP
>action=allowed
>```

Interpretation: the log records a TCP connection from `192.168.1.50` to
port 22 on `10.0.0.20`, and the product recorded it as allowed. This
alone does not prove an SSH login occurred.

## Example: summarize traffic by destination port

``` spl
index=network
| stats count by dest_port
| sort - count
```

## Example: inspect blocked traffic

``` spl
index=firewall action=blocked
| stats count by src_ip dest_ip dest_port
| sort - count
```

Use the correct index, field names, and action value for your source.

## Possible port-scanning indicators

-   One source contacting many destination ports on one host.
-   One source contacting the same port across many hosts.
-   Many short connections or denied connection attempts in a short
    period.

These patterns can also be caused by vulnerability scanners, monitoring,
or legitimate administration. Confirm authorization and context.

## Example: inspect one source

``` spl
index=network src_ip="192.168.1.50"
| table _time src_ip src_port dest_ip dest_port protocol action
| sort _time
```

## Investigation questions

1.  Which system initiated the traffic?
2.  Which destination and port were contacted?
3.  Was the traffic allowed or blocked?
4.  Was there a successful connection or only attempts?
5.  Is the destination expected for that source?
6.  Are there related authentication, DNS, or endpoint events?

## Limitations

>Firewall flow records are not always full packet captures. They may not
>contain application content, process names, or proof of what a user did
>after a connection was established.
