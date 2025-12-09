# leave-automation-v1.1

## Employee Leave Request Automation — Version 1

A simple end-to-end automation that processes employee leave requests using a Flask webhook service.  
This is the **first, core version** of the project — focused only on:

**User Form → Flask Webhook → OpenAI Classification → Slack Notification → Google Sheets Log → HTTP Response**

This version does *not* include n8n, Docker orchestration, or multi-step workflow routing.  
It is intentionally minimal to clearly demonstrate API integration, LLM prompting, and workflow automation fundamentals.

---

## 📌 Features

### ✔ 1. HTML Form Submission  
A simple `form.html` sends leave request data to the Flask webhook.

### ✔ 2. Flask Webhook  
Receives JSON payload from the form, validates fields, and triggers the automation.

### ✔ 3. OpenAI Classification  
Uses OpenAI’s latest API to generate a short summary and urgency score for the leave request.

### ✔ 4. Slack Notification  
Posts a formatted message (name, department, date, reason, urgency score) to a Slack channel via Incoming Webhook.

### ✔ 5. Google Sheets Logging  
Appends each leave request to a Google Sheet using a Google Cloud Service Account.

### ✔ 6. JSON Response  
Returns a clean JSON response back to the user/form.

This version demonstrates the fundamental responsibilities of a **Solution Engineer**:  
API integration → LLM logic → workflow execution → audit logging → communication automation.

---

## 🧠 System Architecture (Version 1)

User Form
↓
Flask Webhook (app.py)
↓
OpenAI (classification + summary)
↓
Slack Notification
↓
Google Sheets Append Log
↓
HTTP JSON Response (success/failure)


---

## 🔑 Requirements

- Python 3.8+
- pip
- A Slack Incoming Webhook URL
- OpenAI API Key
- Google Cloud Service Account JSON key
- A Google Sheet (shared with the service account email)

---

## ⚙️ Installation & Setup

### 1️⃣ Create virtual environment & install deps

**Windows PowerShell:**
```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt

Mac/Linux:
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt


2️⃣ Create a .env file

Copy .env.example → .env and fill values:

OPENAI_API_KEY=YOUR_KEY
SLACK_WEBHOOK_URL=YOUR_SLACK_WEBHOOK
SHEETS_CREDENTIALS_JSON_PATH=/absolute/path/to/credentials.json
SHEETS_SPREADSHEET_ID=YOUR_SPREADSHEET_ID
PORT=5000


Do NOT commit .env or credentials.json.

3️⃣ Prepare Google Sheets credentials

Go to Google Cloud Console

Enable Google Sheets API and Google Drive API

Create a Service Account

Create a JSON key → download it

Put it anywhere on your machine

Share your Google Sheet with the service account email (Editor access)

4️⃣ Run Flask
python app.py


Flask runs on:

http://127.0.0.1:5000

5️⃣ Submit a test request
Option A — Use the form

Open:

templates/form.html


Then click Submit.

Option B — Use PowerShell
$body = @{
  employee_name = "Rakib"
  department = "IT"
  leave_date = "2025-11-21"
  reason = "Feeling sick"
} | ConvertTo-Json

Invoke-RestMethod -Method Post -Uri "http://127.0.0.1:5000/webhook" -ContentType "application/json" -Body $body

Option C — Use curl
curl -X POST http://127.0.0.1:5000/webhook \
  -H "Content-Type: application/json" \
  -d '{"employee_name":"Rakib","department":"IT","leave_date":"2025-11-21","reason":"Feeling sick"}'

📤 Expected Outputs
✔ Slack Message

You should see a nicely formatted message in your Slack channel.

✔ Google Sheets Row

A new row containing:

employee name

department

leave date

reason

LLM summary

urgency classification

timestamp

✔ API Response
{
  "ok": true,
  "summary": "...",
  "urgency": "High"


🧪 Troubleshooting
Issue	----------------------------- Fix
Invalid URL 'None' ---------------- Missing .env variable
Google Sheets API not enabled -----	Enable Sheets API in correct Google Cloud project
Slack shows nothing	--------------- Webhook URL incorrect or revoked
OpenAI fails ---------------------- API key missing or model name incorrect
