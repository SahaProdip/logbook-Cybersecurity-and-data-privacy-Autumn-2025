# 🛡️ Penetration Test Report — Registration System (Phase 1)

## 1️⃣ Introduction

### Tester:

**Name:** Prodip Saha

### Purpose

The purpose of this penetration test is to identify anomalies, vulnerabilities, and privacy/compliance issues in the **user registration functionality** of the Booking System (Phase 1).  
Focus areas include input validation, authentication logic, error handling, Privacy-by-Design, and GDPR-related issues.

### Scope

#### Tested Components
- Registration page  
- Input validation: email, password, birthdate  
- Error handling behavior  
- User creation workflow  
- Front-end and back-end validation interaction  

#### Exclusions
- Login functionality  
- Reservation system  
- Admin features  
- Session management  
- API endpoints not exposed via UI  
- Direct database exploitation  

### Test Approach
**Gray-box / White-hat**

### Test Environment & Dates

- **Start:** 24th November 2025  
- **End:** 26th November 2025  

**Environment Details:**
- OS: Windows 11  
- Tools: Docker Desktop (WSL2), Browser, Manual Testing  
- Application runtime: Containerized Web App (Port 8000)  
- Database: PostgreSQL (Containerized, Port 5432)  
- Browsers used: Chrome
- Container Images:
  - `cybersec-web-phase1-part1`
  - `cybersec-db-phase1-part1`

### Assumptions & Constraints
- No valid user credentials provided  
- Time-boxed testing window  
- Only Phase 1 features available  
- Front-end partially limits invalid input  
- Manual testing only (no automated fuzzers)  
- Back-end source code not provided  

## 2️⃣ Executive Summary

### Short Summary
The registration system contains multiple medium- and high-severity issues, including missing password policy, user enumeration, weak email validation, and inconsistent error handling. While some basic validation exists, several behaviors violate secure design and GDPR transparency principles.

### Overall Risk Level
🛑 **HIGH**

### Top 5 Immediate Actions

1. Implement a **strong password policy** (length, complexity, denylist).  
2. Fix **user enumeration** by unifying all registration error responses.  
3. Add **server-side email format validation** (reject invalid formats).  
4. Improve **error handling** to avoid generic or misleading responses.  
5. Strengthen server-side validation to prevent injection and logic flaws.

## 3️⃣ Severity Scale & Definitions

| Severity Level | Description | Recommended Action |
|----------------|-------------|--------------------|
| 🔴 **High** | Critical vulnerability enabling compromise or data exposure (e.g. SQLi, auth bypass). | Fix immediately |
| 🟠 **Medium** | Significant issue requiring specific conditions or triggering logic weaknesses. | Fix ASAP |
| 🟡 **Low** | Minor issue, UX weakness, or misconfiguration. | Fix soon |
| 🔵 **Info** | No direct impact but useful for system hardening. | Monitor / Maintain |

## 4️⃣ Findings

### F-01 — Weak / Missing Password Policy
**Severity:** 🔴 High  
**Finding:** Password policy is not enforced  

**Description:**  
The system accepts extremely weak passwords such as `123`, `a`, and `password`. No minimum length, complexity, or denylist validation is applied, significantly increasing the risk of credential compromise.

**Evidence:**  
All weak passwords tested were accepted without error during registration.

---

### F-02 — User Enumeration via Registration
**Severity:** 🔴 High  
**Finding:** Different responses for existing vs. new emails  

**Description:**  
Using an existing email triggers **“Error during registration”**, while a new email successfully redirects to the homepage. Attackers can use this behavior to identify registered accounts.

**Evidence:**  
- Existing email → *Error*  
- New email → *Redirect to homepage*

---

### F-03 — Invalid Email Format Accepted
**Severity:** 🟠 Medium  
**Finding:** Weak email input validation  

**Description:**  
Invalid email formats (e.g., `testgmail@com`, missing TLD) are accepted. This violates data integrity and can lead to inconsistent database entries or fraudulent accounts.

**Evidence:**  
Entering `testgmail@com` resulted in successful registration and redirect.

---

### F-04 — Inconsistent Behavior with Long Passwords
**Severity:** 🟠 Medium  
**Finding:** Different outcomes depending on email existence  

**Description:**  
Long passwords cause inconsistent behavior:  
- With new emails → redirect to homepage  
- With existing emails → “Error during registration”  

This inconsistency indicates backend validation flaws or partial input handling failures.

**Evidence:**  
Observed differing behavior for identical password input depending on email state.

---

### F-05 — Generic Error Handling & Poor Transparency
**Severity:** 🟡 Low  
**Finding:** System returns generic error messages  

**Description:**  
Future birthdates or overly long inputs generate a generic “Error during registration” message, without explaining the issue. GDPR principles require transparency regarding data processing failures.

**Evidence:**  
- Future birthdate → generic error  
- Long email → generic error  

