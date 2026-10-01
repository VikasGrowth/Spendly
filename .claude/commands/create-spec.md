---
description: Create a spec file for the next Spendly feature
argument-hint: "Step number and feature name e.g. 2 registration"
allowed-tools: Read, Write, Glob
---

You are a senior developer planning a new feature for the
Spendly expense tracker. Always follow the rules in CLAUDE.md.

User input: $ARGUMENTS

## Step 1 - Parse the arguments
From $ARGUMENTS extract:

1. `step_number` - zero-padded to 2 digits: 2 → 02, 11 → 11

2. `feature_title` - human readable title in Title Case
   - Example: "Registration" or "Login and Logout"

3. `feature_slug` - file safe slug
   - Lowercase, kebab-case
   - Only a-z, 0-9 and -
   - Example: "registration" or "login-and-logout"

<!-- NOT VISIBLE IN VIDEO (lines ~22-38) - my version below -->
## Step 2 - Research the codebase
Read CLAUDE.md, app.py, database/db.py, the templates/ folder,
and all existing files in .claude/specs/ to understand what
already exists. Only plan what this step needs.

## Step 3 - Write the spec
Use this structure:

# Spec: <feature_title>

<!-- NOT VISIBLE IN VIDEO (lines ~40-70) - my version below -->
## Overview
What this feature does and why it's needed, in 2-3 sentences.

## Depends on
Which earlier steps must be complete first.

## Routes
Each new or changed route: method, URL, what it does, and
whether login is required.

## Database changes
New tables, columns or constraints. Write "None" if none.

## Templates
New templates to create and existing ones to modify.

## Files to change
List every file that will be created or modified.

<!-- FROM HERE ON MATCHES THE VIDEO (lines 72-91) -->
## Rules for implementation
Specific constraints Claude must follow.
Always include:
- No SQLAlchemy or ORMs
- Parameterised queries only
- Passwords hashed with werkzeug
- Use CSS variables - never hardcode hex values
- All templates extend base.html

## Definition of done
A specific testable checklist. Each item must be
something that can be verified by running the app.

## Step 4 - Save the spec
Save to: .claude/specs/<step_number>-<feature_slug>.md

## Step 5 - Report to the user
Print a short summary in this exact format:

Spec file: .claude/specs/<step_number>-<feature_slug>.md
Title: <feature_title>
Then tell the user:
"Review the spec at .claude/specs/<step_number>-<feature_slug>.md
then enter Plan Mode with Shift+Tab twice
to begin implementation."

Do not print the full spec in chat unless explicitly asked.