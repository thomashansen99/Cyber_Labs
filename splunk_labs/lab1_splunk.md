# Splunk  
  
```
index=_internal

```
This shows Splunk’s own logs  
  
```
index=_internal sourcetype=splunkd

```
Narrow down to logs from Splunk’s main engine  
  
```
index=_internal sourcetype=splunkd 
| table _time host sourcetype

```
This makes a table that shows only the time, host, and source type. This command is actually pretty cool how it laid everything out like that  
  
```
index=_internal
| stats count by sourcetype

```
Stats is the aggregator command, and then shows the totals for each sourcetype  
  
```
index=_internal
| timechart count by sourcetype

```
This made a line chart that shows activity over time broken down by sourcetype  
  
Freaking sick, love it. This is getting legit  
  
Ok so far we have pulled a log, filtered it, format it into a table, summarized it, and then visualized it with a line chart.   
  
  
# Continuing…  
```
index=_internal
|top component

```
Top shows the most frequent, and components are things Splunk is doing I guess (sorting and top)  
  
```
index=_internal
| timechart count by host

```
Each host is a separate line, but idrk what a host is in this situation. It looks like it’s going hour by hour but the only host seems to be my computer. “In a real SOC, this helps spot which server is noisy or spiking.”  
  
```
index=_internal log_level=ERROR
| stats count by component

```
Showed the five different errors I guess. And how many there were for each  
  
```
index=_internal
| stats count by host
| sort -count

```
The count of events for each host, and the sort puts them in descending order so the ones with the most are at the top. Depending on what a host is in an SOC context this could maybe help you find where the most errors or problems are coming from  
  
  
  
# Now with the first log file  
```
index=main source="secure.log"

```
Everything in the log file  
  
```
index=main source=“secure.log” “Failed password”

```
Just failed logins “This is just like sourcetype=splunkd, except now the filter is a keyword.”  
  
```
index=main source="secure.log" "Failed password
| table _time host source _raw

```
Putting it all into a table  
  
```
index=main source="secure.log" "Failed password"
| stats count

```
Total failed logins (summarizing)   
  
```
index=main source="secure.log" ("Failed password" OR "Accepted password")
| stats count by _raw

```
  
  
# Extracting Fields for Useful Insights  
  
```
index=main source="*secure.log" "Failed password"
| rex field=_raw "for (?:invalid user )?(?<user>\S+)"
| top limit=10 user

```
Shows the top ten most used usernames in failed logins  
  
```
index=main source="*secure.log" "Failed password"
| rex field=_raw "from (?<src_ip>\d{1,3}(?:\.\d{1,3}){3})"
| top limit=10 src_ip

```
Shows the top ten source IPs where the attacks came from   
  
```
index=main source="*secure.log" "Failed password"
| rex field=_raw "from (?<src_ip>\d{1,3}(?:\.\d{1,3}){3})"
| timechart span=1h count by src_ip

```
Each of the IPs gets its own line in the line chart so you can see when each IP did its attacking. It looks like it automatically gave the top ten IPs their own line/catagory and put the rest into “OTHER” so unfortunately the line graph just shows that the vast majority of the attacks came from OTHER which I guess means that the attacker used a lot of different IP addresses.  
  
```
index=main source="*secure.log" ("Failed password" OR "Accepted password")
| eval outcome = if(match(_raw,"Accepted password"), "accepted", "failed")
| rex field=_raw "from (?<src_ip>\d{1,3}(?:\.\d{1,3}){3})"
| timechart span=1h count by outcome

```
Puts it all together(?) although it doesn’t display anything with the IP addresses I guess. Just gives a clean comparison of successful vs failed login attempts across time.  
  
  
  
  
  
  
  
