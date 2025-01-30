# Properties and doors
Assuming the setup is completed for at least one property, you will see the properties and doors you were assigned through the `/buildings` and `/doors` endpoints.
# 1. Fetching your properties
Partners can fetch a list of their Buildings. This will be done by using a partner-scoped token from the BE.
### Request
|Endpoint|GET https://rest.latchaccess.com/access/sdk/v1/buildings  |
|--|--|
|Header|Authorization: Bearer {{access_token}}  |
|Body|Empty|
### Response
   ```HTML
   {
       "buildings": [
         {
            "uuid": "<string>",
            "name": "<string>",
            "address": {
              "addressLine1": "<string>",
              "addressLine2": "<string>",
              "addressLine3": "<string>",
              "city": "<string>",
              "country": "<string>",
              "postalCode": "<string>",
              "state": "<string>",
              "portfolio": {
                "uuid": "<string>",
                "name": "<string>"
              },
              "timezone": "<string>"
            }
         },
         ...
       ]
   }
   ```

### Attributes
If the request was successful, the Partner BE will receive an HTTP 200 with the following fields:
 - `buildings`: List of "buildings" and their metadata. Each entry will include:
	- `uuid`: Unique-identifier of the building.
  - `name`: Name of the building.
  - `address`: Location the building is located.
  - `portfolio`: Includes the name and uuid of the portfolio the building is part of.
  - `timezone`: Timezone the building is in. Format is "Area/Location" (e.g. "America/New_York") based on the TZ identifier from https://en.wikipedia.org/wiki/List_of_tz_database_time_zones.

### Errors
In case of an error, the API will return the following error responses:
 - `401 Unauthorized`: missing or invalid access token.
      ⇒ Check the token hasn't expired and refresh the token if needed.
 - `500 Internal Server Error`: there was an unexpected error.
      ⇒ Contact Latch Support

# 2. Fetching your doors
Get a list of all your assigned doors using a partner-scoped token. Can be filtered by property UUID.
### Request
|Endpoint|GET https://rest.latchaccess.com/access/sdk/v1/doors  |
|--|--|
|Header|Authorization: Bearer {{access_token}}  |
|Parameters|`pageSize: <integer>`   (by default returns all doors)
||`pageToken: "<string>"` (default is "1", first page)
||`buildingUuid: "<string>"` (for filtering results by the Building UUID)
|Body|Empty

> [!NOTE]
> It is highly recommended that you use pagination to avoid timeouts or any issues when retrieving a large set of doors.
### Response
   ```HTML
    {
      "doors": [
        {
            "uuid": "<string>",
            "name": "<string>",
            "type": "DOOR" | "ELEVATOR",
            "buildingUuid": "<string>",
            "accessibilityType": "COMMUNAL | "PRIVATE",
            "isConnected": <boolean>,
            "device": {
                "serialNumber": "<string>",
                "type": "<string>",
                "battery": {
                    "percentage": <int32>,
                    "lastUpdated": <int64>
                }
            }
        },
        ...
      ],
      "nextPageToken": "<string>"
    }
```

> [!NOTE]
> Doors of type ELEVATOR are currently not supported in OpenKit. Most elevators are set up as DOOR on Mission Control.*
### Attributes
If the request was successful, the Partner BE will receive an HTTP 200 with the following fields:
-  `doors`: List of "doors" and their metadata. Each entry will include:
      * `uuid`: Unique-identifier of the door.
      * `name`: Name of the door.
      * `type`: Type of door. Possible values: "DOOR" or "ELEVATOR".
      * `buildingUuid`: Unique-identifier of the building where the door is located.
      * `accessibilityType`: Indicates whether its a communal (entrance, amenities, etc.) or private door (e.g. unit)
      * `isConnected`: Indicates connection status of the door. If internet or hub connected, field is set to `true`.
      * `device`: Includes additional metadata about the physical lock device. **If null, it indicates the door is not yet activated.**
        * `serialNumber`: Serial number of the device.
        * `type`: Type of device. Possible values: 'M', 'R', 'R2', 'C', 'G'
        * `battery`: Battery information with the following fields:
          * `percentage`: Estimated percentage of battery left on the device.
          * `lastUpdated`: Indicates the last time the battery percentage was updated.
	* `nextPageToken`: Token to fetch the next page. Expected value is `null` when there is no next page.
### Errors
In case of an error, the API will return the following error responses:
- `401 Unauthorized`: missing or invalid access token.
		⇒ Check the token hasn't expired and refresh the token if needed.
- `500 Internal Server Error`: there was an unexpected error.
		⇒ Contact Latch Support
