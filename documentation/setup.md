# Door setup
Once you have your partner credentials, you need to have access to properties. Your partner credentials allow you to create users and grant them access to all the **doors** you are assigned to.

1. A Property Manager (or a person with the necessary authority) requests Latch to add a partner to their property via email.[^1]
2. Latch adds the partner to the property.[^2]
3. In Mission Control, a Property Manager selects the doors they want the partner to have access to (door-per-door). _This means editing each door and adding the partner from the dropdown (Note: doors, not keys)_
4. Only then, the partner will see those doors in the list from the /doors endpoint.
5. You use the /doors endpoint to get a list of the buildings and doors you were assigned as a partner.
6. Group them as you see fit.[^3]
7. The partner uses the /users endpoint to invite users and schedule their access for these doors.
8. The partner creates/invites users to these doors
9. The user is created and you can log them in using the passwordless flow, or retrieve doorcodes
10. When the start date of the request comes, the user has access to the doors and can unlock them using the Latch app, partner app, doorcodes, etc.[^4]

[^1]: Send to: integrations@latch.com<br/>Example subject: Latch OpenKit - [property] - Add partner<br/>Example content: [property/company name] authorizes Latch to add <your_partner_name> as an OpenKit partner for the following property: [list of properties] beginning on <date>.<br/>**Note:** the property name should be the one displayed in Mission Control.
[^2]: This will add your partner name to the dropdown in the Door edit section, when the property manager selects it, you will see that door coming through the API.
[^3]: There is no **Unit** or **Key** concept in OpenKit, so it is up to the partner to group the doors as needed.
[^4]: This will depend on the type of access granted to the user
