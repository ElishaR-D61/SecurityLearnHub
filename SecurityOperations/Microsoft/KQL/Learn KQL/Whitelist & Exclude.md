# Whitelist & Exclude

**Dynamic Exclusion** - Best used in analytic rules
Dynamic lists can't use tostring or tolower so extend that first
```kql
let Exclusions = dynamic(["",""]); //
Table
| extend _lowerColumnA = tolower(tostring(ColumnA))
| where _lowerColumnA  !in (Exclusions)
```

**Static Exclusion** - Better for hunting than rules
```kql
let Exclusions =""; //
Table
| where tolower(tostring(ColumnA)) !in tolower(Exclusions)
```

**Both Static and Dynamic Exclusions** - this is ideal for building a hunting query where you may only know some IOCs
```kql
let SingleIOC = ""; //add if known. eg. a username
let MultipleIOCs = dynamic([""]); //add if known eg. urls or domains
//------ Search DeviceNetworkLogs ------//
| where (isempty(SingleIOC) or ColumnA == SingleIOC) //uses 'isempty' so that you can leave the IOC empty 
| where (isempty(MultipleIOCs) or tolower(tostring(ColumnB)) has tolower(MultipleIOCs))
```

**Exclude Dynamic Contains** - For excluding based on a contains
```kql
let Exclusions = dynamic(["",""]); //
Table
| where not(Column1 has_any (Exclusions))
```

**Parse Array to Search** - For parsing an array to make it easier to search it
```kql
let DestinationIPs = dynamic([""]); // add multiple e.g., dynamic(["1.1.1.1","8.8.8.8"])
Table
| extend  arrayColumnA_1 = tostring(parse_json(arrayColumnA)[1])
| where arrayColumnA_1 has_any (DestinationIPs))
| extend DestinationIP = arrayColumnA_1
```

**Array Match** - For matching a IOC to an array value 
```kql
let SingleIOC = ""; 
Table
| where parse_json(ColumnA)[1] == SingleIOC
```

## Examples
Example for searching for multiple destination IPs
```kql
let DestinationIPs = dynamic([""]); // add multiple e.g., dynamic(["1.1.1.1","8.8.8.8"])
//------ Search DeviceNetworkLogs ------//
DeviceNetworkEvents
| where array_length(DestinationIPs) == 0 or array_index_of(DestinationIPs, RemoteIP) != -1
```

Example for searching for multiple destination IPs when also using other parameters that may be empty
```kql
let SourceIP = "";
let DestinationIPs = dynamic([""]); // add multiple e.g., dynamic(["1.1.1.1","8.8.8.8"])
//------ Search DeviceNetworkLogs ------//
DeviceNetworkEvents
| where (isempty(SourceIP) or LocalIP == SourceIP)
| where (isnull(DestinationIPs) or array_length(DestinationIPs) == 0 or array_index_of(DestinationIPs, RemoteIP) != -1)
```

Example for searching for multiple urls that may only contain part of the url
```kql
let DestinationURLs = dynamic([""]);
Table
| where ColumnA has_any (DestinationURLs)
```

Example for searching for multiple urls that may only contain part of the url when also using other parameters that may be empty
```kql
let SourceIP = "";
let DestinationURLs = dynamic([""]);
Table
| where (isempty(SourceIP) or LocalIP == SourceIP)
| where (isnull(DestinationURLs) or array_length(DestinationURLs) == 0 or ColumnA has_any (DestinationURLs))
| extend DestinationURL = ColumnA
```
