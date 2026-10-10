# Splunk SOC Investigations

A beginner-friendly learning repository for Splunk, Search Processing
Language (SPL), common security-log fields, and basic SOC
investigations.

## Goals

-   Understand how Splunk searches events.
-   Learn core SPL commands.
-   Recognize common fields in authentication, Windows, network, DNS,
    and web logs.
-   Practice turning an investigation question into a query.
-   Document findings clearly and honestly.

## Repository contents

-   `01-Basic-Theory/` --- concise notes on Splunk and common log types.
-   `02-SPL-Queries/` --- add reusable query examples as you practice.
-   `03-Investigations/` --- add investigation reports with queries,
    evidence, and conclusions.
-   `04-Cheat-Sheet/` --- add a short revision sheet.

## Suggested workflow

1.  Read a note.
2.  Run the example against data you are authorized to use.
3.  Check whether the fields exist in your dataset.
4.  Record what the results show.
5.  Save screenshots only when they add evidence to an investigation.
6.  Write a conclusion supported by the evidence.

## Important note about examples

The sample index names and field names in these notes are illustrative.
Your environment may use different index names, sourcetypes, or field
extractions. Replace them with the values available in your own Splunk
dataset. A query returning no results does not automatically mean there
is no suspicious activity; the data may not be present or the field
names may differ.

## Responsible use

Use these techniques only on systems and logs you own or are authorized
to investigate. Remove or mask real usernames, IP addresses, hostnames,
tokens, and other sensitive information before publishing screenshots or
logs.

## Progress

-   [x] Splunk basics
-   [ ] Core SPL commands
-   [ ] Common fields
-   [ ] Authentication logs
-   [ ] Windows logs
-   [ ] Network/firewall logs
-   [ ] DNS logs
-   [ ] Web logs
-   [ ] Document first investigations
