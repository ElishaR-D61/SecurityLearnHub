For when the array has spaces in the name
```kql
| extend ClientIPAddress = parse_json(ExtendedProperties).["Client IP Address"]
```

For dynamic lists because they can't use tostring or tolower so extend that first
```kql
| extend LowerRemoteUrl = tolower(tostring(RemoteUrl))
```

For arrays that don't name values
```kql
| extend SourceIP = tostring(parse_json(SrcNatIpAddr)[1])
```
