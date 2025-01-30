# OpenKit Documentation
## Overview
Latch offers a comprehensive access control system for property managers, allowing control over multiple properties and portfolios. It enables them to manage who has access to which spaces and when, view access history, and set up and manage multiple users and guests—all through Mission Control (our web manager).

OpenKit is our API and SDK set designed for partners who want to automate user creation in Latch, issue credentials to users, and specify the entrances residents can access along with their access schedules.

Latch offers a wide variety of locks, which, depending on the property, can be either internet-connected 24/7 or not connected at all. When you create a user via OpenKit and grant access to a door, they can use the Latch app, your app (through the SDK), and the doorcodes.

Certain types of access (like permanent doorcodes or mobile credentials) require the Latch backend to sync with the lock, meaning the lock must be connected to the internet or synced passively (which occurs when the lock is unlocked using the Latch app or when sync is triggered via the SDK). Other types of access (like daily doorcodes) do not require the lock to be online and will function without a sync. This is elaborated further in the users section of this documentation.

Currently, there is no testing or development environment available for partners to integrate with Latch services. While we can create a test property for you, we recommend purchasing devices to facilitate development and testing. If you can work with a property that already has devices or can obtain devices for development, please have the property or portfolio manager email Latch to request your addition as a partner for that property.

Since this is a production environment, partners must adhere to best practices when developing and testing applications and services. This is essential to maintain acceptable levels of traffic to the Latch backend. Please note that traffic from Partner backends will be closely monitored for unusual activity and may be rate-limited based on typical usage patterns.

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
