# OpenKit Documentation
## Overview
Latch offers a comprehensive access control system for property managers, allowing control over multiple properties and portfolios. It enables them to manage who has access to which spaces and when, view access history, and set up and manage multiple users and guests—all through Mission Control (our web manager).

OpenKit is our API and SDK set designed for partners who want to automate user creation in Latch, issue credentials to users, and specify the entrances residents can access along with their access schedules.

Latch offers a wide variety of locks, which, depending on the property, can be either internet-connected 24/7 or not connected at all. When you create a user via OpenKit and grant access to a door, they can use the Latch app, your app (through the SDK), and the doorcodes.

Certain types of access (like permanent doorcodes or mobile credentials) require the Latch backend to sync with the lock, meaning the lock must be connected to the internet or synced passively (which occurs when the lock is unlocked using the Latch app or when sync is triggered via the SDK). Other types of access (like daily doorcodes) do not require the lock to be online and will function without a sync. This is elaborated further in the users section of this documentation.

Currently, there is no testing or development environment available for partners to integrate with Latch services. While we can create a test property for you, we recommend purchasing devices to facilitate development and testing. If you can work with a property that already has devices or can obtain devices for development, please have the property or portfolio manager email Latch to request your addition as a partner for that property.

Since this is a production environment, partners must adhere to best practices when developing and testing applications and services. This is essential to maintain acceptable levels of traffic to the Latch backend. Please note that traffic from Partner backends will be closely monitored for unusual activity and may be rate-limited based on typical usage patterns.

### Release Schedule
Latch teams will work to ensure that issues are resolved in a timely manner by providing hotfixes for Security and Sev1 issues that are either identified by internal Latch teams or by Partner teams.
Partners will be expected to update their App’s Latch SDK version within 90 days of release.

### Customers and Service Partners

Latch is focused on building great solutions to serve both our customers and our partners. 

For customers, we provide industry-leading, Latch-enabled hardware and intuitive software to provide the best end-user experience. For example, with Latch Manager, property managers can easily administer and remotely control all the Latch devices within their properties.

For partners, we are building world-class SDKs and APIs to support mutual customers by enabling Latch features for different management platforms, resident experiences, and building services. Through our SDKs and APIs, we can deliver new products to end-users by leveraging a combination of partner and Latch systems to manage Latch hardware.

| Type | Function |
| ---- | -------- |
| End User | Utilizes the Partner App with Unlock Kit to Unlock Latch devices. |
| Customer | Purchases and manages Latch devices. |
| Partner | Utilizes the Unlock Kit and User Kit. |
| Customer Partner | Purchases Latch devices and utilizes the Unlock Kit and User Kit. |

### Responsibilities
When implementing the Latch Unlock Kit and User Kit, Partners work together with Latch to provide a great user experience to their customers and end-users. Below is a listing of some of the customer and partner responsibilities. Customer Partner responsibilities are the sum of customer and partner responsibilities.

| Customer | Partner | Latch |
| -------- | ------- | ----- |
| Manage Physical Devices | Build client and server integration with Latch | Provide access to APIs, SDKs and documentation |
| Setup Doors and Keys | User Authentication | Partner / User Authorization |
| Assign Doors/Keys to Partners | User Management | JWT Token Provider |
| | JWT Token retrieval from Latch BE | Native IOS/Android SDKs |
| | Host App for Latch SDK | Secure SDK storage and communication |
| | Renew JWT Token | Provide and Cache Device Credentials |
| | Provide a great UI/UX | Encapsulate all BLE Interactivity |
| | Gather Bluetooth Permissions | Provide a list of Doors and Devices to App |
| | | Gather User Consent |
| | | Traffic Monitoring |

## Table of contents
1. [Readme](README.md)
2. [Partner credentials](credentials.md)
1. [Property and door setup](setup.md)
3. [Fetch your doors and properties](get_doors_and_properties.md)
4. [Fetch your users](get_users.md)
5. [Create users and manage access](create_users_and_access.md)
6. [Inviting guests](share_access.md)
7. [SDK: iOS Tutorial](ios_tutorial.md)
9. [SDK: Android Tutorial](android_tutorial.md)
11. [FAQs](faqs.md)
12. [Examples](exmaples.md)
13. [Changelog](changelog.md)
