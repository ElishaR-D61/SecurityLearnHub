#Time Filtering
For before and after a timestamp
```kql
let selectedTimestamp = datetime('2025-12-05T03:29:57.845Z');
Table
| where TimeGenerated between ((selectedTimestamp - 3m) .. (selectedTimestamp + 15m))
| sort by TimeGenerated desc
| extend Relevance = iff(TimeGenerated == selectedTimestamp, "Selected event", iff(TimeGenerated < selectedTimestamp, "Earlier event", "Later event"))
| project-reorder Relevance
```

For setting time ranges as parameters
```kql
// ----Parameters----//
let tz = 'Australia/Sydney';
let now_local = datetime_utc_to_local(now(), tz);
let start_local = startofmonth(datetime_add('month', -1, now_local));
let end_local   = endofmonth(datetime_add('month', -1, now_local));
Table
// --- Convert to local once and reuse
| extend TimeGeneratedLocal = datetime_utc_to_local(TimeGenerated, tz)
// -- set reporting time range --
 | where TimeGeneratedLocal >= start_local and TimeGeneratedLocal <= end_local
```

For last x days
```kql
Table
| where TimeGenerated between (ago(180d) .. now())
```

For Last full month. Eg, October 1-31th
```kql
Table
| where TimeGenerated >= startofmonth(datetime_add("month",-1,now()))
| where TimeGenerated < startofmonth(now())
```

For second last full month. Eg, September 1-30th
```kql
Table
| where TimeGenerated >= startofmonth(datetime_add("month",-2,now()))
| where TimeGenerated < startofmonth(datetime_add("month",-1,now()))
```

For Last three full months . Eg, August 1st to October 31st
```kql
Table
| where TimeGenerated >= startofmonth(datetime_add("month",-1,now()))
| where TimeGenerated < startofmonth(now())
```

For start of last month until now
```kql
Table
| where TimeGenerated >= startofmonth(datetime_add("month",-1,now()))
| where TimeGenerated < now()
```



