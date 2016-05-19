# List metrics by minInsertTime

List three metrics with `lastInsertTime` qual or greater than 2016-05-18T22:13:40.000Z

## Request
### URI
```elm
GET https://atsd_host:8443/api/v1/metrics?minInsertDate=2016-05-13T08:13:40&timeFormat=iso
```
## Response
```json
[
   {
      "name":"properties_pool_active_count",
      "enabled":true,
      "dataType":"FLOAT",
      "counter":false,
      "persistent":true,
      "timePrecision":"MILLISECONDS",
      "retentionInterval":0,
      "invalidAction":"NONE",
      "lastInsertTime":"2016-05-18T22:50:00.000Z",
      "versioned":false
   },
   {
      "name":"properties_queue_size",
      "enabled":true,
      "dataType":"FLOAT",
      "counter":false,
      "persistent":true,
      "timePrecision":"MILLISECONDS",
      "retentionInterval":0,
      "invalidAction":"NONE",
      "lastInsertTime":"2016-05-19T03:50:00.000Z",
      "versioned":false
   },
   {
      "name":"properties_rejected_count",
      "enabled":true,
      "dataType":"FLOAT",
      "counter":false,
      "persistent":true,
      "timePrecision":"MILLISECONDS",
      "retentionInterval":0,
      "invalidAction":"NONE",
      "lastInsertTime":"2016-05-19T10:07:54.749Z",
      "versioned":false
   }
]
```