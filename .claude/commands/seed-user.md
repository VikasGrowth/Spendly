---
description: Create a single dummy user in the expense_tracker database
allowed-tools: Read, Bash(python:*)
---

Read database/db.py to understand the users table
schema and the get_db() helper.

Then write and run a Python script using Bash that:

1. Generates a realistic random Indian user using your
   own knowledge of common Indian names across regions:
   - Name: a realistic Indian first + last name
   - Email: derived from the name with a random 2-3 digit
     number suffix (e.g. rahul.sharma91@gmail.com)
   - Password: "password123" hashed with werkzeug's
     generate_password_hash
   - created_at: current datetime

2. Checks if the generated email already exists in the
   users table. If it does, generate a new one and try again.

3. Inserts the user into the users table using get_db()
   so it goes into expense_tracker.db in the project root.

4. Commits the change and closes the connection.

Do not modify any existing files. Do not add expenses
for this user.

After running, confirm with:
- The new user's id, name, and email
- The plain-text password (password123) so I can log in
- The total number of users now in the table