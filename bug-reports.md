>BUG REPORT BUG-PARA-001
## Bug Report BUG-PARA-001

| Field | Value |
| :--- | :--- |
| **Bug ID** | `BUG-PARA-001` |
| **Title** | Sign-in fails for all users when submitting valid credentials on the ParaBank home page |
| **Reported by** | Vidal Sombat R. |
| **Date reported** | 23 Oct 2026 |
| **Module / Feature** | Authentication — Customer Sign-In |
| **Severity** | Critical |
| **Priority** | High |
| **Status** | New |
| **Assigned to** | Unassigned |
| **Build / Version** | Not displayed in UI |
| **Environment** | Mozilla Firefox 158.0.1 (laptop), Windows 11, Production (Public Demo Site) |
| **Reproducibility** | Always (All attempts, both new and pre-registered accounts) |

### Preconditions
Two sets of credentials were used:
1. A user registered a moment earlier through `/register.htm`.
2. The pre-registered demo account `john` / `demo`.
Neither had an active session at the start of each attempt.

### Steps to reproduce
1. Navigate to `https://parabank.parasoft.com/parabank/index.htm`.
2. In **Username**, enter `john`.
3. In **Password**, enter `demo`.
4. Click **Log In**.
5. Repeat steps 2–4 using the newly registered account's credentials.

### Expected result
The user is authenticated and redirected to the authenticated area.

### Actual result
The login attempts return the message *"An internal error has occurred and has been logged."* No authentication and no redirection to the authenticated area occurs. User remains on the home page. 

### Evidence
- Screenshot of the home page displaying the error message.

### Impact
**Critical.** Authentication is the entry point for the entire application. No user can access the account overview, transactions, transfers, or any other secure services. 
There is no workaround: registration completes successfully, but produces an account that cannot be used because the authenticated area remains unreachable. 
This bug blocks all QA activity related to login, session management, and logout on this site. 

### Additional notes
This bug blocks all the potential test cases in Suite A that require an authenticated session.

>BUG REPORT BUG-PARA-002
## Bug Report BUG-PARA-002

| Field | Value |
| :--- | :--- |
| **Bug ID** | `BUG-PARA-002` |
| **Title** | Registration fails with HTTP 400 when username contains Swedish characters (ÅÄÖ) |
| **Reported by** | Vidal Sombat R. |
| **Date reported** | 23 Oct 2026 |
| **Module / Feature** | Registration — Username Input |
| **Severity** | Medium |
| **Priority** | Medium |
| **Status** | New |
| **Assigned to** | Unassigned |
| **Build / Version** | Not displayed in UI |
| **Environment** | Mozilla Firefox 158.0.1 (laptop), Windows 11, Production (Public Demo Site) |
| **Reproducibility** | Always |

### Preconditions

User is on the registration page (`/register.htm`). A username containing Swedish characters (Å, Ä, Ö) is to be used.

### Steps to reproduce

1. Navigate to `https://parabank.parasoft.com/parabank/register.htm`.
2. Click **Register**
3. In the **Username** field, enter: `TestÅsaÖ`
4. Fill in all other required fields with valid data.
5. Click **Register**.

### Expected result

The username is accepted, or rejected with a clear user-friendly explanation such as "Username contains invalid characters". The page does not display a technical error.

### Actual result

The form submission fails with an **HTTP 400 - Bad Request** error. The page displays:

*"Character decoding failed. Parameter [customer.username] with value [Test\*\*] has been ignored. Note that the name and value quoted here may be corrupted due to the failed decoding."*

The error page displays technical server details that should not be visible to users.

### Evidence

- Screenshot of the error page showing "HTTP Status 400 - Bad Request" and the message about character decoding failing.
- The error message shows the username as `Test**` instead of `TestÅsaÖ`.

### Impact

Users with non-Latin names — common across Sweden, Germany, France and many other markets — cannot register their names correctly. The error message is technical and meaningless to an end user. 

Workaround exists but is impractical: users must transliterate their own names (Å→A, Ö→O), which is not acceptable for real customers.

### Additional notes

Not related to BUG-PARA-001 (login failure); this occurs during registration, before any authentication takes place.

>BUG REPORT BUG-PARA-003
## Bug Report BUG-PARA-003

| Field | Value |
| :--- | :--- |
| **Bug ID** | `BUG-PARA-003` |
| **Title** | No error message shown when Phone field is left empty during registration |
| **Reported by** | Vidal Sombat R. |
| **Date reported** | 23 Oct 2026 |
| **Module / Feature** | Registration — Phone field |
| **Severity** | Low |
| **Priority** | Low |
| **Status** | New |
| **Assigned to** | Unassigned |
| **Build / Version** | Not displayed in UI |
| **Environment** | Mozilla Firefox 158.0.1 (laptop), Windows 11, Production (Public Demo Site) |
| **Reproducibility** | Always |

### Preconditions

User is on the registration page (`/register.htm`). All fields in the registration form are empty.

### Steps to reproduce

1. Navigate to `https://parabank.parasoft.com/parabank/register.htm`.
2. Click **Register**.
3. Leave all fields empty.
4. Click **Register**.

### Expected result

Field-level validation errors appear on all required fields, including **Phone #**, preventing submission until valid data is entered.

### Actual result

Validation errors appear for all required fields except **Phone #**. The Phone field shows no error message and appears to accept an empty value silently.

### Evidence

Screenshot of the registration form after clicking Register with all fields empty, showing error messages beside every field except Phone field.

### Impact

Low severity: the user can still submit the form once other fields are corrected, but the inconsistent validation behavior may confuse users.

### Additional notes
The registration form on `/register.htm` has no UI markers (such as asterisks) indicating which fields are mandatory, which is an industry standard for UI design. That contributes to user confusion regarding this bug. It also raises a usability concern: users may question why a site demands mandatory information if it fails to clearly inform about that.

>BUG REPORT BUG-SAUCE-001
## Bug Report BUG-SAUCE-001

| Field | Value |
| :--- | :--- |
| **Bug ID** | `BUG-SAUCE-001` |
| **Title** | Missing validation error for empty Password field when both Username and Password are blank |
| **Reported by** | Vidal Sombat R. |
| **Date reported** | 23 Oct 2026 |
| **Module / Feature** | Login — Validation |
| **Severity** | Low |
| **Priority** | Low |
| **Status** | New |
| **Assigned to** | Unassigned |
| **Build / Version** | Not displayed in UI |
| **Environment** | Mozilla Firefox 158.0.1 (laptop), Windows 11, Production (Public Demo Site) |
| **Reproducibility** | Always |

### Preconditions

User is on the login page (`https://www.saucedemo.com/`). All fields in the login form are empty.

### Steps to reproduce

1. Navigate to `https://www.saucedemo.com/`.
2. Ensure both **Username** and **Password** fields are empty.
3. Click the **Login** button.

### Expected result

- Validation error appears stating that username and password are required
- User stays on login page

### Actual result

Only one error message appears stating username is required. The Password field shows no error message, despite being empty. User stays on login page.

### Evidence

Screenshot of the login form after clicking Login with both fields empty, showing the error message stating username is required.

### Impact

Low severity: The user cannot log in, so the system remains secure. However, the inconsistent validation feedback is confusing and poor UX. It violates Nielsen's 9th usability rule, which requires that error messages clearly identify the problem and indicate the field at fault.

### Additional notes

This behavior is specific to the case where both fields are empty. If only the Password is empty (and Username is filled), the "Password is required" error appears correctly. 
