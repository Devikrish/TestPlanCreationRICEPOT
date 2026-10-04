# CRM Portal Authentication Test Plan

## 1. Document Control

| Field | Value |
|---|---|
| Application | CRM Portal (fictional training application) |
| Feature | Email/password authentication and logout |
| Plan status | Draft for stakeholder review |
| Requirements baseline | REQ-01 through REQ-04 supplied in the task |
| Test environment | To be confirmed |
| Test owner | QA team, to be assigned |
| Profile structure | Conventional test-plan sections used because a Profile B template or defined headings were not supplied |

## 2. Purpose and Objectives

Define a repeatable functional test approach to verify that CRM Portal users can authenticate with valid credentials, cannot authenticate with invalid or missing credentials, and are logged out securely from protected content.

The plan also defines regression coverage, prerequisites, measurable proposed entry and exit criteria, and open decisions that require stakeholder confirmation before execution.

## 3. Scope

### 3.1 In Scope

- Email and password login using the CRM Portal UI.
- Successful authentication with an approved active synthetic account.
- Rejection of an incorrect password.
- Prevention of authentication when required fields are empty.
- Logout, session termination, and denial of access to protected content after logout.
- Functional regression of the covered login and logout behaviors on the approved browser and environment matrix.
- Verification of user-visible validation or error feedback for negative cases, without assuming exact wording that has not been specified.

### 3.2 Out of Scope

- Single sign-on (SSO).
- Multi-factor authentication (MFA).
- Account registration, activation, or password reset.
- Performance, load, and stress testing.
- Penetration testing or other security testing beyond the functional post-logout access checks in REQ-04.
- Unspecified account lockout, rate limiting, CAPTCHA, or password-policy behavior.

## 4. Test Approach

1. **Review requirements and confirm prerequisites.** Confirm the target build, environment, browser support, test account, protected route, expected landing page, and expected validation behavior with product and development stakeholders.
2. **Prepare controlled test data.** Use only approved synthetic accounts. Keep credentials in the approved secret store or test-runner configuration; do not place credentials in this plan, source control, screenshots, or reports.
3. **Run functional tests.** Execute the traceable cases in Section 7 against the agreed environment. Record actual results, evidence references, build, browser, and defects.
4. **Run regression.** Re-run all in-scope login and logout cases for each agreed release candidate and browser/environment combination.
5. **Triage and retest.** Log deviations as defects, prioritize them with the product team, retest fixes, and perform relevant regression before sign-off.

Tests should assert observable outcomes rather than implementation details. Unless exact UI copy is approved, negative login tests verify visible validation/error feedback and that the user remains unauthenticated; they do not assert a specific message string.

## 5. Environment and Prerequisites

The following are not provided and must be confirmed before execution:

| Prerequisite | Required confirmation |
|---|---|
| Test environment | Base URL, environment owner, availability window, and build/version under test |
| Test account | Approved active synthetic account and a process to verify its expected identity; no production credentials |
| Invalid-password data | A known wrong password for the synthetic active account; ensure attempts will not trigger an unintended lockout |
| Browser matrix | Supported browser names and versions, operating systems, and any required viewport sizes |
| Protected content | One or more protected CRM routes and the expected behavior when an unauthenticated user opens them |
| Login outcomes | Expected post-login landing page and an observable authenticated-state indicator |
| Error behavior | Whether empty-field checks are browser-native, application-rendered, or both; approved wording if exact-copy checks are required |
| Logout behavior | Expected redirect or logged-out page and the intended handling of browser Back, refresh, and direct protected-route navigation |
| Test operations | Defect tracker, severity definitions, evidence-storage location, test owner, and stakeholder sign-off owner |

## 6. Test Data and Safety

- **Valid account:** one active, authorized synthetic account with credentials provisioned through an approved secure channel.
- **Incorrect password:** the valid synthetic account identifier paired with a deliberately incorrect password.
- **Empty fields:** both email and password empty; email empty with password populated; password empty with email populated.
- **Session state:** a successful login session for logout and post-logout checks.
- Do not use real customer or employee credentials or data.
- Avoid repeated invalid attempts beyond the agreed test-data policy. Confirm account-lockout behavior and reset procedure before running repeated negative cases.
- Mask credentials and sensitive user data in evidence and defect reports.

## 7. Requirement Coverage and Test Cases

| Requirement | Test ID | Scenario | Test type | Preconditions / data | Expected result |
|---|---|---|---|---|---|
| REQ-01 | AUTH-001 | Sign in with valid credentials | Positive functional | Active synthetic account; logged out | User is authenticated, reaches the approved authenticated landing page, and the displayed identity corresponds to the test account |
| REQ-02 | AUTH-002 | Sign in with a valid account identifier and incorrect password | Negative functional | Active synthetic account; incorrect password | Authentication is denied; user remains unauthenticated; visible error feedback is provided |
| REQ-03 | AUTH-003 | Submit with both required fields empty | Negative functional / validation | Logged-out state | Authentication is prevented; required-field validation is visible; no authenticated content is available |
| REQ-03 | AUTH-004 | Submit with email empty and password populated | Negative functional / validation | Logged-out state | Authentication is prevented; email-required validation is visible; no authenticated content is available |
| REQ-03 | AUTH-005 | Submit with password empty and email populated | Negative functional / validation | Logged-out state | Authentication is prevented; password-required validation is visible; no authenticated content is available |
| REQ-04 | AUTH-006 | Log out from an authenticated session | Positive functional | Complete AUTH-001; identify an approved protected route | Logout completes; the session is ended; the user is redirected to or shown the approved logged-out state |
| REQ-04 | AUTH-007 | Open a protected route directly after logout | Negative functional / session | Complete AUTH-006; use the same browser session | Protected content is not displayed; user is redirected to login or shown the approved unauthenticated response |
| REQ-04 | AUTH-008 | Use browser Back and refresh after logout | Negative functional / session regression | Complete AUTH-006 from a protected page | Protected content is not exposed after navigation or refresh; any cached view must not permit access to protected actions or data |

Each case applies to every browser/environment combination approved in the matrix. Cases requiring UI-specific behavior must be updated after the application routes, validation mechanism, and expected messages are confirmed.

## 8. Regression Strategy

- Run AUTH-001 through AUTH-008 on the agreed release-candidate build and supported browser matrix.
- After a defect fix, rerun the directly affected case and relevant dependent cases; rerun all in-scope cases before release sign-off.
- Preserve traceability from each result to its requirement, build, environment, browser, and defect (if any).
- Report blocked or unexecuted cases separately; do not count them as passed.

## 9. Proposed Entry Criteria — For Review

Execution may begin when all of the following proposed criteria are met:

1. The target build is deployed to the agreed test environment and its version/build identifier is recorded.
2. The environment and approved browser matrix are documented, and the login page and at least one protected route are reachable.
3. Product/development has confirmed the login landing page, authenticated-state indicator, logout outcome, and post-logout protected-route behavior.
4. One active synthetic test account is available through the approved credential-handling process, with a reset/unlock procedure confirmed.
5. The test cases and requirement mapping in Section 7 are reviewed; no unresolved question prevents an objective pass/fail decision.
6. No known environment blocker prevents safe test execution.

These are proposed thresholds and require review and approval; they are not supplied application requirements.

## 10. Proposed Exit Criteria — For Review

Testing may be considered complete when all of the following proposed criteria are met:

1. **100% requirement coverage:** every requirement REQ-01 through REQ-04 has at least one executed, recorded test result.
2. **100% execution of planned cases:** all applicable AUTH-001 through AUTH-008 cases have a final status of Pass, Fail, or formally accepted Blocked; no case is silently omitted.
3. **Pass threshold:** all REQ-01 and REQ-04 cases pass, and at least 95% of all applicable cases pass. Because this plan contains a small test set, the proposed target is also zero unresolved functional failures; any exception requires documented product-owner approval.
4. **Defect threshold:** zero open Severity 1 or Severity 2 defects affecting in-scope authentication or logout behavior. Lower-severity defects require an agreed disposition and owner.
5. **Retest:** every fix accepted into the candidate build has passed its targeted retest and relevant regression.
6. **Sign-off:** QA results, defects, blocked cases, known limitations, and residual risks are reviewed by the designated QA and product owners.

The 95% threshold is an overall proposed metric only; it does not waive a failed critical case or requirement. Stakeholders must approve or replace these proposed thresholds before execution.

## 11. Results, Defects, and Reporting

For each execution, record the test ID, requirement ID, result, timestamp, build, environment, browser/version, sanitized evidence reference, and defect ID where applicable. Use the project defect tracker and its agreed severity scale.

The completion summary should include:

- Planned, executed, passed, failed, blocked, and not-run case counts.
- Requirement coverage and browser/environment coverage.
- Open defects by severity and their release disposition.
- Deviations from the approved entry/exit criteria.
- QA recommendation and required stakeholder approvals.

## 12. Risks, Assumptions, and Open Decisions

| Item | Impact | Action / owner |
|---|---|---|
| Environment and supported browsers are unspecified | Coverage cannot be finalized or execution reproduced | Product/development to provide environment and browser matrix |
| Active synthetic account and credential reset process are unspecified | REQ-01 and session regression may be blocked; invalid attempts may cause lockout | Environment/test-data owner to provision account and confirm safe attempt policy |
| Exact error text and validation mechanism are unspecified | Exact-copy assertions would be unreliable | Product owner to approve expected UX; until then, assert visible feedback and denied authentication |
| Protected route and logout redirect are unspecified | REQ-04 pass/fail oracle is incomplete | Product/development to identify protected route and expected logged-out behavior |
| Session/cache behavior is unspecified | Browser Back may show a cached rendering even when the server session is invalid | Development to confirm expected behavior; QA to verify protected data/actions remain inaccessible after refresh/navigation |
| Proposed entry/exit thresholds are not approved | Release sign-off criteria may differ by stakeholder | QA and product owners to review and approve Section 9 and Section 10 |
| Profile B headings were not supplied | Exact template conformance cannot be verified | Replace or reorganize these sections if the organization provides its Profile B template |

## 13. Approval

| Role | Name | Decision / date |
|---|---|---|
| QA owner | To be assigned | Pending review |
| Product owner | To be assigned | Pending review |
| Development owner | To be assigned | Pending review |
