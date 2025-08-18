smartsplit-php [master+] $ cat SPEC.md
SmartSplit is a basic CRUD application like SplitWise that lets users split expenses and figure out who needs to pay what to who.

Specifically it has the following features

* A user can sign up with a name, email address, and password
* A user can create a new SmartSplit and give it a name
* A user can add expenses which have a name, an optional description, and an amount
* When adding an expense, a user can specify who paid for it, and who it should be split with
* IMPORTANT: a user can specify that other users are the person who paid, not only the logged in user
* IMPORTANT A user can add others users as the payer or as people to split with even if they haven't joined yet
* For users who haven't joined yet, the user can select them by the invited email address
* Invites are not sent by email, there is no email. THey're just used for unique usernames to manage access
* Once they've joined and added their name, the name should be shown everywhere instead of the email address
* When adding a new expense, the default is that it's split between all users (joined and invited)
* The adder can remove some users if some of them did not take part in that expense
* All splits are always even for simplicity, divided equally between all people specified for that expense
* A user can invite another user to a SmartSplit by specifying their email address.
* Each SmartSplit gets a unique 8-digit alphanumeric code that makes easy to share URLs
* Any user can create a new smartsplit, or join an existing one if they're logged in with an email address that was invited
* Even if that user doesn't yet have an aaccount on SmartSplit, they'll have access to any SmartSplits they're added to after signing up (This bit is important). If the invitee has added their email address, they should have access automatically.
* Any user can add, remove, or edit any expenses within a SmartSplit that they have access to
* Any user can press 'Tally up' which should calculate who needs to make what payments to who to split everything

## Implementation details

* Email addresses are usernames, all registration and login is done with only an email and password
* Passwords are hashed but no extra secrutiy like length or weak passwords or confirmed passwords is applied
* Once a user has registered, they are automatically logged in
* The login and register flow is the same, but the user is registered if they don't have an account and logged in if they do

## Techncial details

* Use a single index.php script for the entire app.
* SQLite for all database functionality.
* No frameworks, just vanilla javascript and css
* No ORMs, use raw SQL
* use a clean minimalist elegant design that's mobile responsive