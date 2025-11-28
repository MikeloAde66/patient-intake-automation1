# Appointment Scheduling Automation v1.0

**Quick Setup - 45 Minutes**

## What This Does
- Real-time appointment booking with conflict detection
- Auto-syncs to Google Calendar
- Sends instant email + SMS confirmations
- Automated 24-hour reminders
- Logs all bookings to Google Sheets

---

## Setup

### 1. Google Sheets (5 min)

Create spreadsheet: **Appointments - [Your Practice]**

**Appointments** sheet columns:
```
appointmentId, patientName, patientEmail, patientPhone, appointmentDate, appointmentTime, appointmentType, status, calendarEventId, notes, bookedAt
```

Copy Sheet ID from URL: `docs.google.com/spreadsheets/d/[SHEET_ID]/edit`

### 2. Twilio Setup (10 min)

**Sign up for Twilio:**
1. Go to twilio.com/try-twilio
2. Sign up (free trial: $15 credit)
3. Get a phone number (costs $1/month after trial)
4. Copy your credentials:
   - Account SID
   - Auth Token
   - Your Twilio phone number

**Note:** Free trial can only send to verified numbers. Upgrade to send to any number.

### 3. n8n Import (15 min)

1. Login to n8n → **Import Workflow**
2. Upload `appointment-scheduling-automation.json`

**Add Credentials:**
- Google Calendar OAuth2
- Google Sheets OAuth2  
- Gmail OAuth2
- HTTP Header Auth for Twilio:
  - Name: `Authorization`
  - Value: `Basic [BASE64_ENCODED]`
  
**To create Twilio auth:**
```bash
echo -n "ACCOUNT_SID:AUTH_TOKEN" | base64
```
Use the output as your Authorization value: `Basic [output]`

**Set Environment Variables:**
- `APPOINTMENTS_SHEET_ID` = your Sheet ID
- `TWILIO_ACCOUNT_SID` = your Account SID
- `TWILIO_PHONE_NUMBER` = your Twilio number (format: +15551234567)

### 4. Test Booking (10 min)

1. Execute webhook node → Copy webhook URL
2. Open `booking-form.html` in browser
3. Update line 285 with your webhook URL
4. Submit test appointment
5. Check:
   - ✓ Google Calendar event created
   - ✓ Email confirmation received
   - ✓ SMS confirmation received
   - ✓ Google Sheets log entry

### 5. Deploy Booking Form (5 min)

**Option A: Embed on your website**
```html
<iframe src="booking-form.html" width="100%" height="800px" frameborder="0"></iframe>
```

**Option B: Use as standalone page**
- Upload `booking-form.html` to your web host
- Link from your website

---

## Features

### Conflict Detection
- Checks Google Calendar before booking
- Returns error if time slot taken
- Suggests alternative times

### Automatic Reminders
- Runs daily at 9 AM
- Finds appointments 24 hours away
- Sends email + SMS reminders automatically

### Appointment Types
Default types (customize in form):
- General Consultation
- Follow-up Visit
- Annual Physical
- Lab Work
- Procedure

---

## Customization

### Change Appointment Duration
In "Process & Validate" node, line 19:
```javascript
endDateTime.setMinutes(endDateTime.getMinutes() + 30); // Change 30 to your duration
```

### Change Reminder Time
In "Daily Reminder Check" node:
```
cronExpression: "0 9 * * *"  // Format: minute hour day month weekday
```
Examples:
- `0 18 * * *` = 6 PM daily
- `0 9,18 * * *` = 9 AM and 6 PM daily

### Add More Appointment Types
Edit `booking-form.html`, line 198:
```html
<option value="New Type">New Type</option>
```

### Customize Email Templates
Edit the HTML in "Send Email Confirmation" node

---

## Cost Breakdown

**Free Tier:**
- n8n: Free (self-hosted) or $20/month (cloud)
- Google Workspace: Free (personal Gmail) or $6/user/month (business)
- Twilio: $15 free credit, then:
  - $1/month per phone number
  - $0.0075 per SMS sent
  - $0.0079 per SMS received

**Example monthly cost:**
- 100 appointments/month
- 2 SMS per appointment (confirmation + reminder)
- Total: $1 (number) + $1.50 (SMS) = **$2.50/month**

---

## Troubleshooting

**Calendar conflict false positive:**
- Check Google Calendar time zone settings
- Verify appointment duration in workflow

**SMS not sending:**
- Twilio trial only sends to verified numbers
- Check phone number format: +1XXXXXXXXXX
- Verify Twilio Auth credentials

**Reminders not sending:**
- Workflow must be Active
- Check cron schedule is set correctly
- Test manually: execute "Daily Reminder Check" node

**Email in spam:**
- Use business Gmail (not personal)
- Set up SPF/DKIM records
- Ask patients to whitelist your email

---

## Security

✓ Validates all input data
✓ Checks for past-date bookings  
✓ OAuth2 for Google services
✓ Encrypted Twilio API calls
✓ Phone number sanitization

**HIPAA Notes:**
- Sign BAA with Twilio (Business/Enterprise plan required)
- Sign BAA with Google Workspace
- Restrict Sheet access
- Enable 2FA on all accounts

---

## Advanced Features

### Send Booking Confirmation to Staff
Add Gmail node after "Log to Sheets":
- Send to: office@yourpractice.com
- Subject: "New Appointment: {{ $json.patientName }}"

### Add Cancellation Link
In email template, add:
```html
<a href="https://yoursite.com/cancel?id={{ $json.appointmentId }}">Cancel Appointment</a>
```
Then create cancel workflow with webhook

### Multi-Provider Scheduling
Add dropdown in booking form for provider selection
Update calendar node to use different calendars per provider

---

## Support

Email: support@protoautomations.com  
Response: Within 24 hours (business days)  
Period: 30 days from purchase

---

**ProtoLabs Healthcare Automations**  
More products at: protoautomations.com
