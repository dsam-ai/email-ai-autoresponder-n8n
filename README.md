# Email AI Auto-Responder — n8n + Groq

![n8n](https://img.shields.io/badge/n8n-workflow-orange?logo=n8n)
![Groq](https://img.shields.io/badge/Groq-llama--3.3--70b-blue)
![Gmail](https://img.shields.io/badge/Gmail-integrated-red?logo=gmail)
![License](https://img.shields.io/badge/license-MIT-green)

Automatically reads incoming Gmail messages, uses **Groq LLM (llama-3.3-70b-versatile)** to categorize and draft a professional reply, then sends the response — all without touching your inbox.

---

## Use Case

Perfect for:
- **Freelancers & agencies** who receive high volumes of client enquiries
- **Support teams** wanting instant first-response coverage
- **Sales teams** that need instant lead acknowledgement 24/7
- **Anyone** who wants AI-powered inbox zero

Typical categories handled: `Support`, `Sales`, `Spam`, `Other`

---

## Architecture

```
Gmail Inbox (unread)
        │
        ▼
 Gmail Trigger (polls every minute)
        │
        ▼
 Groq AI (llama-3.3-70b-versatile)
   ├─ Categorizes email (Support / Sales / Spam / Other)
   └─ Drafts professional reply body
        │
        ▼
 Parse AI Response (Code node)
        │
        ▼
 IF Not Spam?
   ├── YES ──► Gmail Send Reply ──► Label "auto-responded"
   └── NO  ──► Label "spam-detected"
```

---

## Nodes

| # | Node | Type | Purpose |
|---|------|------|---------|
| 1 | Gmail Trigger | `n8n-nodes-base.gmailTrigger` | Polls for unread emails |
| 2 | Groq AI Categorize & Draft | `n8n-nodes-base.httpRequest` | Calls Groq API |
| 3 | Parse AI Response | `n8n-nodes-base.code` | Extracts JSON from LLM response |
| 4 | IF Not Spam | `n8n-nodes-base.if` | Routes spam vs. legitimate email |
| 5 | Gmail Send Reply | `n8n-nodes-base.gmail` | Sends the AI-drafted reply |
| 6 | Label as Auto-Responded | `n8n-nodes-base.gmail` | Tags thread for deduplication |
| 7 | Label Spam | `n8n-nodes-base.gmail` | Tags detected spam |

---

## Setup Guide

### 1. Prerequisites
- [n8n](https://n8n.io) instance (cloud or self-hosted)
- Google account with Gmail API enabled
- Groq API account → [console.groq.com](https://console.groq.com)

### 2. Configure Credentials

#### Gmail OAuth2
1. Go to **Google Cloud Console** → APIs & Services → Credentials
2. Create an **OAuth 2.0 Client ID** (Web application)
3. Add redirect URI: `https://your-n8n-instance.com/rest/oauth2-credential/callback`
4. In n8n: Settings → Credentials → New → **Gmail OAuth2** → paste Client ID & Secret

#### Groq API Key
1. Visit [console.groq.com/keys](https://console.groq.com/keys) → Create API Key
2. In n8n: Settings → Credentials → New → **HTTP Header Auth**
   - Name: `Groq API Key`
   - Header Name: `Authorization`
   - Header Value: `Bearer YOUR_GROQ_API_KEY`

### 3. Import Workflow
1. In n8n: Workflows → Import from File → select `workflow.json`
2. Assign the credentials created above to each node
3. Create Gmail labels: `auto-responded` and `spam-detected` in your Gmail settings
4. **Activate** the workflow

### 4. Customize the AI Prompt
Edit the system prompt in the **Groq AI Categorize & Draft** node to match your business tone, add specific instructions (e.g., your company name, pricing info, team names).

---

## Configuration

| Variable | Where to set | Description |
|----------|-------------|-------------|
| Poll frequency | Gmail Trigger node | How often to check inbox (default: every minute) |
| AI model | HTTP Request body | Change `llama-3.3-70b-versatile` to any Groq model |
| Gmail filter | Gmail Trigger `q` param | Add filters like `from:client@example.com` |

---

## Example AI Output

```json
{
  "category": "Sales",
  "confidence": 0.92,
  "subject_reply": "Re: Pricing enquiry for your automation services",
  "reply_body": "Thank you for reaching out! I'd love to discuss how we can automate your workflows..."
}
```

---

## Author

Built by **Dilovar Sam** — AI Automation Freelancer  
n8n cloud: [dsam.app.n8n.cloud](https://dsam.app.n8n.cloud)

---

## License

MIT
