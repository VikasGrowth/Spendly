---
description: Seed realistic dummy expenses for a specific user
argument-hint: "<user_id> <count> <months>"
allowed-tools: Read, Bash(python:*)
---

Read database/db.py to understand the expenses table
schema, the db connection pattern (get_db()), the
CATEGORIES list, and the database file name.

User input: $ARGUMENTS

## Step 1 - Parse arguments

Extract from $ARGUMENTS:
- user_id - integer, the user to add expenses for
- count - integer, number of expenses to create
- months - integer, how many past months to
  spread them across

If any argument is missing or not a valid positive
integer, stop and say:
"Usage: /seed-expense <user_id> <count> <months>
Example: /seed-expense 1 50 6"

## Step 2 - Check the user exists

Query the users table for user_id. If no user is
found, stop and say:
"No user found with id <user_id>. Run /seed-user first."
Do not insert anything.

## Step 3 - Generate and insert expenses

Write and run a Python script using Bash that creates
<count> expenses for this user:
- category: picked from the CATEGORIES list in db.py,
  so every category is used at least once when count
  allows it
- amount: realistic for an Indian user, in rupees,
  matched to the category (e.g. Food 80-1500,
  Transport 30-800, Bills 300-5000, Shopping
  500-8000, Health 200-4000, Entertainment
  150-2500, Other 50-2000). Round to 2 decimals.
- description: a short, realistic Indian context
  note (e.g. "Swiggy dinner", "Auto to office",
  "Electricity bill", "Apollo pharmacy",
  "BookMyShow tickets")
- date: a random date spread across the last
  <months> months up to today, stored in the same
  format the existing expenses use

Insert all rows in a single transaction using
get_db(), then commit and close the connection.

Do not modify any existing files. Do not touch
other users' expenses.

## Step 4 - Confirm

After running, report:
- The user's id and name
- Number of expenses inserted
- The date range covered
- Count and total amount per category
- The user's total number of expenses now