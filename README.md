# RTI Online Automation Instructions using Playwright CLI

This repository provides a generalized, reusable instruction set for automating RTI request submission on the RTI Online portal using Playwright CLI and an automation agent such as Claude Code.

The automation is driven entirely by the instruction file and can be reused by replacing placeholder values.

---

## Overview

This setup enables:

* Step by step automation of RTI filing
* Compatibility with AI agents executing instructions
* Minimal manual intervention limited to OTP and payment

---

## Prerequisites

Run the following commands in your system terminal:

```
npm install -g @playwright/cli@latest
playwright-cli --help
npx playwright install chromium
```

Ensure:

* Node.js is installed
* Playwright CLI is available
* Chromium browser is installed

---

## Configuration

Before execution, replace all placeholder values in the instruction file:

```
<USER_EMAIL>
<USER_MOBILE>
<USER_NAME>
<ADDRESS_LINE_1>
<ADDRESS_LINE_2>
<ADDRESS_LINE_3>
<PINCODE>
<STATE_NAME>
<USER_PHONE>
<MINISTRY_ID>
<DEPARTMENT_NAME>
<RTI_TEXT>
```

---

## How to Execute using Claude Code

1. Open Claude Code terminal

2. Navigate to the repository directory

3. Run the following instruction:

```
Follow the step by step instructions in rti_submit_request.md and execute them using playwright-cli
```

4. The agent will execute all steps sequentially

---

## Execution Flow

1. Open RTI Online portal
2. Navigate to Submit Request
3. Enter email and mobile number
4. Capture and solve captcha
5. Submit and accept OTP dialog
6. Enter OTP received via email
7. Fill RTI request form
8. Capture and solve second captcha
9. Submit form and handle retries
10. Proceed to payment gateway
11. Select UPI and generate QR code
12. Complete payment
13. Capture confirmation

---

## Manual Inputs Required

### OTP

* Sent to registered email
* Must be entered manually
* Use latest OTP if validation fails

### Payment

* QR code will be generated
* Scan using UPI app
* Confirm completion within time limit

---

## Captcha Handling

* Captcha images are captured using screenshots
* Read text from image and input value
* Case insensitive
* Retry if incorrect

---

## Output Files

* captcha1.png
* captcha2.png
* rti_upi_qr.png
* rti_registration_confirmed.png

---

## Important Notes

* RTI filing fee is 10 rupees
* QR code expires in about 2 minutes
* OTP is email based only
* Ministry selection works best with numeric ID
* Some pages may show errors after payment this is expected
* Registration number is sent via email

---

## Failure Handling

* If OTP does not match use latest OTP
* If captcha fails retry with new value
* If selection times out continue and accept dialog
* Always accept confirmation dialogs

---

## Limitations

* Full automation is not possible due to:

  * Captcha verification
  * OTP authentication
  * Payment confirmation

---

## Best Practices

* Execute steps exactly as defined
* Do not skip steps
* Ensure stable internet connection
* Complete payment quickly after QR generation

---

## Disclaimer

This project is for educational and automation purposes only.
Users are responsible for ensuring compliance with applicable laws and terms of use of the RTI Online portal.
