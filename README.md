# QA Assessment: Netaxept Payment Gateway Analysis

This repository contains a comprehensive QA analysis and test design for a web-based payment gateway. The project demonstrates a systematic approach to testing financial transactions, terminal logs, security, and edge-case scenarios.

**Repository:** https://github.com/Alexander0108/netaxept-payment-gateway-qa

## 📁 Project Structure
- `task/`: Contains the original assignment requirements and terminal screenshots.
- `report/`: Contains the final formal PDF report.
- `README.md`: Project presentation and expanded test coverage.

---

## 🖥️ Phase 1: Environment & Terminal Analysis
**Environment:**
* **Device:** MacBook Air M2
* **OS:** macOS Tahoe 26.3.1
* **Browser:** Google Chrome (Version: 146.0.7680.178)

**Key Findings from Logs:**
* **Data Integrity:** Identification of POST request parameters and payload structure.
* **Security Audit:** Verification of HTTPS encryption and data masking during transmission.
* **Response Handling:** Analysis of server status codes (200 OK vs 500 Internal Server Error) and callback mechanisms.

---

## 🧪 Phase 2: Initial Test Suite
Comprehensive test suite covering functional, negative, and security requirements.

<details>
  <summary><b>1. Positive Scenarios (Click to expand)</b></summary>

| ID | Title | Steps | Expected Result | Priority |
|:---|:---|:---|:---|:---|
| **TC-01** | Valid Visa Payment | 1. Enter 16-digit Visa <br>2. Select expiry date <br>3. Enter 3-digit CVV <br>4. Click "Pay" <br>5. Open DevTools -> Network | 1. Success redirection. <br>2. POST callback sent to merchant server. <br>3. Request status 200 OK, data encrypted. | High |
| **TC-02** | Valid Amex Payment | 1. Enter Amex card number <br>2. Select valid date <br>3. Enter 4-digit CVV <br>4. Click "Pay" | System recognizes Amex, accepts 4-digit code, and processes payment. | High |
| **TC-03** | Cancel Transaction | 1. Enter any card data <br>2. Click "Cancel" button | Form closes or redirects back to store; funds not blocked; status is cancelled. | Medium |

</details>

<details>
  <summary><b>2. Negative Scenarios (Click to expand)</b></summary>

| ID | Title | Steps | Expected Result | Priority |
|:---|:---|:---|:---|:---|
| **TC-N-01** | Expired Card | 1. Enter Card data with Expiry date < today <br>2. Click "Pay" | Error: "Card expired" or similar; Transaction rejected. | High |
| **TC-N-02** | Invalid Number (Luhn) | 1. Enter random 16 digits failing Luhn algorithm <br>2. Click "Pay" | Error: "Invalid card number". | High |
| **TC-N-03** | Empty Fields | 1. Leave all fields empty <br>2. Click "Pay" | Fields highlighted in red; "This field is required" message appears. | Medium |
| **TC-N-04** | Short CVV Code | 1. Enter 1-2 digits in CVV field <br>2. Click "Pay" | Validation prevents submission or shows format error. | Medium |
| **TC-N-05** | Year Selector Validation | 1. Open year selection dropdown | List should not contain years earlier than current year. | Medium |
| **TC-N-06** | Server Error (500) | 1. Fill valid data <br>2. Simulate connection drop (DevTools/Charles) <br>3. Click "Pay" | Form doesn't freeze; user receives clear "Service temporarily unavailable" message. | Medium |

</details>

<details>
  <summary><b>3. Security Testing (Click to expand)</b></summary>

| ID | Title | Steps | Expected Result |
|:---|:---|:---|:---|
| **TC-S-01** | Data Masking | 1. Enter 16-digit card number <br>2. Move to next field | Number is masked (e.g., **** **** **** 1234); sensitive data not visible on screen. |
| **TC-S-02** | CVV Hiding | 1. Enter 3-4 digits in CVV field | Input is masked with dots/stars to prevent data theft. |
| **TC-S-03** | XSS / SQL Injection | 1. Enter `<script>alert(1)</script>` in cardholder/number field <br>2. Click "Pay" | System validates input; script is not executed; error message shown. |
| **TC-S-04** | Auto-clear Sensitive Data | 1. Fill all fields <br>2. Refresh page (F5) | All fields are cleared; sensitive data (Card, CVV) not stored in browser cache. |

</details>

---

## 🛠️ Phase 3: Suggestions for Improvement
Recommendations based on UX/UI and Logic analysis:

* **Logic/UX:** Set current year as the default for the Expiry date to prevent payment errors.
* **Logic:** Add "Cardholder Name" field, as most modern gateways require 3D Secure verification.
* **Visual:** Sync branding; "nets" logo currently conflicts with the "Netaxept" name.
* **UX/UI:** Increase margin between "Pay" and "Cancel" buttons to prevent accidental cancellations.
* **UX:** Add "Eye" icon to CVV field to allow users to verify input before submission.

---

## 🚀 Phase 4: Expanded Atomic Coverage (Post-Feedback)
After initial review, the test suite was expanded to include granular validation logic:

### 💳 Field-Level Validation Checklist
- [ ] **Card Number EP:** Verification of 13, 15, 16, and 19-digit cards.
- [ ] **Card Number BVA:** Validation of n-1 and n+1 character lengths for each card type.
- [ ] **Paste Logic:** Verification that spaces/dashes are stripped during pasting into the card field.
- [ ] **Expiry Date Logic:** Validation for "Last month", "Current month", and "Far future" (+20 years).

### 🌐 Compatibility & Environment
- [ ] **Cross-Browser Matrix:** Chrome (Blink), Safari (WebKit), Firefox (Gecko).
- [ ] **Mobile Responsiveness:** Viewport testing for iPhone 14/15, Samsung S24, and iPad Air.

---
**Author:** Oleksandr Dermanskyi  
**Role:** QA Automation Engineer / SDET  
**Tools:** Chrome DevTools, Markdown, Manual Analysis.