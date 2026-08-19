# Test Cases: POST /api/register

**Feature:** FR01 | **Endpoint:** `POST /api/register`  
**Total:** 64 TCs | **Data-driven eligible:** 23 TCs  
**Contract:** `docs/apis/post-register/CONTRACT.md` | **OpenAPI:** `docs/openapi/paths/auth/register.yaml`  
**Generated:** 2026-08-19

## TC-FR — Functional / Domain Testing

> Tests input equivalence partitions and boundary values for every request field.

| ID             | Title                                                               | Precondition                              | Input                                                                                 | Expected Result                                                     | Data-driven? | Status | Actual Result |
| -------------- | ------------------------------------------------------------------- | ----------------------------------------- | ------------------------------------------------------------------------------------- | ------------------------------------------------------------------- | ------------ | ------ | ------------- |
| TC-FR01-FR-001 | Register account with all valid fields                              | No existing user with same email          | name: "Nguyen Van A", email: "test_new@domain.com", password: "Password123!"          | 200 OK — `{message: "User registered successfully", id: <integer>}` | Yes          | —      | —             |
| TC-FR01-FR-002 | Register account with name at minimum length (1 char)               | No existing user with same email          | name: "A", email: "min1@domain.com", password: "Password123!"                         | 200 OK — registration successful                                    | Yes          | —      | —             |
| TC-FR01-FR-003 | Register account with name at maximum length (100 chars)            | No existing user with same email          | name: "A"×100, email: "max100@domain.com", password: "Password123!"                   | 200 OK — registration successful                                    | Yes          | —      | —             |
| TC-FR01-FR-004 | Register account with name exceeding maximum length (101 chars)     | None                                      | name: "A"×101, email: "over100@domain.com", password: "Password123!"                  | 400 Bad Request — validation error                                  | Yes          | —      | —             |
| TC-FR01-FR-005 | Register account with name as empty string                          | None                                      | name: "", email: "empty-name@domain.com", password: "Password123!"                    | 400 Bad Request — name cannot be empty                              | Yes          | —      | —             |
| TC-FR01-FR-006 | Register account with name consisting of whitespace only            | None                                      | name: " ", email: "ws-name@domain.com", password: "Password123!"                      | 400 Bad Request — name cannot be whitespace-only                    | Yes          | —      | —             |
| TC-FR01-FR-007 | Register account with name containing Vietnamese diacritics         | No existing user with same email          | name: "Nguyễn Văn An", email: "vn-name@domain.com", password: "Password123!"          | 200 OK — registration successful                                    | Yes          | —      | —             |
| TC-FR01-FR-008 | Register account with invalid email format (missing @ symbol)       | None                                      | name: "Test User", email: "invalidemail.com", password: "Password123!"                | 400 Bad Request — invalid email format                              | Yes          | —      | —             |
| TC-FR01-FR-009 | Register account with invalid email format (missing domain)         | None                                      | name: "Test User", email: "user@", password: "Password123!"                           | 400 Bad Request — invalid email format                              | Yes          | —      | —             |
| TC-FR01-FR-010 | Register account with invalid email format (missing local part)     | None                                      | name: "Test User", email: "@domain.com", password: "Password123!"                     | 400 Bad Request — invalid email format                              | Yes          | —      | —             |
| TC-FR01-FR-011 | Register account with email at maximum length (254 chars)           | No existing user with same email          | name: "Test User", email: "a"×242+"@domain.com" (254 total), password: "Password123!" | 200 OK — registration successful                                    | Yes          | —      | —             |
| TC-FR01-FR-012 | Register account with email exceeding maximum length (255 chars)    | None                                      | name: "Test User", email: "a"×243+"@domain.com" (255 total), password: "Password123!" | 400 Bad Request — email too long                                    | Yes          | —      | —             |
| TC-FR01-FR-013 | Register account with password at minimum length (8 chars)          | No existing user with same email          | name: "Test User", email: "pass-min8@domain.com", password: "Pass1!aB"                | 200 OK — registration successful                                    | Yes          | —      | —             |
| TC-FR01-FR-014 | Register account with password below minimum length (7 chars)       | None                                      | name: "Test User", email: "pass-min7@domain.com", password: "Pa1!aBc"                 | 400 Bad Request — password too short                                | Yes          | —      | —             |
| TC-FR01-FR-015 | Register account with password at maximum length (128 chars)        | No existing user with same email          | name: "Test User", email: "pass-max128@domain.com", password: "Password1!"+"a"×118    | 200 OK — registration successful                                    | Yes          | —      | —             |
| TC-FR01-FR-016 | Register account with password exceeding maximum length (129 chars) | None                                      | name: "Test User", email: "pass-max129@domain.com", password: "Password1!"+"a"×119    | 400 Bad Request — password too long                                 | Yes          | —      | —             |
| TC-FR01-FR-017 | Register account with password missing uppercase letter             | None                                      | name: "Test User", email: "no-upper@domain.com", password: "password1!"               | 400 Bad Request — password policy violation                         | Yes          | —      | —             |
| TC-FR01-FR-018 | Register account with password missing lowercase letter             | None                                      | name: "Test User", email: "no-lower@domain.com", password: "PASSWORD1!"               | 400 Bad Request — password policy violation                         | Yes          | —      | —             |
| TC-FR01-FR-019 | Register account with password missing digit                        | None                                      | name: "Test User", email: "no-digit@domain.com", password: "Password!"                | 400 Bad Request — password policy violation                         | Yes          | —      | —             |
| TC-FR01-FR-020 | Register account with password missing special character            | None                                      | name: "Test User", email: "no-special@domain.com", password: "Password1"              | 400 Bad Request — password policy violation                         | Yes          | —      | —             |
| TC-FR01-FR-021 | Register account with duplicate email (same case)                   | User with "test@eshop.com" already exists | name: "Other User", email: "test@eshop.com", password: "Password123!"                 | 409 Conflict — email already registered                             | Yes          | —      | —             |
| TC-FR01-FR-022 | Register account with duplicate email (different case)              | User with "test@eshop.com" already exists | name: "Other User", email: "TEST@ESHOP.COM", password: "Password123!"                 | 409 Conflict — case-insensitive uniqueness                          | Yes          | —      | —             |
| TC-FR01-FR-023 | Register account with role field in request body                    | None                                      | name: "Hacker", email: "hacker@domain.com", password: "Password123!", role: "admin"   | 200 OK — role field ignored; user created with role="user"          | No           | —      | —             |

## TC-ST — State Transition Testing

> Tests HTTP response states, system data state changes, and field-level state preconditions.

| ID             | Title                                                                                  | Initial State                | Input                                                                      | Expected Response | Expected System State                                 | Data-driven? | Status | Actual Result |
| -------------- | -------------------------------------------------------------------------------------- | ---------------------------- | -------------------------------------------------------------------------- | ----------------- | ----------------------------------------------------- | ------------ | ------ | ------------- |
| TC-FR01-ST-001 | Verify user record is created with correct defaults on successful registration         | No user record exists        | name: "New User", email: "st-newuser@domain.com", password: "Password123!" | 200 OK            | New row inserted: role="user", login_attempts=0, etc. | No           | —      | —             |
| TC-FR01-ST-002 | Verify no user record is created when registration fails due to missing required field | None                         | name: null, email: "noname@domain.com", password: "Password123!"           | 400 Bad Request   | No new row inserted in `users` table                  | No           | —      | —             |
| TC-FR01-ST-003 | Verify response transitions to 409 when email already exists in system                 | User "test@eshop.com" exists | name: "Another User", email: "test@eshop.com", password: "Password123!"    | 409 Conflict      | No new user row created                               | Yes          | —      | —             |
| TC-FR01-ST-004 | Verify response transitions to 400 when request has invalid email format               | None                         | name: "Test User", email: "not-an-email", password: "Password123!"         | 400 Bad Request   | No new row inserted in `users` table                  | No           | —      | —             |
| TC-FR01-ST-005 | Verify response transitions to 400 when password fails complexity policy               | None                         | name: "Test User", email: "badpass@domain.com", password: "weakpassword"   | 400 Bad Request   | No new row inserted in `users` table                  | No           | —      | —             |
| TC-FR01-ST-006 | Verify response transitions to 400 for malformed JSON request                          | None                         | Raw body: `{name: "Test"` (invalid syntax)                                 | 400 Bad Request   | No new row inserted in `users` table                  | No           | —      | —             |

## TC-SEC — Security Testing

> Tests resistance to OWASP API Security Top 10 attack vectors as specified in the contract's security rules.

| ID              | Title                                                        | Attack Type      | Payload / Vector                                                         | Expected Result                                   | Security Rule  | Status | Actual Result |
| --------------- | ------------------------------------------------------------ | ---------------- | ------------------------------------------------------------------------ | ------------------------------------------------- | -------------- | ------ | ------------- |
| TC-FR01-SEC-001 | Verify password is not stored as plaintext in database       | Insecure storage | name: "SecTest", email: "sectest@domain.com", password: "Password123!"   | 200 OK — DB `password` must not be "Password123!" | SEC-01         | —      | —             |
| TC-FR01-SEC-002 | Verify response body does not expose password field          | Data exposure    | name: "SecTest2", email: "sectest2@domain.com", password: "Password123!" | 200 OK — no `password` in body                    | SEC-01b        | —      | —             |
| TC-FR01-SEC-003 | Verify XSS payload in name field is not executed when stored | XSS              | name: "<script>alert(1)</script>"                                        | 200 OK — value safely stored                      | SEC-04         | —      | —             |
| TC-FR01-SEC-004 | Verify XSS payload via img tag in name field                 | XSS              | name: "<img src=x onerror=alert(1)>"                                     | 200 OK — value safely stored                      | SEC-04         | —      | —             |
| TC-FR01-SEC-005 | Verify SQL injection in email field does not cause data leak | SQLi             | email: "' OR '1'='1"                                                     | 400 Bad Request — no side effect                  | SEC-05         | —      | —             |
| TC-FR01-SEC-006 | Verify SQL injection via DROP TABLE is blocked               | SQLi             | email: "'; DROP TABLE users; --"                                         | 400 or 500 — `users` table exists                 | SEC-05         | —      | —             |
| TC-FR01-SEC-007 | Verify SQL injection in name field is blocked                | SQLi             | name: "'; SELECT \* FROM users;--"                                       | 200 OK or 400 — parameterized query               | SEC-05b        | —      | —             |
| TC-FR01-SEC-008 | Verify role escalation via role field is blocked             | Role Escalation  | role: "admin"                                                            | 200 OK — role="user" in DB                        | SEC-06         | —      | —             |
| TC-FR01-SEC-009 | Verify error response does not expose raw SQL messages       | Misconfig        | Trigger DB error                                                         | 500 — body must NOT contain SQL text              | Sensitive Data | —      | —             |
| TC-FR01-SEC-010 | Verify Content-Type header is application/json               | Misconfig        | Valid request                                                            | 200 OK — `Content-Type: application/json`         | API8:2023      | —      | —             |

## TC-SCH — Schema Validation

> Tests that every response matches its documented schema.

| ID              | Title                                              | Scenario  | Field / Header Checked | Expected Value / Type         | Status | Actual Result |
| --------------- | -------------------------------------------------- | --------- | ---------------------- | ----------------------------- | ------ | ------------- |
| TC-FR01-SCH-001 | Verify success response has required message field | Success   | `message`              | string                        | —      | —             |
| TC-FR01-SCH-002 | Verify success response has required id field      | Success   | `id`                   | integer                       | —      | —             |
| TC-FR01-SCH-003 | Verify success response omits password             | Success   | `password`             | absent                        | —      | —             |
| TC-FR01-SCH-004 | Verify success response has no extra fields        | Success   | body                   | `additionalProperties: false` | —      | —             |
| TC-FR01-SCH-005 | Verify success response Content-Type header        | Success   | `Content-Type`         | `application/json`            | —      | —             |
| TC-FR01-SCH-006 | Verify 400 error body has error field              | 400 Error | `error`                | string                        | —      | —             |
| TC-FR01-SCH-007 | Verify 409 error body has error field              | 409 Error | `error`                | string                        | —      | —             |
| TC-FR01-SCH-008 | Verify 500 error body has error field              | 500 Error | `error`                | string                        | —      | —             |

## TC-ERR — Error Handling

> Tests graceful handling of malformed, missing, and type-incorrect inputs.

| ID              | Title                                               | Scenario        | Expected Status | Expected Error                  | Status | Actual Result |
| --------------- | --------------------------------------------------- | --------------- | --------------- | ------------------------------- | ------ | ------------- |
| TC-FR01-ERR-001 | Register account with completely empty request body | Empty JSON `{}` | 400 Bad Request | Error message                   | —      | —             |
| TC-FR01-ERR-002 | Register account with no request body               | Null body       | 400 Bad Request | Error message                   | —      | —             |
| TC-FR01-ERR-003 | Register account with invalid JSON syntax           | Invalid JSON    | 400 Bad Request | Parse error                     | —      | —             |
| TC-FR01-ERR-004 | Register account with wrong Content-Type            | `text/plain`    | 400 Bad Request | Error message                   | —      | —             |
| TC-FR01-ERR-005 | Register account with name field as integer         | name: 12345     | 400 Bad Request | Type error                      | —      | —             |
| TC-FR01-ERR-006 | Register account with email field as integer        | email: 12345    | 400 Bad Request | Type error                      | —      | —             |
| TC-FR01-ERR-007 | Register account with password field as boolean     | password: true  | 400 Bad Request | Type error                      | —      | —             |
| TC-FR01-ERR-008 | Register account with name field missing            | No name         | 400 Bad Request | Error: name is required         | —      | —             |
| TC-FR01-ERR-009 | Register account with email field missing           | No email        | 400 Bad Request | Error: email is required        | —      | —             |
| TC-FR01-ERR-010 | Register account with password field missing        | No password     | 400 Bad Request | Error: password is required     | —      | —             |
| TC-FR01-ERR-011 | Register account with all fields set to null        | All fields null | 400 Bad Request | Error: all required             | —      | —             |
| TC-FR01-ERR-012 | Register account with unknown extra fields          | extra: "val"    | 200 or 400      | Extra field ignored or rejected | —      | —             |

## TC-IDP — Idempotency

> Tests repeated identical request behavior.

| ID              | Title                                           | Description                 | First Call Result | Repeat Call Result | Status | Actual Result |
| --------------- | ----------------------------------------------- | --------------------------- | ----------------- | ------------------ | ------ | ------------- |
| TC-FR01-IDP-001 | Verify second registration returns 409 conflict | Send identical POST twice   | 200 OK            | 409 Conflict       | —      | —             |
| TC-FR01-IDP-002 | Verify registration request is not idempotent   | Send two different requests | 200 OK            | 200 OK             | —      | —             |

## TC-RL — Rate Limiting

> Tests rate limit enforcement.

| ID             | Title                                           | Request Pattern          | Expected Behavior after Limit | Status | Actual Result |
| -------------- | ----------------------------------------------- | ------------------------ | ----------------------------- | ------ | ------------- |
| TC-FR01-RL-001 | Verify 5 registration requests succeed          | 5 requests within 1 hour | All succeed with 200 OK       | —      | —             |
| TC-FR01-RL-002 | Verify 6th registration request is rate limited | 6 requests within 1 hour | 6th request returns 429       | —      | —             |
| TC-FR01-RL-003 | Verify rate limit resets after cooldown         | Wait 1 hour after 429    | Request returns 200 OK        | —      | —             |

## Manual Extension TCs — 5 Additional Cases

| TC ID           | Category              | Why AI typically misses this                                                                                                                                                                                                                                                                 |
| --------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| TC-FR01-FR-022  | FR — Functional       | AI typically generates duplicate checks based on exact string matches (case-sensitive). Testing for case-insensitive uniqueness requires inferring implicit business rules regarding email normalization, a logical step that AI frequently overlooks.                                       |
| TC-FR01-SEC-001 | SEC — Security        | AI tends to focus purely on black-box HTTP responses. Consequently, it often misses or avoids specifying system-level verification methods, such as directly querying the database to ensure the password was properly hashed and not stored as plaintext.                                   |
| TC-FR01-ST-002  | ST — State Transition | While AI correctly handles Positive Data States (verifying record creation on success), it frequently forgets to assert the "Negative Data State" on validation failures (e.g., HTTP 400), omitting the crucial check that no garbage records were inadvertently inserted into the database. |
| TC-FR01-IDP-002 | IDP — Idempotency     | When prompted for Idempotency, AI rigidly applies the "duplicate requests cause 409 Conflict" pattern. It routinely forgets the complementary test case: proving that two completely distinct requests must be handled independently and result in two separate successful records.          |
| TC-FR01-RL-003  | RL — Rate Limiting    | AI easily generates the boundary test for hitting the limit (the 6th request is blocked). However, it consistently neglects the Recovery state scenario—verifying that the system allows requests again after the cooldown window expires—due to a lack of time-based simulation logic.      |

## Audit Log

> Human fills this section during audit. Mark each TC as VALID, INVALID, or INCOMPLETE.
>
> - **VALID** — TC is correct and ready for execution.
> - **INVALID** — TC has incorrect expected result, wrong input, or tests a non-existent scenario. Must be corrected or removed.
> - **INCOMPLETE** — TC is partially correct but missing information. Must be completed before execution.
>
> The Final Status column indicates the final decision after any fixes are applied. Only TCs with a Final Status of VALID will be used.

| ID              | Audit Status | Notes (Original)                                                  | Final Status | Notes (Fix)                                                     |
| --------------- | ------------ | ----------------------------------------------------------------- | ------------ | --------------------------------------------------------------- |
| TC-FR01-FR-001  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-002  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-003  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-004  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-005  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-006  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-007  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-008  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-009  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-010  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-011  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-012  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-013  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-014  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-015  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-016  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-017  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-018  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-019  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-020  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-FR-021  | INVALID      | Data-driven was incorrectly marked No. Should use test@eshop.com. | VALID        | Changed Data-driven to Yes and updated input to test@eshop.com. |
| TC-FR01-FR-022  | INVALID      | Data-driven was incorrectly marked No. Should use test@eshop.com. | VALID        | Changed Data-driven to Yes and updated input to test@eshop.com. |
| TC-FR01-FR-023  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ST-001  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ST-002  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ST-003  | INVALID      | Data-driven was incorrectly marked No. Should use test@eshop.com. | VALID        | Changed Data-driven to Yes and updated input to test@eshop.com. |
| TC-FR01-ST-004  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ST-005  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ST-006  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SEC-001 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SEC-002 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SEC-003 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SEC-004 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SEC-005 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SEC-006 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SEC-007 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SEC-008 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SEC-009 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SEC-010 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SCH-001 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SCH-002 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SCH-003 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SCH-004 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SCH-005 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SCH-006 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SCH-007 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-SCH-008 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ERR-001 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ERR-002 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ERR-003 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ERR-004 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ERR-005 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ERR-006 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ERR-007 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ERR-008 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ERR-009 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ERR-010 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ERR-011 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-ERR-012 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-IDP-001 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-IDP-002 | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-RL-001  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-RL-002  | VALID        |                                                                   | VALID        |                                                                 |
| TC-FR01-RL-003  | VALID        |                                                                   | VALID        |                                                                 |
