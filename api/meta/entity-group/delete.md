# Entity Group: Delete

## Description

Delete the specified entity group.

Member entities and their data is not affected by this operation.

## Request

| **Method** | **Path** | **Content-Type Header**|
|:---|:---|---:|
| DELETE | `/api/v1/entity-groups/{group}` | `application/json` |

### Path Parameters 

|**Name**|**Type**|**Description**|
|:---|:---|:---|
| group |string|Entity group name.|

## Response

### Fields

None.

## Example

### Request

#### URI

```elm
DELETE https://atsd_host:8443/api/v1/entity-groups/nmon-aix
```

#### Payload

None.

#### curl

```elm
curl https://atsd_host:8443/api/v1/entity-groups/nmon-aix \
  --insecure --verbose --user {username}:{password} \
  --request DELETE
```
  
### Response

None.

## Additional examples




