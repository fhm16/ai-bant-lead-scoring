```markdown
# Step-by-Step Setup Guide

Follow these steps to replicate and run the **AI BANT Lead Scoring & Routing** workflow in your own n8n instance.

## Prerequisites
- **n8n** (Self-hosted via Docker/Desktop or n8n Cloud)
- **Google Gemini API Key** (from Google AI Studio)
- **Gmail OAuth2 / App Credential** connected in n8n
- **Google Sheets OAuth2 Credential** connected in n8n

## Workflow Configuration

### Step 1: Import the Workflow
1. Open your n8n dashboard and create a new workflow.
2. Click the top-right menu (`...`) → **Import from File** and select `workflows/bant-lead-scoring-workflow.json`.

### Step 2: Configure Form Trigger (`On form submission`)
Ensure the form contains the following fields:
- `Budget` (Dropdown: `$1000+`, `$500 below`)
- `Authority` (Dropdown: `Yes`, `No`)
- `Timeline` (Dropdown: `1 month`, `Not sure`)

### Step 3: Connect Google Gemini (`Message a model`)
1. Select your **Google Gemini(PaLM) API** credential.
2. Verify the prompt maps the incoming form fields (`Budget`, `Authority`, `Timeline`) and instructs the model to output raw JSON without markdown wrappers.

### Step 4: Configure Routing (`Switch` Node)
- **Mode:** `Rules`
- **Output 1 (`HOT Lead`):** Checks if `{{ JSON.parse($json.content.parts[0].text).status }}` equals `HOT`.
- **Output 2 (`Fallback`):** Routes all remaining (`COLD`) leads to Google Sheets.

### Step 5: Configure Action Nodes
1. **Gmail (`Send a message`):**
   - **To:** Set your sales team recipient email address.
   - **Subject:** `🔥 New HOT Lead Alert: Score {{ JSON.parse($node["Message a model"].json.content.parts[0].text).score }}/100`
   - **Message:** Maps `score`, `status`, and `reason` dynamically from the Gemini output.
2. **Google Sheets (`Append row in sheet`):**
   - Create a Google Sheet with headers: `Budget`, `Authority`, `Timeline`, `Score`, `Reason` (or import `data/cold-leads-template.csv`).
   - Select **Resource:** `Sheet Within Document` and **Operation:** `Append Row`.
   - Map each column to its corresponding form and Gemini JSON expression.