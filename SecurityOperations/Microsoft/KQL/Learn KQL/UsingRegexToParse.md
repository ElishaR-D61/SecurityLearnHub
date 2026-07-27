# How to parse  using regex
If you are struggling with parsing content out of a long syslog entry, this includes examples of Regex and how to parse into new column

```kql
Syslog 
| where Facility contains "local4" 
| where SyslogMessage contains "LOGSTASH" 
| extend Parser = extract_all(@"(\d+.\d+)\s([\w\-\_]+)\s([\w\-\_]+)\s([\S\s]+)$",dynamic([1,2,3,4,5,6,7]),SyslogMessage) 
| extend NewColumn1 = tostring(Parser[0]) 
| extend SrcIpAddr = extract(@"src=([0-9\.]+)\:",1,NewColumn1), 
SrcPortNumber = toint(extract(@"src=([0-9\.]+)\:(\d+)\s",2,NewColumn1)), 
DstIpAddr = extract(@"dst=([0-9\.]+)\:",1,NewColumn1), 
DstPortNumber = toint(extract(@"dst=([0-9\.]+)\:(\d+)\s",2,NewColumn1)), 
HttpRequestMethod = extract(@"request: (\w+)\s",1,NewColumn1), 
Url = extract(@"request: (\w+)\s(\S+)",2,NewColumn1) 
``` 
