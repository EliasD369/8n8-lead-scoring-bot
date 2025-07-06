n8n Lead Scoring & Notification Bot

Overview

This n8n workflow automates lead processing end-to-end for REM Waste (or similar use cases). It performs the following steps:

1. Webhook Trigger: Receives incoming lead submissions via HTTP POST.


2. Extract Payload: Flattens the JSON payload from body to top-level fields.


3. Validate Required Fields: Ensures full_name, email, budget, and interest_level are present and valid.


4. Score Lead: Applies custom JavaScript logic to assign a score (Cold, Warm, Hot) based on budget thresholds and interest level.


5. Store in Airtable: Creates a new record in an Airtable base with all lead details and the calculated score.


6. Check Hot Lead: Routes only Hot leads to the sales channel.


7. Notify Sales Slack: Sends an instant message to a Slack channel for high-value leads.


8. Simulate Form (Bonus): Optionally re-triggers the webhook to simulate a second submission.


9. 2-Minute Delay (Bonus): Pauses execution to simulate lead nurturing.


10. Follow-Up Slack (Bonus): Sends a follow-up Slack message after the delay.



Setup Instructions

1. Import Workflow into n8n

1. In your n8n instance, click Import → File in the workflow canvas.


2. Select lead_scoring_workflow.json and click Import.



2. Configure Credentials

Airtable

1. Create or log in to your Airtable account.


2. Create a new base (e.g., Lead Scoring) and a table named Leads with fields:

full_name (Single line text)

email (Email)

phone (Single line text)

company_size (Number or Single line text)

budget (Number)

interest_level (Single select: Low, Medium, High)

score (Single select: Cold, Warm, Hot)



3. Generate a Personal Access Token (PAT) in your Airtable account settings with data.records:read and data.records:write scopes for this base.


4. In n8n, go to Credentials → New → Airtable API.


5. Paste your PAT and Base ID (the appXXXXX in your base URL), then save.



Slack

1. Create a Slack app or use an existing one in your workspace.


2. Add the chat:write scope under OAuth & Permissions.


3. Install or reinstall the app to your workspace.


4. Copy the Bot User OAuth Token (xoxb-...).


5. In n8n, go to Credentials → New → Slack API.


6. Paste the token and save.



Usage

Triggering the Workflow

Option A: Direct POST

curl -X POST "https://<YOUR_N8N_HOST>/webhook/lead-scoring" \
  -H "Content-Type: application/json" \
  -d '{
    "full_name":"Jane Doe",
    "email":"jane@example.com",
    "phone":"123-456-7890",
    "company_size":"50",
    "budget":"6000",
    "interest_level":"High"
  }'

Option B: Form Simulation Use the built-in Simulate Form HTTP Request node or connect a Typeform/Tally.so form to the same webhook URL.


Expected Behavior

1. The webhook returns { "status": "received" }.


2. The lead is scored and stored in Airtable.


3. If score === "Hot", a Slack message is posted to your configured channel.


4. (Bonus) After 2 minutes, a follow-up message is sent to nurture the lead.
