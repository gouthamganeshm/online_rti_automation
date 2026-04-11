# RTI Online - File an RTI Request with Claude Code

Automate filing an RTI request on [rtionline.gov.in](https://rtionline.gov.in/) using [Claude Code](https://claude.ai/code) and its built-in Playwright browser skill.

## Prerequisites

- Claude Code installed and running
- The `playwright-cli` skill available (comes bundled with Claude Code)
- A valid Indian mobile number and email address
- ₹10 for the RTI filing fee (paid via UPI QR scan)

---

## How to Use

1. Copy this file into your Claude Code project directory
2. Replace all `<PLACEHOLDER>` values in the [Configuration](#configuration) section below with your details
3. Tell Claude Code: `run instructions from rti_submit_request_template.md`
4. Follow along — Claude will pause twice for human input:
   - **OTP** — sent to your email after credentials are verified
   - **UPI Payment** — scan the QR code to pay ₹10

---

## Configuration

Replace these placeholders before running:

| Placeholder | Description | Example |
|---|---|---|
| `<USER_EMAIL>` | Your email address | `john@gmail.com` |
| `<USER_MOBILE>` | 10-digit mobile number | `12345698` |
| `<USER_NAME>` | Your full name | `John Doe` |
| `<ADDRESS_LINE_1>` | Address line 1 (name/building) | `John Doe` |
| `<ADDRESS_LINE_2>` | Address line 2 (street/locality) | `12 MG Road, Near Bus Stand` |
| `<ADDRESS_LINE_3>` | Address line 3 (city, state) | `Mumbai, Maharashtra` |
| `<PINCODE>` | 6-digit PIN code | `400001` |
| `<STATE_NAME>` | State name as on RTI portal | `Maharashtra` |
| `<USER_PHONE>` | Phone number for form (can be same as mobile) | `478968213` |
| `<MINISTRY_ID>` | Numeric ministry ID (see [Ministry ID Reference](#ministry-id-reference)) | `47` |
| `<DEPARTMENT_NAME>` | Department name exactly as shown on RTI portal | `Department of Health & Family Welfare` |
| `<RTI_TEXT>` | Your RTI request body text | *(see below)* |

---

## Steps

### 1. Open RTI Online
```
playwright-cli open https://rtionline.gov.in/
```

### 2. Click "Submit Request"
```
playwright-cli click "getByRole('link', { name: 'Submit Request' })"
```

### 3. Accept Guidelines and Proceed
```
playwright-cli check "getByRole('checkbox')"
playwright-cli click "getByRole('button', { name: 'Submit' })"
```

### 4. Fill Email and Mobile
```
playwright-cli fill "#Email" "<USER_EMAIL>"
playwright-cli fill "getByRole('textbox', { name: 'Enter Mobile Number' })" "<USER_MOBILE>"
```

### 5. Read Captcha and Fill It
> Screenshot the captcha image and read it directly — do not ask the user.
```
playwright-cli screenshot "getByRole('img', { name: 'captchaImage' })" --filename=rti_submit_captcha.png
```
> Read `rti_submit_captcha.png` using the Read tool, interpret the text, then fill:
```
playwright-cli fill "[id='6_letters_code']" "<CAPTCHA_VALUE_READ_FROM_IMAGE>"
```
> **Captcha tips:** Font is distorted — watch for ambiguous characters: `1`/`l`/`I`, `0`/`O`, `1`/`/`, `f`/`r`. Prefer the simpler alphanumeric reading. Captcha is case-insensitive.

### 6. Submit Credentials and Accept OTP Dialog
```
playwright-cli click "getByRole('button', { name: 'Submit' })"
playwright-cli dialog-accept
```

### 7. ⏸ Wait for OTP — Human Input Required
> OTP is sent to `<USER_EMAIL>` **only** (not SMS).
> **Ask the user:** "An OTP has been sent to `<USER_EMAIL>`. Please check your inbox (including spam) for the latest email from RTI Online and share the OTP."
> **Important:** If OTP fails ("OTP does not match"), the user may have provided a stale OTP from an older email. Ask them to check for the **latest** email and retry.

### 8. Fill OTP and Submit
```
playwright-cli fill "getByRole('textbox', { name: 'Enter OTP Number' })" "<OTP_FROM_USER>"
playwright-cli click "getByRole('button', { name: 'Submit' })"
```
> If "OTP does not match" appears, ask the user for the latest OTP and retry this step.

### 9. Fill the RTI Request Form

#### Select Ministry
> Always use the numeric ministry ID — text-based selection is unreliable.
```
playwright-cli select "#MinistryId" "<MINISTRY_ID>"
```

#### Select Public Authority (Department)
> The `#DepartmentId` dropdown is briefly disabled while loading — it will always time out. That is expected. Immediately snapshot after the timeout to catch the pending confirm dialog.
```
playwright-cli select "#DepartmentId" "<DEPARTMENT_NAME>"
playwright-cli snapshot
playwright-cli dialog-accept
```

#### Personal Details
```
playwright-cli fill "#ConfirmEmail" "<USER_EMAIL>"
playwright-cli fill "#Name" "<USER_NAME>"
```
> Gender defaults to Male. Change if needed: `playwright-cli click "input[name='sex'][value='F']"` for Female.

#### Address
```
playwright-cli fill "#address1" "<ADDRESS_LINE_1>"
playwright-cli fill "#address2" "<ADDRESS_LINE_2>"
playwright-cli fill "#address3" "<ADDRESS_LINE_3>"
playwright-cli fill "#pincode" "<PINCODE>"
```

#### State, Status, Education, Phone, BPL
> Country defaults to India — no action needed.
```
playwright-cli select "#stateId" "<STATE_NAME>"
playwright-cli click "input[name='status'][value='U']"
playwright-cli click "input[name='educational_Status'][value='L']"
playwright-cli fill "#phone" "<USER_PHONE>"
playwright-cli select "#BPL" "No"
```
> `status` values: `U` = Below Poverty Line citizen, `G` = General. Adjust if needed.
> `educational_Status` values: `L` = Graduate & above, `B` = Below Graduate. Adjust if needed.

#### RTI Request Text
```
playwright-cli fill "#Description" "<RTI_TEXT>"
```

### 10. Read Form Security Code and Fill It
> Screenshot the captcha element again (same element, refreshed code) and read it directly.
```
playwright-cli screenshot "getByRole('img', { name: 'captchaImage' })" --filename=rti_form_captcha.png
```
> Read `rti_form_captcha.png`, interpret the code, then fill:
```
playwright-cli fill "[id='6_letters_code']" "<SECURITY_CODE_READ_FROM_IMAGE>"
```

### 11. Submit Form (with captcha retry handling)
```
playwright-cli click "getByRole('button', { name: 'Make Payment' })"
playwright-cli dialog-accept
```
> Check if captcha passed:
```
playwright-cli eval "document.querySelector('p strong')?.textContent || 'ok'"
```
> **If result is `"Captcha code does not match"`:** The button changes to `#requestSubmit`. Re-screenshot, re-read, and retry:
```
playwright-cli screenshot "getByRole('img', { name: 'captchaImage' })" --filename=rti_form_captcha.png
playwright-cli fill "[id='6_letters_code']" "<NEW_CAPTCHA_VALUE>"
playwright-cli click "#requestSubmit"
playwright-cli dialog-accept
```
> Repeat until the page navigates to `payment.php`.

---

## Payment Steps (SBI Gateway)

### 12. Select UPI as Payment Mode
```
playwright-cli click "#OnlineModeId"
playwright-cli click "[id='01']"
playwright-cli click "getByRole('button', { name: 'Pay' })"
playwright-cli dialog-accept
```

### 13. Select UPI on SBI Gateway
> The UPI button is overlapped by another element — use JS eval to bypass it:
```
playwright-cli eval "document.querySelector('a[onclick=\"paySubmitUPI(\'UPI\')\"]').click()"
```

### 14. Confirm Payment Details
> Verify the amount shown is ₹10, then click Confirm:
```
playwright-cli click "getByRole('button', { name: 'Confirm' })"
```

### 15. ⏸ Scan QR Code — Human Input Required
> Take a full-page screenshot and open it for the user:
```
playwright-cli screenshot --full-page --filename=rti_upi_qr.png
```
> **Tell the user:** "Please scan the QR code in `rti_upi_qr.png` with any UPI app (GPay, PhonePe, Paytm, etc.) to pay ₹10. The QR expires in ~2 minutes — act quickly."
> Wait for the user to confirm payment before proceeding.

### 16. Capture SBI Payment Confirmation
> Do NOT call `dialog-accept` immediately — the success dialog appears asynchronously. Snapshot first to catch it:
```
playwright-cli snapshot --depth=2
```
> A modal will appear: `"Your transaction status is Success."` — accept it:
```
playwright-cli dialog-accept
```
> Screenshot the SBI remittance confirmation page for your records:
```
playwright-cli screenshot --full-page --filename=rti_registration_confirmed.png
```

### 17. Wait for RTI Online Redirect
> SBI auto-redirects to RTI Online after ~10 seconds. Snapshot to wait for it:
```
playwright-cli snapshot --depth=2
```
> The page lands on `doubleverification.php` showing **"The Page You are referring cannot be served"** — this is a **known, expected** RTI Online server behaviour after the SBI redirect. It does NOT indicate failure.

> Inform the user:
> - ✅ Payment confirmed by SBI (Transaction Status: **Success**)
> - The **RTI Registration Number** will arrive at `<USER_EMAIL>` via email within minutes
> - Track status anytime at https://rtionline.gov.in/ → **View Status** → enter your email

---

## Ministry ID Reference

Common ministry numeric IDs for `<MINISTRY_ID>`:

| ID | Ministry / Department |
|---|---|
| `8` | Cabinet Secretariat |
| `12` | Department of Agriculture, Cooperation & Farmers Welfare |
| `23` | Department of Atomic Energy |
| `24` | Department of Commerce |
| `27` | Department of Telecommunications |
| `28` | Department of Posts |
| `29` | Ministry of Electronics & Information Technology |
| `34` | Department of Defence |
| `39` | Ministry of Environment, Forest and Climate Change |
| `40` | Ministry of External Affairs |
| `44` | Department of Revenue |
| `47` | Department of Health & Family Welfare |
| `48` | Ministry of AYUSH |
| `51` | Ministry of Heavy Industries |
| `53` | Ministry of Home Affairs |
| `59` | Ministry of Education |
| `61` | Department of Higher Education |
| `63` | Ministry of Information & Broadcasting |
| `64` | Ministry of Labour & Employment |
| `69` | Ministry of Corporate Affairs |
| `70` | Ministry of New & Renewable Energy |
| `71` | Ministry of Earth Sciences |
| `75` | Department of Personnel & Training |
| `78` | Ministry of Petroleum & Natural Gas |
| `80` | Ministry of Power |
| `81` | Ministry of Railways |
| `83` | Ministry of Rural Development |
| `86` | Ministry of Drinking Water and Sanitation |
| `88` | Department of Science & Technology |
| `93` | Ministry of Social Justice & Empowerment |
| `94` | Department of Space |
| `96` | Ministry of Steel |
| `97` | Ministry of Ports, Shipping and Waterways |
| `98` | Ministry of Textiles |
| `100` | Ministry of Tourism |
| `103` | Ministry of Housing & Urban Affairs |
| `104` | Ministry of Road Transport & Highways |
| `107` | Ministry of Youth Affairs & Sports |

> For the full list, visit https://rtionline.gov.in/ → Submit Request → Select Ministry dropdown, or refer to the [RTI portal's public authority list](https://rtionline.gov.in/request/allpa.php).

---

## Troubleshooting

| Issue | Fix |
|---|---|
| OTP does not match | Ask user for the **latest** OTP email — they may have a stale one from a previous session |
| Captcha keeps failing | Re-screenshot and re-read carefully. Common misreads: `1`↔`l`↔`I`, `0`↔`O`, `f`↔`r` |
| `#DepartmentId` times out | Expected — always snapshot immediately after and accept the dialog |
| "Make Payment" button missing after captcha fail | Use `#requestSubmit` instead |
| SBI page shows blank / no UPI button | Use JS eval: `playwright-cli eval "document.querySelector('a[onclick...]').click()"` |
| RTI page shows "Page cannot be served" after payment | Expected — registration number arrives via email |
| QR code expired before payment | Re-run from Step 12 — the request is saved, only payment needs to be retried via `payment.php` |

---

## Notes

- RTI filing fee is **₹10** (non-refundable)
- Captcha fields use selector `[id='6_letters_code']` — same for both login and form pages
- The `#DepartmentId` select **always** times out — this is normal; the selection goes through
- After captcha failure, the submit button changes from "Make Payment" → `#requestSubmit`
- After paying, a success dialog appears on `merchantupiconfirm.htm` — snapshot first to catch it
- The `--full-page` screenshot flag uses a hyphen: `--full-page` (not `--fullPage`)
- UPI Collect via VPA ID is disabled per NPCI guidelines — only QR scan is supported
- The RTI confirmation page error after SBI redirect is a known server-side issue — always expected
