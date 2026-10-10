# 04 --- Authentication Logs

Authentication logs record attempts to prove identity, such as password
logins, remote logons, and multi-factor authentication events. They help
analysts investigate failed logins, suspicious access, and possible
account compromise.

## Common fields

  | Field/concept           |Meaning|
  |:------------------------|:------------------------|
  |`_time`                  |Time of the event
  |`user` / `user_name`     |Account involved
  |`src_ip`                 |Originating IP, if recorded
  |`host`                   |System handling or recording the event
  |`action` / `status`      |Outcome; exact values vary|
  |`reason`                 |Failure reason, if recorded
  |`authentication_method`  |Password, MFA, Kerberos, etc., if recorded|
  |`Logon_Type`             |Windows logon type in relevant Windows events
  |`EventCode` / `EventID`  |Windows event identifier in relevant data|
  |`TargetUserName`         |Common Windows field for target account in some event |
                                      
  -----------------------------------------------------------------------

## Example: To count failed events by source IP

``` spl
index=auth action=failure
| stats count by src_ip
| sort - count
```

Use this only if your data uses index `auth`, field `action`, and value
`failure`. Otherwise adjust them.

## Example: To inspect a user's login timeline

``` spl
index=auth user="alice"
| table _time user src_ip host action status
| sort _time
```

## Example: To look for failures followed by success

First find the account's events:

``` spl
index=auth user="alice"
| table _time user src_ip action status host
| sort _time
```

Then manually inspect the timeline and validate whether failures were
followed by a successful login. A simple count alone cannot prove that a
brute-force attack succeeded.

## What may be suspicious?

-   Many failures for one account in a short period.
-   Failures against many accounts from one source.
-   A successful login after repeated failures.
-   Login from an unusual source or at an unusual time.
-   A new source IP followed by sensitive actions.

These are leads, not proof. Consider expected behavior, VPNs, NAT,
shared IPs, service accounts, and the surrounding evidence.

## Windows event IDs to recognize

-   **4624:** successful logon.
-   **4625:** failed logon.
-   **4634:** account logoff.
-   **4648:** logon using explicit credentials.
-   **4672:** special privileges assigned to a new logon.

The fields available depend on Windows audit configuration and how
events are ingested.

## Analyst workflow

1.  Set an appropriate time range.
2.  Identify the relevant account, source IP, destination host, and
    outcome.
3.  Count and inspect events.
4.  Build a timeline.
5.  Compare with known-good behavior and other log sources.
6.  Record evidence, uncertainty, and next steps.
