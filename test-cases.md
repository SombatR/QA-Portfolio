
# QA Test Suite: Authentication & Registration
**Tester:** Vidal Sombat R.
**Date:** 23 Oct 2026
**Objective:** Validate login, registration, session management, and localization (Swedish characters) across two practice environments.


## Suite A: ParaBank (Registration & Blocker Documentation)
**Target:** `https://parabank.parasoft.com/parabank/index.htm`
**Environment**  Mozilla Firefox 158.0.1 (laptop), Windows 11, Production (Public Demo Site) 
**Status:** Authentication is currently non-functional (see BUG-PARA-001). Registration is functional.
**Strategy:** Tested registration flows. Login/session tests blocked and moved to Suite B.

| ID | Title | Type | Priority | Preconditions | Steps | Expected Result | Actual Result | Status | Bug ID |
|---|---|---|---|---|---|---|---|---|---|
| TC-A01 | New user registers and is automatically logged in | Integration | High | Unique username/email available | 1. Click **Register**<br>2. Fill all required fields<br>3. Click **Register** | Registration confirmation shown, and the user is automatically logged into the authenticated area | Registration confirmation shown; user was logged into the authenticated area without second login | Pass | — |
| TC-A02 | Registered account cannot authenticate in subsequent session | Integration | High | Account created via TC-A01 | 1. Log out<br>2. Re-enter the credentials<br>3. Click **Log In** | User authenticated and redirected to the authenticated area | The user is not authenticated or redirected, an error message is displayed: "An internal error has occurred and has been logged" | Fail | BUG-PARA-001 |
| TC-A03 | Username with Swedish characters (ÅÄÖ) handled correctly | Boundary / Localization | Medium | Username `TestÅsaÖ` unused | 1. Click **Register**<br>2. Enter Username `TestÅsaÖ`<br>3. Fill remaining fields<br>4. Click **Register** | Accepted, or rejected with clear explanation; no encoding corruption | Rejected with HTTP 400 - Bad Request "Character decoding failed" error; "name and value quoted here may be corrupted due to the failed decoding" | Fail | BUG-PARA-002 |
| TC-A04 | Empty registration form blocks submission | Negative | High | — | 1. Leave all fields empty<br>2. Click **Register** | Field-level validation errors on all required fields | Field-level validation errors on all fields but **Phone #** | Fail | BUG-PARA-003 |
| TC-A05 | Password and Confirm Password mismatch rejected | Negative | High | — | 1. Click **Register**<br>2. Fill all required fields<br>3. Enter different passwords<br>4. Click **Register** | Validation error identifying mismatch; registration blocked | Validation error identifies mismatch. Both password fields are cleared, the entered values in the other fields are retained. | Pass | — |

## Suite B: Sauce Demo (Login, Session & Logout)
**Target:** `https://www.saucedemo.com/`
**Environment**  Mozilla Firefox 158.0.1 (laptop), Windows 11, Production (Public Demo Site) 
**Credentials:** `standard_user` / `secret_sauce` (for login tests), `locked_out_user` / `secret_sauce` (for locked-out account test)
**Scope:** Full login flow, negative testing, and session management.

| ID | Title | Type | Priority | Preconditions | Steps | Expected Result | Actual Result | Status | Bug ID |
|---|---|---|---|---|---|---|---|---|---|
| TC-B01 | User logs in with valid credentials and lands on inventory page | Functional | High | `standard_user` available | 1. Enter valid username<br>2. Enter valid password<br>3. Click **Login** | User is authenticated and redirected to inventory page; URL changes to /inventory.html | User is authenticated and redirected to inventory page; URL changes to /inventory.html | Pass | — |
| TC-B02 | Incorrect password rejected with error message | Negative | High | `standard_user` available | 1. Enter valid username<br>2. Enter incorrect password<br>3. Click **Login** | Error states username and password do not match any user; user stays on login screen | Error states username and password do not match any user in this service; user stays on login screen | Pass | — |
| TC-B03 | Locked-out account rejected with clear message | Negative | Medium | `locked_out_user` available | 1. Enter username `locked_out_user`<br>2. Enter password `secret_sauce`<br>3. Click **Login** | Message clearly identifies account as locked, and user stays on login screen | User stays on login screen, the error message says this user has been locked out | Pass | — |
| TC-B04 | Unregistered username rejected | Negative | Medium | No account registered with that username | 1. Enter unknown username<br>2. Enter any password<br>3. Click **Login** | Generic "Username and password do not match" error appears; message is identical to the response in TC-B02 so a real username cannot be distinguished from a fake one. | Error states username and password do not match any user in this service; user stays on login screen. The error message is identical to the one in TC-B02 | Pass | — |
| TC-B05 | Empty username and password fields block login with validation errors | Negative | High | — | 1. Leave both fields empty<br>2. Click **Login** | Validation error stating missing fields are required; user stays on login page | User stays on login page, only username error message appears saying username is required, there is no password error message | Fail | BUG-SAUCE-001 |
| TC-B06 | Blank username with filled-in password rejected | Negative | Medium | `standard_user` account exists | 1. Leave username blank<br>2. Enter existing password<br>3. Click **Login** | Validation error states that username is required, user stays on login page | Validation error states that username is required, user stays on login page | Pass | — |
| TC-B07 | Whitespace-only username is not authenticated | Boundary | Low | `standard_user` account exists | 1. Enter `"   "` (spaces) as username<br>2. Enter existing password<br>3. Click **Login** | Login fails; error stating that username and password do not match any user appears (since whitespace is not trimmed and is treated as an unknown username); user stays on login page | Error states username and password do not match any user in this service; user stays on login page | Pass | — |
| TC-B08 | Over-length username (>255 chars) not authenticated | Boundary | Medium | `standard_user` account exists | 1. Paste 300-character string as username<br>2. Enter existing password<br>3. Click **Login** | Login fails; no system crash; generic error appears indicating no such user exists | Error states username and password do not match any user in this service; user stays on login page | Pass | — |
| TC-B09 | Login succeeds after logout clears session | Functional | High | Authenticated session active, `standard_user` exists | 1. Click **Logout**<br>2. Verify return to login screen<br>3. Type `https://www.saucedemo.com/inventory.html` into the browser address bar<br>4. Observe redirect behavior<br>5. Re-enter valid credentials (`standard_user` / `secret_sauce`)<br>6. Click **Login** | Logout returns user to login page; direct navigation to `/inventory.html` redirects back to login page (since the session is cleared and inventory page is an authenticated area); re-login succeeds and logs the user in on inventory page | Logout returned user to login page; navigating directly to `/inventory.html` redirected back to login with an error message stating 'inventory.html' can only be accessed when logged in; re-login succeeded and landed on inventory page | Pass | — |
| TC-B10 | Back button after logout does not restore session | Security | High | Authenticated session active, `standard_user` exists | 1. Click **Logout**<br>2. Press browser **Back** button<br>3. Observe what page loads | Authenticated area (`/inventory.html`) not displayed; user is redirected to or remains on login page | Back button redirects to login page; error message states 'inventory.html' can only be accessed when logged in | Pass | — |

### Execution summary

| Metric | Count |
|:-------|:-----:|
| Total | 15 |
| Passed | 11 |
| Failed | 4 |
| Blocked | 0 |
| Not Run | 0 |
| Pass rate | 73.33% |