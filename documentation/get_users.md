# Your users
You can see a list of your users and their access using the `/users` endpoint.
# 1. Fetching your users
Partners can fetch a list of their Users and their access. This will be done by using a partner-scoped token from the BE.
### Request
|Endpoint|GET https://rest.latchaccess.com/access/sdk/v1/users|
|--|--|
|Header|Authorization: Bearer {{access_token}}|
|Parameters|`pageToken: "<string>" (default is "0", first page)`|
||`pageSize: <integer> (default is 100)`|
|Body|Empty

### Response
```HTML
    {
      "users": [
        {
          "email": "<string>",
          "firstName": "<string>",
          "lastName": "<string>",
          "userUuid": "<string>",
          "accesses": [
            {
              "doorUuid": "<string>",
              "passcodeType": "<string>",
              "shareable": <boolean>,
              "startTime": "<string>",
              "endTime": "<string>",
              "granter": {
                "type": "<string>",
                "uuid": "<string>"
              },
              "role": "<string>",
              "doorcode": {
                "code": "<string>",         // e.g. "1234567"
                "description": "<string>"
              }
            },
            ...
          ]
        },
        ...
      ],
      "nextPageToken": "<string>"
    }
```
### Attributes
If the request was successful, the Partner BE will receive an HTTP 200 containing a list of User objects, with the following fields:
 -  `users`: List of users and their metadata. Each entry will include:
    * `email`: Email address associated with the user.
    * `firstName`: First name of the user.
    * `lastName`: Last name of the user.
    * `userUuid`: Unique identifier of the user.
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
    * `nextPageToken`: Token to fetch the next page. Expected value is `null` when there is no next page.
### Errors
   In case of an error, the API will return the following error responses:
- `401 Unauthorized`: missing or invalid access token.
     ⇒ Check the token hasn't expired and refresh the token if needed.
- `500 Internal Server Error`: there was an unexpected error.
     ⇒ Contact Latch Support
# 2. Fetch single user
Partners can fetch a single user and its access. This will be done by using a partner-scoped token from the BE.
### Request
|Endpoint|GET https://rest.latchaccess.com/access/sdk/v1/users/:user|
|--|--|
|Header|Authorization: Bearer {{access_token}}|
|Body|Empty|
### Response
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
### Attributes
If the request was successful, the Partner BE will receive an HTTP 200 containing a **User** object, with the following fields:
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
### Errors
In case of an error, the API will return the following error responses:
- `401 Unauthorized`: missing or invalid access token.
   ⇒ Check the token hasn't expired and refresh the token if needed.
- `404 Not Found`: invalid user.
   ⇒ Check the user identifier.
- `500 Internal Server Error`: there was an unexpected error.
   ⇒ Contact Latch Support
