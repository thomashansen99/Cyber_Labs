# Splunk  Lab 1 - SSH Attack Dashboard
In this lab, I used Splunk to analyze SSH authentication logs (`secure.log`) and learn SIEM basics. 
The purpose was to understand how brute force login attempts appear in logs, practice SPL searches, 
and build a dashboard that summarizes usernames, source IPs, and login outcomes (failed vs accepted).
This exercise is similar to what a real SOC analyst might do when investigating brute-force attempts on a production server.
---
## Queries

### Top Usernames in Failed Logins
```spl
index=main source="*secure.log" "Failed password"
| rex field=_raw "for (?:invalid user )?(?<user>\S+)"
| top limit=10 user
```
Searches through the log file for `"Failed password"` and then uses `rex` to extract the username that was attempted. 
The`(?:invalid user )?` part ignores the phrase “invalid user” so it only captures the actual usernames.
Then `top limit=10 user` shows the ten most commonly attacked usernames.

### Top Source IPs in Failed Logins
```spl
index=main source="*secure.log" "Failed password"
| rex field=_raw "from (?<src_ip>\d{1,3}(?:\.\d{1,3}){3})"
| top limit=10 src_ip
```
Finds all failed login attempts, then extracts the IP address after the word “from” into a field called `src_ip.`
This shows which IP addresses the attacks came from, and shows the top ten most used source IPs.

### Failed vs Accepted Over Time
```spl
index=main source="*secure.log" ("Failed password" OR "Accepted password")
| eval outcome = if(match(_raw,"Accepted password"), "accepted", "failed")
| timechart span=1h count by outcome
```
Searches for both failed and accepted logins.
The `eval` statement creates a new field called `outcome` that labels each event as “accepted” or “failed.”
Then `timechart span=1h count by outcome` counts how many of each type occurred in one-hour buckets,
and compares successful logins against brute force failures over time.

### Failed Logins by Source IP Over Time
```spl
index=main source="*secure.log" "Failed password"
| rex field=_raw "from (?<src_ip>\d{1,3}(?:\.\d{1,3}){3})"
| timechart span=1h limit=20 count by src_ip
```
Looks only at failed logins and extracts the IP addresses.
The `timechart` command then shows counts of failed logins over time, grouped by `src_ip.`
Adding `limit=20` displays the top 20 IPs as individual lines, the rest grouped into “OTHER.”
This chart shows which attacking IPs were active at different times.

## The Dashboard

![SSH Attack Dashboard](./screenshots/dashboard(1).png)
![SSH Attack Dashboard](./screenshots/dashboard(2).png)


- Top Usernames → showed attackers targeted root/admin/operator.
- Top IPs → showed multiple IPs, suggesting distributed brute-force.
- Failed vs Accepted → proved brute-force activity (overwhelming failures).
- IPs over time → attacks were sustained, not just a single burst.


## What did I learn
- There were far more failed logins than successful ones which clearly shows a brute force attack
- The most common usernames used in the attack were `root`, `admin`, and `operator`
- The attacker distributed their attack across a lot of different source IPs
- I learned how to use `stats`, `top`, `timechart`, and `rex` to analyze logs
- I learned how to save searches to a dashboard which makes it easier to see patterns
- I can now quickly detect brute-force attacks in SSH logs using Splunk

## Conclusion
This lab helped me build a foundation in Splunk searching, field extraction, and dashboards. 
I can now turn raw log data into meaningful insights about security events. 
In future labs, I plan to expand this by creating alerts and more complex dashboards to simulate a real SOC workflow.
