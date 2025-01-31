# System Flow
1. [Door/Property Setup](#door-property-setup)
2. [Partner Backend](#authentication-for-sdk)
3. [Invite guests](#inviting-guests)

## Door/Property Setup
```mermaid
sequenceDiagram
    participant Property Manager
    participant Latch
    participant Partner
    participant User

    Property Manager->>Latch: Email Request to Add Partner to Property
    activate Latch
    Latch-->>Property Manager: Confirmation/Request Details
    deactivate Latch
    Latch->>Latch: Adds Partner to Property (in Mission Control)
    Property Manager->>Latch: Selects Doors for Partner Access (in Mission Control)
    Partner->>Latch: GET /doors (to see assigned doors)
    activate Partner
    Latch-->>Partner: List of Assigned Doors/Buildings
    deactivate Partner
    Partner->>Partner: Groups Doors as Needed
    Partner->>Latch: GET /users (to manage users)
    activate Partner
    Latch-->>Partner: User Management Interface/Data
    deactivate Partner
    Partner->>Latch: POST /users (invite/create user for door)
    activate Partner
    Latch-->>Partner: User Created/Invited
    deactivate Partner
    Partner->>Latch: (Optional) Request Doorcodes
    activate Partner
    Latch-->>Partner: Doorcodes
    deactivate Partner
    alt User Access Starts
        User->>Latch App/Partner App/Doorcode: Unlocks Door
        activate User
        Latch-->>User: Door Unlocked
        deactivate User
    end
    note right of Latch: Partner sees doors ONLY after Property Manager<br>grants access in Mission Control (door-by-door).<br>No "Unit" or "Key" concept in OpenKit.
    note right of Partner: User access starts based on schedule/grant type.
```

## Authentication for SDK
```mermaid
sequenceDiagram
    participant User
    participant Partner App
    participant Partner BE
    participant Latch Auth0
    participant Latch BE

    alt User-Scoped Flow
        User->>Partner App: Initiates process (via Partner App)
        Partner App->>Partner BE: Sends User Email & IP
        Partner BE->>Latch Auth0: POST /passwordless/start (Client ID/Secret, Email, IP)
        Latch Auth0-->>User: OTP Sent to User Email
        Partner App->>User: Prompts for OTP
        User->>Partner App: Enters OTP
        Partner App->>Partner BE: Sends User Email & OTP
        Partner BE->>Latch Auth0: POST /oauth/token (Client ID/Secret, Email, OTP)
        Latch Auth0-->>Partner BE: Access Token, Refresh Token
        Partner BE->>Partner App: Access Token
        Partner App->>Latch SDK: Initializes SDK with Access Token
        Latch SDK->>Latch BE: Unlock Request
        Latch BE-->>Latch SDK: Unlock Response
        Latch SDK-->>Partner App: Unlock Success/Failure
        Partner App->>User: Displays Unlock Status

        alt Access Token Expires
            Latch SDK->>Latch BE: Unlock Request (Expired Token)
            Latch BE-->>Latch SDK: TOKEN_EXPIRED Error
            Latch SDK-->>Partner App: TOKEN_EXPIRED Exception
            Partner App->>Partner BE: Requests New Access Token (Refresh Token)
            Partner BE->>Latch Auth0: POST /oauth/token (Client ID/Secret, Refresh Token)
            Latch Auth0-->>Partner BE: New Access Token, Refresh Token
            Partner BE->>Partner App: New Access Token
            Partner App->>Latch SDK: Re-initializes SDK with New Access Token
            Latch SDK->>Latch BE: Unlock Request (New Token)
            Latch BE-->>Latch SDK: Unlock Response
            Latch SDK-->>Partner App: Unlock Success/Failure
            Partner App->>User: Displays Unlock Status
        end
    else Partner-Scoped Flow
        Partner App->>Partner BE: Initiates Admin Action
        Partner BE->>Latch Auth0: POST /oauth/token (M2M Client ID/Secret)
        Latch Auth0-->>Partner BE: Access Token
        Partner BE->>Latch BE: Admin Request (with Access Token)
        Latch BE-->>Partner BE: Admin Response
        Partner BE->>Partner App: Displays Result
        alt Access Token Expires
          Partner BE->>Latch Auth0: POST /oauth/token (M2M Client ID/Secret)
          Latch Auth0-->>Partner BE: Access Token
          Partner BE->>Latch BE: Admin Request (with Access Token)
          Latch BE-->>Partner BE: Admin Response
          Partner BE->>Partner App: Displays Result
        end
    end
```

## Inviting guests
```mermaid
sequenceDiagram
    participant Partner App
    participant Latch SDK
    participant Latch BE

    alt Invite Guest
        Partner App->>Latch SDK: inviteGuests(firstName, lastName, email, phone, startTime, endTime, deviceUuids, passcodeType)
        activate Latch SDK
        Latch SDK->>Latch BE: Invite Guest Request (data from inviteGuests())
        activate Latch BE
        Latch BE-->>Latch SDK: InviteGuestsResult (Success/Error)
        deactivate Latch BE
        Latch SDK-->>Partner App: InviteGuestsResult
        deactivate Latch SDK
        alt Success
            Partner App->>Partner App: Displays Success Message (Guest Info)
        else Error
            Partner App->>Partner App: Displays Error Message (InviteErrorReason)
        end
    else Fetch Guests
        Partner App->>Latch SDK: guests()
        activate Latch SDK
        Latch SDK->>Latch BE: Fetch Guests Request
        activate Latch BE
        Latch BE-->>Latch SDK: GuestsResult (Success/Error)
        deactivate Latch BE
        Latch SDK-->>Partner App: GuestsResult
        deactivate Latch SDK
         alt Success
            Partner App->>Partner App: Displays List of Guests
        else Error
            Partner App->>Partner App: Displays Error Message (InviteErrorReason)
        end
    end
    note right of Partner App: Validation is the same as creating a user.<br>When a Resident loses access, so will all of their dependent Guests.
```
