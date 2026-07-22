# QA Assessment Report: Netaxept Payment Gateway

This repository contains a comprehensive QA analysis and test design for the **Netaxept Payment Window / Hosted Payment Form API**. The report covers functional testing, card validation, security & PCI-DSS compliance, UI/UX error handling, and localization checks.

**Repository:** https://github.com/Alexander0108/netaxept-payment-gateway-qa

## 📁 Project Structure

- `task/` — Original assignment requirements and terminal screenshots.
- `report/` — Formal PDF report (generated via Python `python-docx` → HTML → Headless Chrome PDF).
- `README.md` — Project presentation and test suite summary.

---

## 🧪 Test Suite Summary

| Category                                   | Total Cases | High (P1) | Medium (P2) | Low (P3) |
| ------------------------------------------ | ----------- | --------- | ----------- | -------- |
| 1. Positive Scenarios (Smoke / Happy Path) | 4           | 3         | 1           | 0        |
| 2. Card Validation (PAN & Expiry Date)     | 6           | 2         | 4           | 0        |
| 3. Security & Validation (CVV / CVC / CID) | 3           | 2         | 1           | 0        |
| 4. UI / UX & System Error Handling         | 6           | 3         | 2           | 1        |
| 5. Security & PCI-DSS Compliance           | 6           | 6         | 0           | 0        |
| 6. Localization & Currency Display         | 2           | 0         | 1           | 1        |
| **TOTAL**                                  | **27**      | **16**    | **9**       | **2**    |

---

## 📋 Detailed Test Cases

<details>
<summary><b>1. Positive Scenarios — Smoke / Happy Path (4 cases)</b></summary>

| ID        | Title                                  | Expected Result                                                        | Priority |
| --------- | -------------------------------------- | ---------------------------------------------------------------------- | -------- |
| TC-POS-01 | Successful Visa Payment (3D-Secure)    | Status "Success", redirect to success page, receipt generated          | P1       |
| TC-POS-02 | Successful Mastercard Payment (no 3DS) | Transaction successful without additional OTP confirmation             | P1       |
| TC-POS-03 | Successful Amex Payment (4-digit CID)  | CVV/CID field accepts 4 digits. Payment successful                     | P2       |
| TC-POS-04 | User Cancellation                      | Transaction cancelled, return to merchant site with "Cancelled" status | P1       |

</details>

<details>
<summary><b>2. Card Validation — PAN & Expiry Date (6 cases)</b></summary>

| ID        | Title                                     | Expected Result                                                                     | Priority |
| --------- | ----------------------------------------- | ----------------------------------------------------------------------------------- | -------- |
| TC-VAL-01 | Invalid PAN (Luhn check fails)            | Error: "Invalid card number". Request blocked                                       | P1       |
| TC-VAL-02 | Special characters in PAN                 | Mask ignores letters & special chars, accepts only digits                           | P2       |
| TC-VAL-03 | Boundary PAN lengths (<13 and >19 digits) | Input limited to 13–19 digits. Pay button disabled when <13 digits                  | P2       |
| TC-VAL-04 | Expired card (expiry date in the past)    | Error: "Card expired". Payment impossible                                           | P1       |
| TC-VAL-05 | Non-existent month in expiry date         | Validator rejects values outside 01–12 range                                        | P2       |
| TC-VAL-06 | Year dropdown validation                  | Past years unavailable or marked expired. Current year + ≥10 future years available | P2       |

</details>

<details>
<summary><b>3. CVV / CVC / CID Validation (3 cases)</b></summary>

| ID        | Title                              | Expected Result                                                 | Priority |
| --------- | ---------------------------------- | --------------------------------------------------------------- | -------- |
| TC-CVV-01 | CVV too short (1–2 digits)         | Error: "CVV must contain 3 digits"                              | P1       |
| TC-CVV-02 | CVV exceeds length (>4 digits)     | Field truncates input to max 4 digits (card-type dependent)     | P2       |
| TC-CVV-03 | Empty CVV with other fields filled | Form not submitted, CVV field highlighted red: "Required field" | P1       |

</details>

<details>
<summary><b>4. UI/UX & System Error Handling (6 cases)</b></summary>

| ID       | Title                                            | Expected Result                                                                            | Priority |
| -------- | ------------------------------------------------ | ------------------------------------------------------------------------------------------ | -------- |
| TC-UI-01 | Auto card logo detection (BIN-based)             | Card system icon dynamically changes based on BIN                                          | P3       |
| TC-UI-02 | Card declined (Insufficient Funds)               | Clear user-friendly error: "Transaction declined: Insufficient funds"                      | P1       |
| TC-UI-03 | Payment session timeout                          | Message: "Session expired", redirect to re-initialization                                  | P1       |
| TC-UI-04 | Mobile responsiveness & numeric keyboard         | Numeric keyboard triggered (`inputmode="numeric"`). Form is responsive                     | P2       |
| TC-UI-05 | Order details match (Order123, amount, merchant) | Form correctly displays Order ID = Order123, amount = 120.00 EUR, merchant = Test merchant | P1       |
| TC-UI-06 | CVV/CVC/CID tooltip                              | Tooltip opens with help text, closes on re-click or outside click                          | P2       |

</details>

<details>
<summary><b>5. Security & PCI-DSS Compliance (6 cases)</b></summary>

| ID        | Title                                          | Expected Result                                                                 | Priority |
| --------- | ---------------------------------------------- | ------------------------------------------------------------------------------- | -------- |
| TC-SEC-01 | Race condition — double-click protection       | Button disabled after 1st click. Only 1 network request sent                    | P1       |
| TC-SEC-02 | CVV masking & clipboard protection             | Symbols shown as dots (●●●). Copying from field blocked                         | P1       |
| TC-SEC-03 | PAN masking in DevTools / Network tab          | Full PAN and CVV not transmitted in plaintext (tokenization used)               | P1       |
| TC-SEC-04 | Secure connection check (HTTPS / SSL)          | Strict HTTPS protocol, valid SSL certificate, no mixed content                  | P1       |
| TC-SEC-05 | Amount tampering protection (DOM manipulation) | Backend validates amount by Session ID. Original amount charged                 | P1       |
| TC-SEC-06 | XSS protection & PAN masking in form           | Scripts not executed (sanitized). PAN masked on blur — only last 4 digits shown | P1       |

</details>

<details>
<summary><b>6. Localization & Currency Display (2 cases)</b></summary>

| ID        | Title                                    | Expected Result                                                            | Priority |
| --------- | ---------------------------------------- | -------------------------------------------------------------------------- | -------- |
| TC-LOC-01 | Localized error messages & currencies    | All text messages, field labels, and currency symbol match selected locale | P3       |
| TC-LOC-02 | Thousand separators & decimal formatting | Number formatting matches regional standards of selected language          | P2       |

</details>

---

## 🛠️ UX/UI & Usability Recommendations

| #   | Area                               | Recommendation                                                                | Expected Improvement                                                       |
| --- | ---------------------------------- | ----------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| 1   | PAN Input Formatting               | Auto-insert spaces after every 4 digits during card number entry              | Reduces input errors, improves readability, follows payment form standards |
| 2   | Card Logo Highlighting (BIN-based) | Dynamically highlight card system logo on first digits input (border + glow)  | Instant visual feedback, reduces risk of unsupported card usage            |
| 3   | Inline Validation Error Styling    | Display validation errors directly below the relevant field (red text + icon) | Improves usability — user immediately sees which field is incorrect        |

---

## 🚀 Summary & Testing Recommendations

- **High Priority Coverage:** 16 of 27 test cases cover critical paths (Smoke, card validation, Security). Priority execution: TC-POS-\*, TC-VAL-01, TC-SEC-01, TC-SEC-03.
- **Security & Compliance:** Verification of no plaintext PAN/CVV in logs and network requests (PCI-DSS) is mandatory before Production release.
- **Automation Potential:** Positive scenarios (TC-POS-01 – TC-POS-04) and field validation checks should be automated first via API/UI tests.

---

## 🔧 Environment & Test Scope

| Parameter     | Value                                             |
| ------------- | ------------------------------------------------- |
| Device        | MacBook Air M2                                    |
| OS            | macOS Tahoe 26.3.1                                |
| Browser       | Google Chrome (Version 146.0.7680.178)            |
| Target System | Netaxept Payment Window / Hosted Payment Form API |

---

**Author:** Oleksandr Dermanskyi  
**Role:** QA Engineer  
**Tools:** Chrome DevTools, Python (python-docx), Pandoc, Headless Chrome, Manual Analysis
