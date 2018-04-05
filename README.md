# Tetration Analytics User App
## Rogue services finder
### Purpose
This Tetration User App (PySpark) finds misconfigured servers using out of IT policy services (DNS servers, NTP, ...). By uploading to Tetration Data Lake the list of IT official providers, Tetration can compare with all DC / Cloud flows and find if servers are using other providers, and obviously which one.

### Requirements
This User App (as always) runs in the context of a Scope of servers. If used for alerting, a Kafka broker must be configured as a Data Tap.

### Usage
Some configuration has to be provided:
```
# Configuration
# Kafka TAP to send alert to
# Keep it empty if you don't want to send an alert and just get result
kafka_target = "Kafka-TAP"

# To identify the service in the alert message
service_type = "DNS"

# Port of the service (53 for DNS, 123 for NTP, ...)
port = "53"

# Procotol, UDP vs TCP
protocol = "UDP"
```

And reference file has to be uploaded to Tetration Datalake. It must be a single colmumn .csv file (dns.csv is a sample), like following:
```
dns_servers
8.8.8.8
192.168.0.1
```

This can be done in the UI:
![Datalake upload](img.png?raw=true "Datalake upload")
Tetration takes care of the versioning of the file, and the user app will always use latest version (by default).
```
# Datalake directory where the official list is uploaded using "Data platform --> Data Lake" in the shared zone
directory = "/shared/services-list/dns"

# CSV file must be single column, with this header. Ex of a valid CSV file (without """ btw):
"""
dns_servers
8.8.8.8
192.168.0.1
"""
field_name = "dns_servers"
```

The .ipynb (Jupyter notebook) file has to be imported into Tetration User App module

### Sample output
#### Please note that these are lab workloads running on purpose
```
Unofficial DNS providers: ['10.1.1.49']
Badly configured consumers: ['10.49.166.92' '10.49.166.81' '10.49.166.93' '10.49.166.82' '10.49.166.83' '10.49.166.95' '10.49.166.90' '10.49.166.96' '10.49.166.91']
```
```json
{
    "message_desc": "rogue DNS servers usage",
    "message_timestamp": "2018-04-05 13:22:27 UTC",
    "message_type": "alert",
    "src_dst": [
        {
            "Consumer": "10.49.166.92",
            "Provider": "10.1.1.49"
        },
        {
            "Consumer": "10.49.166.81",
            "Provider": "10.1.1.49"
        },
        {
            "Consumer": "10.49.166.93",
            "Provider": "10.1.1.49"
        },
        {
            "Consumer": "10.49.166.82",
            "Provider": "10.1.1.49"
        },
        {
            "Consumer": "10.49.166.83",
            "Provider": "10.1.1.49"
        },
        {
            "Consumer": "10.49.166.95",
            "Provider": "10.1.1.49"
        },
        {
            "Consumer": "10.49.166.90",
            "Provider": "10.1.1.49"
        },
        {
            "Consumer": "10.49.166.96",
            "Provider": "10.1.1.49"
        },
        {
            "Consumer": "10.49.166.91",
            "Provider": "10.1.1.49"
        }
    ]
}
```

### Related information
- Cisco Tetration Analytics: https://www.cisco.com/go/tetration
- PySpark: https://spark.apache.org/docs/1.6.2/api/python/index.html
- Pandas: http://pandas.pydata.org/pandas-docs/stable/
