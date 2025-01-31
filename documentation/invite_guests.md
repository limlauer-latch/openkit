# Invite guests
Partners and their End Users can grant Guest access (for non-Residents) through two methods: a Machine to Machine Server Integration or the Latch SDK within a Partner App. This page focuses on using the Latch SDK inside a Partner App.
> [!NOTE]
> It is important to note that when a  Resident loses access, so will all of their dependent Guests.

## 1. Latch SDK Guests
In order for Partners to allow End Users to share access via their Partner App, the following Latch SDK functions can be used.

### 1.1 Invite Guests
Guests can be invited via the SDK's `inviteGuests()` function:
```js
LatchSdk.inviteGuests(
  firstName: String,
  lastName:  String,   //Optional
  email:     String,
  phone:     String,   //Optional
  startTime: DateTime, 
  endTime:   DateTime, //Optional
  deviceUuids: [String], //UUID
  passcodeType: Enum,  // Permanent, Daily, DailySingleUse
): InviteGuestsResult
```
> [!NOTE]
> Validation is the same as creating a user - [Validation](https://github.com/limlauer-latch/openkit/blob/main/documentation/create_users_and_access.md#validation)

### 1.2 InviteGuestsResult

| Name                                     | Description                                                                                                                        |
|------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|
| Success (guest: Guest)                   | Invite succeeded with Guest information inside .guest                                                                              |
| Uninitialized                            | SDK is not initialized                                                                                                             |
| NetworkError (reason: InviteErrorReason) | Network Error with error reason being one of:“Email is required”, “Email or phone is required”, “Start time too far in the future” |
| InternalError                            | All other errors                                                                                                                   |

### 1.3 Fetch Guests
Guests for a given End User can be fetched  via the SDK's `guests()` function:

```js
LatchSdk.guests(
): GuestsResult
```

### 1.4 GuestsResult
|Name| Description|
|---|---|
|Success (guests: [Guest])|All guests information wrapped inside .guests|
|Uninitialized|SDK is not initialized|
|NetworkError (reason: InviteErrorReason)|Network Error with error reason being one of: “Email is required”, “Email or phone is required”, “Start time too far in the future”|
|InternalError|All other errors|
