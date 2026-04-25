# RTI Online Automation using Playwright CLI + Claude Code 🤖⚙️

Automate filing RTI requests on the **Central RTI portal** (rtionline.gov.in) or the **Karnataka RTI portal** (rtionline.karnataka.gov.in) using Playwright CLI and Claude Code by executing a reusable instruction file.

This repository is designed so that **Claude Code reads a markdown instruction file and performs all actions automatically**, with minimal human input.

---

## Overview 🚀

This setup enables:

* End-to-end RTI filing automation via instructions 🧠
* Execution through Claude Code (agent-driven) 🤖
* Reusable templates with configurable inputs ♻️
* Minimal manual steps (OTP + payment only) ✍️💳

---

## Portals Supported 🌐

| Portal | Instruction File | Database |
|---|---|---|
| Central RTI — rtionline.gov.in | `rti_submit_request_template.md` | `rti_database.xlsx` |
| Karnataka RTI — rtionline.karnataka.gov.in | `karnataka_rti_submit_request_template.md` | `Karnataka_RTI_Database.xlsx` |

---

## Prerequisites 🧩

Run the following in your system terminal:

```id="cmd1"
npm install -g @playwright/cli@latest
playwright-cli --help
npx playwright install chromium
```

Make sure:

* Node.js is installed ✅
* Playwright CLI is working ⚙️
* Chromium browser is installed 🌐

---

## How to Use 🛠️

### Central RTI Portal (rtionline.gov.in)

1. Clone or download this repository 📦
2. Open the project in Claude Code 💻
3. Replace all `<PLACEHOLDER>` values in `rti_submit_request_template.md` (see Configuration below) ✏️
4. In Claude Code, run:

```id="cmd2"
run instructions from rti_submit_request_template.md
```

5. Claude will execute all steps automatically 🤖

---

### Karnataka RTI Portal (rtionline.karnataka.gov.in)

1. Clone or download this repository 📦
2. Open the project in Claude Code 💻
3. Replace all `<PLACEHOLDER>` values in `karnataka_rti_submit_request_template.md` (see Configuration below) ✏️
4. In Claude Code, run:

```id="cmd3"
run instructions from karnataka_rti_submit_request_template.md
```

5. Claude will execute all steps automatically 🤖

---

## Configuration ⚙️

### Central RTI Portal

Replace the following placeholders in `rti_submit_request_template.md`:

| Placeholder | Description | Example |
|---|---|---|
| `<USER_EMAIL>` | Your email | `john@gmail.com` |
| `<USER_MOBILE>` | Mobile number | `13456644` |
| `<USER_NAME>` | Full name | `John Doe` |
| `<ADDRESS_LINE_1>` | Address line 1 | `Flat 12, ABC Building` |
| `<ADDRESS_LINE_2>` | Address line 2 | `MG Road` |
| `<ADDRESS_LINE_3>` | City & state | `Mumbai, Maharashtra` |
| `<PINCODE>` | PIN code | `400001` |
| `<STATE_NAME>` | State | `Maharashtra` |
| `<USER_PHONE>` | Phone number | `13456644` |
| `<MINISTRY_ID>` | Ministry numeric ID | `47` |
| `<DEPARTMENT_NAME>` | Department name | `Department of Health & Family Welfare` |
| `<RTI_TEXT>` | RTI query text | Custom query |

> For `<MINISTRY_ID>` and `<DEPARTMENT_NAME>`, refer to `rti_database.xlsx` 📊

---

### Karnataka RTI Portal

Replace the following placeholders in `karnataka_rti_submit_request_template.md`:

| Placeholder | Description | Example |
|---|---|---|
| `<USER_EMAIL>` | Your email | `john@gmail.com` |
| `<USER_MOBILE>` | Mobile number | `13456644` |
| `<USER_NAME>` | Full name | `John Doe` |
| `<ADDRESS_LINE_1>` | Address line 1 | `Flat 12, ABC Building` |
| `<ADDRESS_LINE_2>` | Address line 2 | `MG Road` |
| `<ADDRESS_LINE_3>` | City & PIN | `Bengaluru 560001` |
| `<PINCODE>` | PIN code | `560001` |
| `<USER_PHONE>` | Phone number | `13456644` |
| `<MINISTRY_ID>` | Department numeric ID | `1163` |
| `<RTI_TEXT>` | RTI query text | Custom query |

> For `<MINISTRY_ID>`, refer to the **Department ID Reference** section in `karnataka_rti_submit_request_template.md` or `Karnataka_RTI_Database.xlsx` (1,696 entries) 📊

---

## Execution Flow 🔄

### Central RTI Portal

Claude Code performs:

1. Opens RTI portal 🌐
2. Navigates to "Submit Request" 🧭
3. Fills email & mobile 📧📱
4. Solves captcha (via screenshot) 🔍
5. Handles OTP verification (email) 🔐
6. Fills RTI form 📝
7. Solves second captcha 🔁
8. Submits request 📤
9. Navigates to SBI payment gateway 💳
10. Generates UPI QR 📷
11. Waits for payment ⏳
12. Captures confirmation ✅
13. Registration number arrives via email 📩

---

### Karnataka RTI Portal

Claude Code performs:

1. Opens Karnataka RTI portal 🌐
2. Accepts guidelines 🧭
3. Fills email & mobile 📧📱
4. Solves captcha (via screenshot) 🔍
5. Handles OTP verification (mobile SMS) 🔐
6. Fills RTI form including public authority 📝
7. Solves second captcha 🔁
8. Submits request 📤
9. Navigates to Khajane-II → SBI ePay gateway 💳
10. Generates UPI QR 📷
11. Waits for payment ⏳
12. Captures confirmation ✅
13. Registration number shown on screen 🖥️

---

## Manual Inputs Required ✋

### OTP (Required) 🔐

| Portal | OTP Delivery |
|---|---|
| Central RTI | Sent to your **email** 📧 |
| Karnataka RTI | Sent to your **mobile** 📱 |

* Claude will pause and ask for it ⏸️
* Always provide the **latest OTP** 🕒
* Karnataka portal has a **daily OTP limit** — stop and resume the next day if exceeded ⚠️

---

### Payment (Required) 💳

| Portal | QR File | Expiry |
|---|---|---|
| Central RTI | `rti_upi_qr.png` | ~2 minutes ⏳ |
| Karnataka RTI | `karnataka_rti_upi_qr.png` | ~5 minutes ⏳ |

* Scan using any UPI app 📱
* Complete ₹10 payment within the expiry window ⚡

---

## Captcha Handling 🔍

* Captcha is auto-captured as image 🖼️
* Claude reads and fills it 🤖
* If incorrect, it retries 🔁

**Common misreads (Central RTI portal):**

* `1`, `l`, `I`
* `0`, `O`
* `f`, `r`

**Common misreads (Karnataka RTI portal):**

* `g`, `9` (most frequent — look for the descender/tail)
* `1`, `l`, `I`
* `0`, `O`

> **Karnataka Khajane-II captcha warning:** The captcha token is one-time use on the server. Never test it via AJAX/eval before submitting — it will be consumed and the payment submission will fail. ⚠️

---

## Output Files 📁

### Central RTI Portal

* `rti_submit_captcha.png`
* `rti_form_captcha.png`
* `rti_upi_qr.png`
* `rti_registration_confirmed.png`

### Karnataka RTI Portal

* `karnataka_rti_captcha.png`
* `karnataka_rti_otp_captcha.png`
* `karnataka_rti_form_captcha.png`
* `karnataka_rti_khajane_captcha.png`
* `karnataka_rti_upi_qr.png`
* `karnataka_rti_confirmed.png`

---

## Important Notes ⚠️

| | Central RTI | Karnataka RTI |
|---|---|---|
| Fee | ₹10 💰 | ₹10 💰 |
| OTP channel | Email only 📧 | Mobile only 📱 |
| Payment gateway | SBI (direct) | Khajane-II → SBI ePay |
| Registration number | Sent via email 📩 | Shown on screen 🖥️ |
| Department selection | Ministry + Department dropdowns | Single `#MinistryId` dropdown |
| Department database | `rti_database.xlsx` | `Karnataka_RTI_Database.xlsx` (1,696 entries) |
| Post-payment page | Error page is normal ⚠️ | Confirmation page with registration number ✅ |

---

## Failure Handling 🧯

* OTP mismatch → use latest OTP 🔄
* Captcha failure → auto retry 🔁
* Department select timeout → ignore and continue ⏭️
* Payment timeout → restart from payment step 🔄
* Khajane captcha "Invalid" → reload page, re-select UPI + SBI e-Pay, retry 🔄

---

## Limitations 🚧

Full automation is not possible due to:

* Captcha verification 🔍
* OTP authentication 🔐
* UPI payment confirmation 💳

---

## Best Practices 💡

* Do not modify instruction steps unnecessarily ⚠️
* Replace all placeholders before running ✏️
* Ensure stable internet connection 🌐
* Complete payment quickly ⚡

---

## Disclaimer ⚖️

This project is for educational and automation purposes only.

Users are responsible for complying with RTI portal terms and applicable laws.

---

## ⚠️ Final Note

This entire repository is vibe coded — use it at your own risk ⚠️🤖🔥
