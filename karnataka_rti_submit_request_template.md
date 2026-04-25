# Karnataka RTI Online - File an RTI Request with Claude Code

Automate filing an RTI request on [rtionline.karnataka.gov.in](https://rtionline.karnataka.gov.in/) using [Claude Code](https://claude.ai/code) and its built-in Playwright browser skill.

## Prerequisites

- Claude Code installed and running
- The `playwright-cli` skill available (comes bundled with Claude Code)
- A valid Indian mobile number and email address
- ₹10 for the RTI filing fee (paid via UPI QR scan)

---

## How to Use

1. Copy this file into your Claude Code project directory
2. Replace all `<PLACEHOLDER>` values in the [Configuration](#configuration) section below with your details
3. Tell Claude Code: `run instructions from karnataka_rti_submit_request_template.md`
4. Follow along — Claude will pause twice for human input:
   - **OTP** — sent to your mobile after credentials are verified
   - **UPI Payment** — scan the QR code to pay ₹10

---

## Configuration

Replace these placeholders before running:

| Placeholder | Description | Example |
|---|---|---|
| `<USER_EMAIL>` | Your email address | `john@gmail.com` |
| `<USER_MOBILE>` | 10-digit mobile number | `9876543210` |
| `<USER_NAME>` | Your full name | `John Doe` |
| `<ADDRESS_LINE_1>` | Address line 1 (name/building) | `John Doe` |
| `<ADDRESS_LINE_2>` | Address line 2 (street/locality) | `12 MG Road, Near Bus Stand` |
| `<ADDRESS_LINE_3>` | Address line 3 (city, PIN) | `Bengaluru 560001` |
| `<PINCODE>` | 6-digit PIN code | `560001` |
| `<USER_PHONE>` | Phone number for form (can be same as mobile) | `9876543210` |
| `<MINISTRY_ID>` | Numeric department ID (see [Department ID Reference](#department-id-reference)) | `1163` |
| `<RTI_TEXT>` | Your RTI request body text | *(see below)* |

---

## Steps

### 1. Open Karnataka RTI Portal
```
playwright-cli open https://rtionline.karnataka.gov.in/guidelines.php?request
```
> Navigate directly to the guidelines+request page — skips the home page click.

### 2. Check Guidelines Checkbox and Submit
```
playwright-cli check "input[type='checkbox']"
playwright-cli click "input[type='submit'][value='Submit']"
```

### 3. Fill Email, Mobile and Captcha
```
playwright-cli fill "#email" "<USER_EMAIL>"
playwright-cli fill "getByRole('textbox', { name: 'Please enter 10 digit mobile' })" "<USER_MOBILE>"
```
> Screenshot the captcha and read it directly — never ask the user:
```
playwright-cli screenshot "getByRole('img', { name: 'captchaImage' })" --filename=karnataka_rti_captcha.png
```
> Read `karnataka_rti_captcha.png` using the Read tool and interpret the captcha text, then fill:
```
playwright-cli fill "[id='6_letters_code']" "<CAPTCHA_VALUE_READ_FROM_IMAGE>"
playwright-cli click "getByRole('button', { name: 'Submit' })"
```
> **Captcha tips:** Watch for `g` vs `9` (most common ambiguity — prefer `g` when the character has a tail/descender), `1` vs `l` vs `I`, `0` vs `O`, `f` vs `r`. Captcha is case-sensitive on this portal.

### 4. ⏸ Wait for OTP — Human Input Required
> OTP is sent to **mobile `<USER_MOBILE>`** — not email (unlike the central RTI portal).
> **Ask the user:** "An OTP has been sent to your mobile `<USER_MOBILE>`. Please share the OTP."
> Wait for the user to provide the OTP before proceeding.

> Screenshot the captcha on the OTP page and read it:
```
playwright-cli screenshot "getByRole('img', { name: 'captchaImage' })" --filename=karnataka_rti_otp_captcha.png
```
> Read `karnataka_rti_otp_captcha.png`, then fill OTP and captcha and submit:
```
playwright-cli fill "getByRole('textbox', { name: 'Enter OTP Number' })" "<OTP_FROM_USER>"
playwright-cli fill "[id='6_letters_code']" "<CAPTCHA_VALUE_READ_FROM_IMAGE>"
playwright-cli click "getByRole('button', { name: 'Submit' })"
```
> If the page shows **"OTP does not match"** — a new captcha loads automatically. Ask the user for the **latest OTP** from their mobile (the previous OTP may have expired) and retry this step.
> If the page shows **"Today's OTP Limit has Exceeded"** — the daily OTP limit for this mobile number has been hit. Stop and resume the next day.

### 5. Fill the RTI Request Form

#### Public Authority
> The public authority is selected from `#MinistryId` — a single dropdown listing all Karnataka departments. Always use the numeric value ID for reliability.
```
playwright-cli select "#MinistryId" "<MINISTRY_ID>"
```
> To find a department's ID by keyword while on the form page: `playwright-cli eval "Array.from(document.querySelector('#MinistryId').options).filter(o => o.text.toLowerCase().includes('keyword')).map(o => o.text + '|' + o.value).join('\\n')"`

#### Personal Details
```
playwright-cli fill "#Name" "<USER_NAME>"
playwright-cli fill "#address1" "<ADDRESS_LINE_1>"
playwright-cli fill "#address2" "<ADDRESS_LINE_2>"
playwright-cli fill "#address3" "<ADDRESS_LINE_3>"
playwright-cli fill "#pincode" "<PINCODE>"
playwright-cli fill "#phone" "<USER_PHONE>"
```
> Gender (Male), Country (India), State (Karnataka), District, Taluk, Mobile and Email are pre-filled from the OTP session — no action needed.
> To change gender to Female: `playwright-cli click "input[name='sex'][value='F']"`

#### Location, Educational Status and BPL
```
playwright-cli click "input[name='status'][value='U']"
playwright-cli click "input[name='educational_Status'][value='L']"
playwright-cli select "#BPL" "N"
```
> Location radio uses `name="status"` (not `name="location"`). Urban = `U`, Rural = `R`.
> `educational_Status` values: `L` = Graduate & above, `B` = Below Graduate. Adjust if needed.

#### RTI Request Text
> Update the request text below for each new request before running.
```
playwright-cli fill "#Description" "<RTI_TEXT>"
```
> Note: Only alphabets A–Z a–z, numbers 0–9 and special characters `, . - _ ( ) / @ : & \ %?` are allowed in the request text.

### 6. Read Form Captcha and Submit
```
playwright-cli screenshot "getByRole('img', { name: 'captchaImage' })" --filename=karnataka_rti_form_captcha.png
```
> Read `karnataka_rti_form_captcha.png` and fill it:
```
playwright-cli fill "[id='6_letters_code']" "<CAPTCHA_VALUE_READ_FROM_IMAGE>"
playwright-cli click "getByRole('button', { name: 'Submit' })"
```
> A confirm dialog appears asking to verify the selected public authority — accept it:
```
playwright-cli dialog-accept
```
> The page navigates to `payment.php`.

### 7. RTI Payment Page — Select Mode and Pay
> The payment mode radio button `#OnlineMode` must be explicitly clicked — it appears selected visually but is not checked by default.
```
playwright-cli click "#OnlineMode"
playwright-cli click "getByRole('button', { name: 'Pay' })"
```
> The page redirects to the Khajane-II payment gateway.

### 8. Khajane-II Gateway — UPI via SBI e-Pay

> **CRITICAL — Read before acting:**
> The Khajane captcha token is **one-time use on the server**. Any call to `CapchaValidationServlet` (including AJAX pre-tests via `eval`) **consumes the token** and makes the form submission fail. Never test the captcha before submitting. Never reload the captcha image via JS src manipulation. Only use the reload button if the captcha is truly unreadable — but do it once, not repeatedly.

**a) Select UPI as payment mode** (only one select is visible at this point):
```
playwright-cli select "select" "10700169"
```

**b) Wait for the Types of Aggregator dropdown to appear** (loaded via AJAX after UPI selection). Take a snapshot to find its ref:
```
playwright-cli snapshot
```
> Look for a combobox with options "ICICI e-Pay" and "SBI e-Pay". Note its ref (typically `e151` or similar — varies per page load).

**c) Select SBI e-Pay as aggregator** using the ref from the snapshot:
```
playwright-cli select <AGGREGATOR_REF> "10700451"
```

**d) Screenshot the captcha AFTER all dropdown selections are done** — take a snapshot first to get current refs for the captcha image, textbox, and checkboxes:
```
playwright-cli snapshot
```
> Then screenshot using the captcha image ref (e.g., `e161`):
```
playwright-cli screenshot <CAPTCHA_IMAGE_REF> --filename=karnataka_rti_khajane_captcha.png
```
> Read `karnataka_rti_khajane_captcha.png` and note the captcha text.

**e) Fill captcha, check both disclaimers, and submit immediately** — do all in one sequence without any intervening AJAX calls:
```
playwright-cli fill <CAPTCHA_TEXTBOX_REF> "<CAPTCHA_VALUE_READ_FROM_IMAGE>"
playwright-cli check <CONDITIONONE_CHECKBOX_REF>
playwright-cli check <CONDITIONTWO_CHECKBOX_REF>
playwright-cli click "[id='viewns_Z7_I2K611S0OOUI80QI7UE1HU0087_:deptGtwyForm:j_id_5j']"
```
> Submit button has Kannada text — use the ID selector above. A 5000ms timeout on click is expected and harmless.

> **If "Invalid Captcha code entered":** Do NOT pre-test via AJAX. Do `playwright-cli reload` to get a fresh captcha (the challan persists). Re-select UPI and SBI e-Pay, then re-read the fresh captcha and submit. After reload the Mode of Payment resets to "Select" — re-select it.

> **If the Khajane page fails to load (chrome-error://):** Go back to the RTI payment page and click Pay again. If the payment URL has expired, restart the full application from Step 1.

### 9. SBI ePay Gateway — Select UPI QR and Pay
> The page lands on the SBI ePay gateway showing Debit/Credit Card by default. Click the UPI tab on the left panel:
```
playwright-cli click ".activeUPI"
```
> Select UPI QR. The radio button may be hidden — use JS to expand the section and click it:
```
playwright-cli eval "(() => { let el = document.getElementById('collapseup'); if(el){ el.style.display='block'; el.style.visibility='visible'; } let r = document.getElementById('upiQR1'); if(r) r.click(); return (r ? r.checked : 'not found'); })()"
```
> Click Pay Now to generate the QR code:
```
playwright-cli click "getByRole('button', { name: 'Pay Now' })"
```

### 10. ⏸ Scan QR Code — Human Input Required
> The QR code page loads with a countdown timer (~5 minutes). Take a screenshot:
```
playwright-cli screenshot --filename=karnataka_rti_upi_qr.png
```
> **Tell the user:** "Please scan the QR code in `karnataka_rti_upi_qr.png` with any UPI app (GPay, PhonePe, Paytm, etc.) to pay ₹10. The QR expires in ~5 minutes — please act quickly."
> Wait for the user to confirm payment is done before proceeding.

### 11. Capture Confirmation and Registration Number
> After the user confirms payment, the page auto-redirects to the Karnataka RTI portal with the payment slip. Take a full-page screenshot:
```
playwright-cli screenshot --full-page --filename=karnataka_rti_confirmed.png
```
> The confirmation page shows:
> - **Registration Number** (format: `KNDDA/R/YYYY/NNNNN`) — read and share this with the user
> - Name, Date of Filing, Public Authority filed with
> - Nodal Officer telephone and email
>
> Inform the user:
> - Their **RTI Registration Number** (read it from the page)
> - Status can be tracked at https://rtionline.karnataka.gov.in → **View Status** using their registered mobile number

---

## Department ID Reference

All Karnataka public authorities with their numeric IDs for `<MINISTRY_ID>`:
Refer Karnataka_RTI_Database.xlsx for more info

> To search for a department by keyword while on the form page: `playwright-cli eval "Array.from(document.querySelector('#MinistryId').options).filter(o => o.text.toLowerCase().includes('keyword')).map(o => o.text + '|' + o.value).join('\\n')"`

---

## Troubleshooting

| Issue | Fix |
|---|---|
| OTP does not match | Ask user for the **latest** OTP from their mobile — they may have a stale one |
| OTP limit exceeded | Daily OTP limit hit. Stop and resume the next day |
| Captcha keeps failing (RTI portal) | Re-screenshot and re-read carefully. Common misreads: `g`↔`9`, `1`↔`l`↔`I`, `0`↔`O` |
| Captcha keeps failing (Khajane) | Do `playwright-cli reload`, re-select UPI + SBI e-Pay, re-screenshot and submit |
| Khajane page fails to load | Go back to RTI payment page and click Pay again |
| "Invalid Captcha" on Khajane | Never test via AJAX. Reload the page and retry |
| QR code expired before payment | Re-run from Step 7 — the request is saved, only payment needs to be retried |

---

## Notes

- **URL:** Start at `https://rtionline.karnataka.gov.in/guidelines.php?request` (direct, skips home page)
- **RTI Fee:** ₹10 (no processing fee or GST for UPI QR payments)
- **OTP:** Sent to **mobile only** — not email. OTP is 6 digits. Daily limit applies — multiple failed attempts exhaust the limit quickly.
- **Captcha field selector (RTI portal):** `[id='6_letters_code']`
- **Captcha field selector (Khajane-II):** Use the ref from snapshot (e.g., `e159`) — the field ID is `captchaTextcode`
- **Captcha tips:** `g` vs `9` is the most common ambiguity — look for a descender (tail below baseline) to identify `g`.
- **Khajane captcha is one-time use:** Never call `CapchaValidationServlet` via AJAX/eval before submitting — it consumes the token.
- **Khajane captcha does NOT change** when UPI or aggregator dropdowns are selected. Screenshot it after all dropdowns are set.
- **Khajane aggregator dropdown** appears via AJAX after UPI is selected. Use `playwright-cli snapshot` to find its ref.
- **Checkboxes** appear after SBI e-Pay is selected. Find refs via snapshot and check both before submitting.
- **Submit button (Khajane)** has Kannada text — use ID: `[id='viewns_Z7_I2K611S0OOUI80QI7UE1HU0087_:deptGtwyForm:j_id_5j']`
- **Public authority** is selected from `#MinistryId`. Always use the numeric value ID.
- **Location** radio uses `name="status"`: Urban = `U`, Rural = `R`.
- **`#OnlineMode`** on the RTI payment page must be explicitly clicked — not checked by default.
- **SBI ePay UPI tab:** Use `.activeUPI` class. The UPI QR radio `#upiQR1` may be hidden — use the JS eval above.
- The **registration number is shown directly on screen** after payment (unlike the central RTI portal which sends it by email).