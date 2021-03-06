# Entity: Entity Groups

## Description

Retrieve a list of entity groups to which the specified entity belongs.

## Request

| **Method** | **Path** | 
|:---|:---|
| GET | `/api/v1/entities/{entity}/groups` |

### Path Parameters 

|**Name**|**Type**|**Description**|
|:---|:---|:---|
| `entity` |string|Entity name.|

## Response

An array of objects containing the following fields describing an entity group.

### Fields

| **Name** | **Type** | **Description** |
|:---|:---|:---|
| `name` | string| Entity group name. |
| `tags` | object | Entity group tags. |

## Example

### Request

#### URI

```elm
GET https://atsd_host:8443/api/v1/entities/nurswgvml007/groups
```

#### Payload

None.

#### curl

```elm
curl https://atsd_host:8443/api/v1/entities/nurswgvml007/groups \
  --insecure --verbose --user {username}:{password} \
  --request GET
```

### Response

```json
[
    {
        "name": "VMware VMs",
        "tags": {}
    },
    {
        "name": "environment-prod",
        "tags": {
            "tag1": "v1"
        }
    },
    {
        "name": "nmon-linux",
        "tags": {}
    },
    {
        "name": "nur-entities-name",
        "tags": {}
    }
    {
        "name": "nurswg-dc1",
        "tags": {}
    },
    {
        "name": "scollector-linux",
        "tags": {}
    },
    {
        "name": "scollector-nur",
        "tags": {}
    },
    {
        "name": "scollector-uptime",
        "tags": {}
    },
    {
        "name": "solarwind-vmware-vm",
        "tags": {}
    },
    {
        "name": "tcollector - linux",
        "tags": {}
    }
]
```

## Additional examples



