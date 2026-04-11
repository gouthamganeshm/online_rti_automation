# RTI Online Automation using Playwright CLI

This repository provides a structured instruction set to automate RTI request submission on the RTI Online portal using Playwright CLI and an AI agent such as Claude Code.

---

## Overview

This project is designed to:

* Automate RTI form submission
* Work with AI agents that execute step by step instructions
* Reduce manual effort while handling unavoidable steps like OTP and payment

The automation is driven entirely by the instruction file:

```
rti_submit_request.md
```

---

## Prerequisites

Run the following commands in your system terminal:

```
npm install -g @playwright/cli@latest
playwright-cli --help
npx playwright install chromium
```

### Requirements

* Node.js installed
* Internet connection
* Valid email ID
* Mobile number
* UPI app for payment

---

## Repository Structure

```
.
├── rti_submit_request.md   # Instruction file for automation agent
├── README.md               # Documentation
```

---

## Configuration

Before execution, update placeholders inside:

```
rti_submit_request.md
```

Replace all values such as:

```
<USER_EMAIL>
<USER_NAME>
<MINISTRY_ID>
<RTI_TEXT>
```

---

## How to Run Using Claude Code

1. Open Claude Code terminal

2. Navigate to the repository folder

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
4. Solve captcha (from screenshot)
5. Enter OTP (user input required)
6. Fill RTI request form
7. Solve second captcha
8. Submit form and handle retries
9. Redirect to SBI payment gateway
10. Generate UPI QR code
11. User completes payment
12. Capture confirmation

---

## Manual Steps Required

### OTP

* OTP is sent to email
* Enter the latest OTP when prompted

### Payment

* Scan QR code using UPI app
* Confirm payment within time limit

---

## Captcha Handling

* Captcha images are saved as:

  * captcha1.png
  * captcha2.png
* Read text directly from image
* Captcha is case insensitive
* Retry if incorrect

---

## Output Files

```
captcha1.png
captcha2.png
upi_qr.png
confirmation.png
```

---

## Important Notes

* RTI filing fee is 10 rupees
* QR code expires in about 2 minutes
* Always use latest OTP
* Ministry selection works best with numeric ID
* Some pages may show errors after payment this is expected
* RTI registration number is sent via email

---

## Failure Handling

* If OTP fails use latest email OTP
* If captcha fails retry until correct
* If page times out continue and check for dialog
* Always accept confirmation dialogs

---

## Limitations

* Full automation is not possible due to:

  * Captcha
  * OTP verification
  * Payment confirmation

---

## Future Improvements

* OCR based captcha solving
* Automatic OTP retrieval from email
* End to end automation pipeline
* Logging and retry improvements

---

## Disclaimer

This project is intended for educational and automation purposes only.
Users are responsible for complying with applicable laws and terms of use of the RTI Online portal.
