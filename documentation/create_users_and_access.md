# Users and access
Partners can invite users, without the need of creating them ahead of time, and grant/revoke access to a set of doors. This will be done by using a partner-scoped token from the BE. If the user already exists, access will be added to their account, else it will be created and access will be added to their account.
> [!IMPORTANT]
> The two endpoints (`v1/users` and `v2/users`) have different **responses**, choose the one that works for your use case. The v2 section includes a table with the type of doorcode you will get depending on the access you grant and the lock connectivity.

|Version|Description|Methods|
|---|---|---|
|v1|Grants mobile and doorcode access to a user, response includes user UUID and doors|GET, POST, PATCH|
|v2|Same as v1, plus user & access information, and the doorcodes in the API response|POST|
> [!TIP]
> If you want to give a user mobile access or doorcodes (Optional: the user can receive the doorcodes and notification via an SMS/Email from Latch); and/or you want them to use the Latch app or your partner app, use the v1 endpoint.
> If you are trying to retrieve doorcodes directly from the API and display them yourself, use the v2 endpoint.
## Validation
|Access type|Requires|Notification (optional)|
|---|---|---|
|**PERMANENT**<br/>*(Type: mobile access)*<br/>Has a custom `start time` and (optional) `end time`. The customer can unlock the door(s) using the Latch App (including doorcodes) or the Partner App.|Email required. Phone number optional|Latch will send the Guest a Latch Email Invite.|
|**DAILY**<br/>*Type: doorcode*<br/>Doorcode that lasts for the entire calendar day|Either email or phone is required (not both).<br/>The start time must be either on the day of the request or the next day (No start time further in advance will be allowed).<br/>The exact start time on the day is not honored.<br/>Shareable must be `false`.|If email is provided, Latch will email the doorcode.<br/><br/>If the phone number is provided, Latch will text the doorcode unless there's a using with that phone number, in which case, Latch will send an email instead.|
|**DAILY_SINGLE_USE**<br/>*(Type: doorcode)*<br/>Doorcode that lasts for the entire calendar day, but expires after the first use|Either email or phone is required (not both). The start time must be either on the day of the request or the next day (No start time further in advance will be allowed).<br/>Shareable must be `false`.|If email is provided, Latch will email the doorcode.<br/><br/>If the phone number is provided, Latch will text the doorcode unless there's a using with that phone number, in which case, Latch will send an email instead.|
### Lock connectivity
- **PERMANENT:** The lock must be online or must be sync'd[^1] with the Latch BE after the access is granted and before it’s used.
- **DAILY:** the lock can be offline
- **DAILY_SINGLE_USE:** the lock can be offline

[^1]: Syncing a lock is done automatically if the lock is internet-connected, or passively when unlocking a door using the Latch app. You can also trigger a sync in your partner app using the SDK.

> [!IMPORTANT]
>- The `role` in the request does not bear relevance on the validation. This field field allows clients to classify their understanding of a User's role with respect to a certain Door, but does not imply a certain credential type or shareability.
>- The `role` field currently supports: `RESIDENT` and `NON_RESIDENT`.
>- Is there a way to customize the OTP email? Currently, no.
>- Is there a way to not send the email/sms notification? Yes, by setting the shouldNotify flag to false.
> [!WARNING]
> In v2/users, the `role` field decides if you are able to read a doorcode in the response. _There is more information in the "Doorcode Results in API Response" table._

# 1. Create users and grant access (using v1)
### 1.1 Request
|Endpoint|POST https://rest.latchaccess.com/access/sdk/v1/users  |
|--|--|
|Header|Authorization: Bearer {{access_token}}  |
|Request body:|*See below*
```HTML
{
   "firstName": "<string>",
   "lastName": "<string>",
   "email": "<string>",
   "phone": "<string>",
   "startTime": "<datetime>",  // e.g. "2022-09-30T15:11:02.537Z"
   "endTime": "<datetime>",    // e.g. "2022-09-30T15:11:02.537Z"
   "doorUuids": [
     "<string>",
     ...
   ],
   "shareable": <boolean>,
   "passcodeType": "PERMANENT" | "DAILY" | "DAILY_SINGLE_USE",
   "role": "RESIDENT" | "NON_RESIDENT",
   "shouldNotify": <boolean>
}
```
#### 1.1.2 Field descriptions
- `shareable`: controls whether the user is able to share this access with a guest
- `shouldNotify` (default `true`): controls whenever email notifications are sent to the invited user. The emails include the welcome email and/or the Doorcode email.
- `passcodeType`: `DAILY` or `DAILY_SINGLE_USE`
- `role`: `RESIDENT`
> [!TIP]
> If `shouldNotify` is set to `false` **no** emails are sent. In order to prevent invalid scenarios validations are put in place to prevent the following request situation since it would lead to the user having no way of accessing their Doorcode
### 1.2 Response
If the request was successful, the Partner BE will receive an HTTP 200 with the following fields:
```HTML
{
    "userUuid": "<string>",
    "doors": [
      "uuid": "<string>"
      "name": "<string>",
      "type": "DOOR" | "ELEVATOR",
      "buildingUuid": "<string>",
    ]
}
```
#### 1.2.1 Field descriptions
* `userUuid`: Unique identifier of the invited user and the list of doors the user has access to:
  * `uuid`: Unique-identifier of the door.
  * `name`: Name of the door.
  * `type`: Type of door. Possible values: "DOOR" or "ELEVATOR".
  * `buildingUuid`: Unique-identifier of the building where the door is located.

### 1.3 Errors
* `400 Bad Request`: missing/invalid parameters or invalid door UUIDs.
⇒ Check all the parameters are correct and check all the given doors are valid. Ensure at least 1 doorUuid is supplied in the request.
* `401 Unauthorized`: missing or invalid access token.
⇒ Check the token hasn't expired and refresh the token if needed.
* `500 Internal Server Error`: there was an unexpected error.
⇒ Contact Latch Support

# 2. Create users and grant access (using v2)
You can create/invite a user and get doorcodes directly in the response using this endpoint.
### 2.1 Request
|Endpoint|POST https://rest.latchaccess.com/access/sdk/v2/users  |
|--|--|
|Header|Authorization: Bearer {{access_token}}  |
|Request body:|*See below*|
```HTML
{
   "firstName": "<string>",
   "lastName": "<string>",
   "email": "<string>",
   "phone": "<string>",
   "startTime": "<datetime>",  // e.g. "2022-09-30T15:11:02.537Z"
   "endTime": "<datetime>",    // e.g. "2022-09-30T15:11:02.537Z"
   "doorUuids": [
     "<string>",
     ...
   ],
   "shareable": <boolean>,
   "passcodeType": "PERMANENT" | "DAILY" | "DAILY_SINGLE_USE",
   "role": "RESIDENT" | "NON_RESIDENT",
   "shouldNotify": <boolean>
}
```
> [!NOTE]
> Field descriptions for the request are the same as the v1 endpoint.
#### 2.1.2 Response
```HTML
{
   "email": "<string>",
   "firstName": "<string>",
   "lastName": "<string>",
   "userUuid": "<string>",
   "phone": "<string>"
   "accesses": [
     {
       "doorUuid: "<string>",
       "passcodeType": "<string>",
       "shareable": <boolean>,
       "startTime": "<string>",
       "endTime": "<string>",
       "granter": {
         "type": "<string>",
         "uuid": "<string>",
       },
       "role": "<string>",
       "doorcode": {
         "code": "<string>"          // e.g. "1234567"
         "description": "<string>"
       }
     },
     ...
   ]
}
```
### 2.2 Response
If the request was successful, the Partner BE will receive an HTTP 200 containing a User object, with the following fields:
* `email`: Email address associated with the user.
* `firstName`: First name of the user.
* `lastName`: Last name of the user.
* `userUuid`: Unique identifier of the user.
* `phone`: Phone number of the user. Can be `null`.
* `accesses`: List of doors the user has access to with the following fields:
  * `doorUuid`: Unique identifier of the door.
  * `passcodeType`: Indicates access type. Possible values are `PERMANENT`, `DAILY`, `DAILY_SINGLE_USE`.
  * `shareable`: Indicates whether user can share access to guests.
  * `startTime`: Start time of access to door.
  * `endTime`: End time of access to door.
  * `granter`: Indicates who granted access to door. Possible values are `PARTNER`, `USER`.
  * `role`: Classifies a type of user. Possible values are `RESIDENT`, `NON_RESIDENT`.
  * `doorcode`: Doorcode object with the following fields:
    * `code`: 7 digit code for guest access to unlock the door. Can be `null`.
    * `description`: A message to explain the code result with the following possible values:
      * `VALID`: Indicates a valid 7 digit doorcode is returned.
      * `COMMUNAL_DOORCODE_CONFLICT`: Indicates a user has permanent access to public doors granted by a property manager in Mission Control in a property with the common doorcodes feature enabled. The `code` field is `null`.
      * `USER_HAS_RESIDENT_ACCESS`: Indicates the user has resident access to the door granted via UserKit or Mission Control. The `code` field is `null`.
      * `USER_HAS_GUEST_ACCESS_CONFLICT`: Indicates the user already has guest permanent access to the door granted via Mission Control. The `code` field is `null`.

### 2.3 Doorcode Results in API Response
| Passcode Type    | Role         | Door Accessibility Type | Door Connection Status         | API Response                                                                                                                                                                                                 |
|------------------|--------------|-------------------------|--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PERMANENT        | NON_RESIDENT | COMMUNAL and/or PRIVATE | CONNECTED                      | A single 7 digit common doorcode for all communal doors that works for the entire duration of access.<br/><br/>A unique 7 digit doorcode for each private door that works for the entire duration of access. |
| PERMANENT        | NON_RESIDENT | COMMUNAL and/or PRIVATE | NOT CONNECTED                  | Same response as above.<br/><br/>NOTE: Permanent doorcodes will not unlock a non-connected door until a sync is performed. Daily doorcodes will work on non-connected doors w/o a sync.                      |
| PERMANENT        | RESIDENT     | COMMUNAL and/or PRIVATE | CONNECTED and/or NOT CONNECTED | No doorcodes.<br/><br/>Exposing resident doorcodes is not supported.                                                                                                                                         |
| DAILY            | NON_RESIDENT | COMMUNAL and/or PRIVATE | CONNECTED and/or NOT CONNECTED | A unique 7 digit doorcode for each door that works for the entire calendar day set to the timezone of the door.                                                                                              |
| DAILY            | RESIDENT     | COMMUNAL and/or PRIVATE | CONNECTED and/or NOT CONNECTED | No doorcodes.<br/><br/>Exposing resident doorcodes is not supported.                                                                                                                                         |
| DAILY_SINGLE_USE | NON_RESIDENT | COMMUNAL and/or PRIVATE | CONNECTED and/or NOT CONNECTED | A unique 7 digit doorcode for each door that works for the entire calendar day set to the timezone of the door.<br/><br/>However, it expires 15 minutes after first use.                                     |
| DAILY_SINGLE_USE | RESIDENT     | COMMUNAL and/or PRIVATE | CONNECTED and/or NOT CONNECTED | No doorcodes.<br/><br/>Exposing resident doorcodes is not supported.                                                                                                                                         |

### 2.4 Errors
> [!NOTE]
> Error messages for the request are the same as the v1 endpoint.

# 3. Revoking access
Partners can revoke user access to given doors. This will be done by using a partner-scoped token from the BE.
### 3.1 Request
|Endpoint|DELETE https://rest.latchaccess.com/access/sdk/v1/users/:user/doors/:door  |
|--|--|
|Header|Authorization: Bearer {{access_token}}  |
|Request body|empty|
|Response body|empty|

### 3.2 Response
If the request was successful, the Partner BE will receive an HTTP 200 and an **empty body** response.
### 3.3 Errors
In case of an error, the API will return the following error responses:
* `404 Not Found`: invalid user or door UUIDs. ⇒ Check the user and door identifiers.
* `401 Unauthorized`: missing or invalid access token. ⇒ Check the token hasn't expired and refresh the token if needed.
* `500 Internal Server Error`: there was an unexpected error. ⇒ Contact Latch Support

# 4. Patch access
Partners can update user access to given doors. This will be done by using a partner-scoped token from the BE.
### 4.1 Request
- `:user` ⇒ a valid user UUID
- `:door` ⇒ a valid door UUID

|Endpoint|PATCH https://rest.latchaccess.com/access/sdk/v1/users/:user/doors/:door|
|--|--|
|Header|Authorization: Bearer {{access_token}}  |
|Request body:|*See below*|
```html
{
	"shareable": <boolean>,
	"endTime": "<string>"
}
```

### 4.2 Field Descriptions
> [!IMPORTANT]
> Keep in mind that if `endTime` is omitted from the request body, the API will treat it as if the `endTime` was explicitly set to `null`. This is expected in order to support requests to remove expiration from access. To keep the existing `endTime`, simply add it to the request body.

#### Response
```html
{
   "email": "<string>",
   "firstName": "<string>",
   "lastName": "<string>",
   "userUuid": "<string>",
   "phone": "<string>"
   "accesses": [
     {
       "doorUuid: "<string>",
       "passcodeType": "<string>",
       "shareable": <boolean>,
       "startTime": "<string>",
       "endTime": "<string>",
       "granter": {
         "type": "<string>",
         "uuid": "<string>",
       },
       "role": "<string>",
       "doorcode": {
         "code": "<string>"          // e.g. "1234567"
         "description": "<string>"
       }
     },
     ...
   ]
}
```

### 4.3 Response fields description
If the request was successful, the Partner BE will receive an HTTP 200 containing a User object, with the following fields:
* `email`: Email address associated with the user.
* `firstName`: First name of the user.
* `lastName`: Last name of the user.
* `userUuid`: Unique identifier of the user.
* `phone`: Phone number of the user. Can be `null`.
* `accesses`: List of doors the user has access to with the following fields:
    * `doorUuid`: Unique identifier of the door.
    * `passcodeType`: Indicates access type. Possible values are `PERMANENT`, `DAILY`, `DAILY_SINGLE_USE`.
    * `shareable`: Indicates whether user can share access to guests.
    * `startTime`: Start time of access to door.
    * `endTime`: End time of access to door.
    * `granter`: Indicates who granted access to door. Possible values are `PARTNER`, `USER`.
    * `role`: Classifies a type of user. Possible values are `RESIDENT`, `NON_RESIDENT`.
    * `doorcode`: Doorcode object with the following fields:
        * `code`: 7 digit code for guest access to unlock the door. Can be `null`.
        * `description`: A message to explain the code result with the following possible values:
            * `VALID`: Indicates a valid 7 digit doorcode is returned.
            * `COMMUNAL_DOORCODE_CONFLICT`: Indicates a user has permanent access to public doors granted by a property manager in Mission Control in a property with the common doorcodes feature enabled. The `code` field is `null`.
            * `USER_HAS_RESIDENT_ACCESS`: Indicates the user has resident access to the door granted via UserKit or Mission Control. The `code` field is `null`.
            * `USER_HAS_GUEST_ACCESS_CONFLICT`: Indicates the user already has guest permanent access to the door granted via Mission Control. The `code` field is `null`.
### 4.4 Errors
In case of an error, the API will return the following error responses:
* `400 Bad Request`: invalid request ⇒ Check endTime is after current and start time. 
* `401 Unauthorized`: missing or invalid access token. ⇒ Check the token hasn't expired and refresh the token if needed.
* `404 Not Found`: invalid user or door UUIDs. ⇒ Check the user and door identifiers. Also check user has permanent access to the given door.
* `500 Internal Server Error`: there was an unexpected error. ⇒ Contact Latch Support
