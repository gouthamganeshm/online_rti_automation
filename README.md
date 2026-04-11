# RTI Online Automation using Playwright CLI + Claude Code 🤖⚙️

Automate filing an RTI request on https://rtionline.gov.in/ using Playwright CLI and Claude Code by executing a reusable instruction file.

This repository is designed so that **Claude Code reads a markdown instruction file and performs all actions automatically**, with minimal human input.

---

## Overview 🚀

This setup enables:

* End-to-end RTI filing automation via instructions 🧠
* Execution through Claude Code (agent-driven) 🤖
* Reusable template with configurable inputs ♻️
* Minimal manual steps (OTP + payment only) ✍️💳

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

1. Clone or download this repository 📦
2. Open the project in Claude Code 💻
3. Update placeholders in `rti_submit_request.md` (see Configuration below) ✏️
4. In Claude Code terminal, run:

```id="cmd2"
run instructions from rti_submit_request.md
```

5. Claude will execute all steps automatically 🤖

---

## Configuration ⚙️

Replace the following placeholders in the instruction file:

| Placeholder         | Description         | Example                                 |
| ------------------- | ------------------- | --------------------------------------- |
| `<USER_EMAIL>`      | Your email          | `john@gmail.com`                        |
| `<USER_MOBILE>`     | Mobile number       | `12345689`                            |
| `<USER_NAME>`       | Full name           | `John Doe`                              |
| `<ADDRESS_LINE_1>`  | Address line 1      | `Flat 12, ABC Building`                 |
| `<ADDRESS_LINE_2>`  | Address line 2      | `MG Road`                               |
| `<ADDRESS_LINE_3>`  | City & state        | `Mumbai, Maharashtra`                   |
| `<PINCODE>`         | PIN code            | `400001`                                |
| `<STATE_NAME>`      | State               | `Maharashtra`                           |
| `<USER_PHONE>`      | Phone number        | `12345689`                            |
| `<MINISTRY_ID>`     | Ministry numeric ID | `47`                                    |
| `<DEPARTMENT_NAME>` | Department name     | `Department of Health & Family Welfare` |
| `<RTI_TEXT>`        | RTI query text      | Custom query                            |

### Notes 📝

* For `<MINISTRY_ID>` and `<DEPARTMENT_NAME>`, refer to the `rti_database.xlsx` sheet 📊

---

## Execution Flow 🔄

Claude Code performs:

1. Opens RTI portal 🌐
2. Navigates to "Submit Request" 🧭
3. Fills email & mobile 📧📱
4. Solves captcha (via screenshot) 🔍
5. Handles OTP verification 🔐
6. Fills RTI form 📝
7. Solves second captcha 🔁
8. Submits request 📤
9. Navigates to payment 💳
10. Generates UPI QR 📷
11. Waits for payment ⏳
12. Captures confirmation ✅

---

## Manual Inputs Required ✋

### OTP (Required) 🔐

* Sent to your email 📧
* Claude will pause and ask for it ⏸️
* Always provide the **latest OTP** 🕒

---

### Payment (Required) 💳

* QR code will be generated (`rti_upi_qr.png`) 📷
* Scan using any UPI app 📱
* Complete ₹10 payment within ~2 minutes ⏳

---

## Captcha Handling 🔍

* Captcha is auto-captured as image 🖼️
* Claude reads and fills it 🤖
* If incorrect, it retries 🔁

**Common mistakes:**

* `1`, `l`, `I`
* `0`, `O`
* `f`, `r`

---

## Output Files 📁

Generated during execution:

* `rti_submit_captcha.png`
* `rti_form_captcha.png`
* `rti_upi_qr.png`
* `rti_registration_confirmed.png`

---

## Important Notes ⚠️

* RTI fee: ₹10 💰
* OTP is email-only (no SMS) 📧
* Ministry selection works best with numeric ID 🔢
* Department dropdown may timeout (expected behavior) ⏳
* After payment, RTI site may show error page — **this is normal** ⚠️
* Registration number is sent via email 📩

---

## Failure Handling 🧯

* OTP mismatch → use latest email OTP 🔄
* Captcha failure → auto retry 🔁
* Department select timeout → ignore and continue ⏭️
* Payment timeout → restart from payment step 🔄

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
