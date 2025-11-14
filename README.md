# MAILY

### AI-Powered Email & Calendar Management Agent

An intelligent n8n workflow that combines the power of AI with your daily communication tools. MAILY acts as your personal assistant, managing emails, scheduling events, and maintaining your contacts — all through natural conversation.

![Status](https://img.shields.io/badge/Status-Active-brightgreen?style=for-the-badge)
![n8n](https://img.shields.io/badge/n8n-Workflow-ff6d5a?style=for-the-badge&logo=n8n&logoColor=white)
![AI Agent](https://img.shields.io/badge/AI-Agent-blue?style=for-the-badge)
![LangChain](https://img.shields.io/badge/LangChain-Enabled-green?style=for-the-badge)

---

## Overview

MAILY is an AI agent that helps you manage your daily communication tasks through natural language. Simply chat with MAILY to:

- **Send emails** via Gmail
- **Schedule calendar events** on Google Calendar
- **Look up contacts** from your centralized database
- **Remember context** across the conversation

No more switching between apps — just tell MAILY what you need!

---

## Features

| Feature | Description |
|---------|-------------|
| **Natural Language Interface** | Chat naturally with the AI agent |
| **Email Management** | Send emails through Gmail with AI-generated content |
| **Calendar Integration** | Create and schedule Google Calendar events |
| **Contact Database** | Access contacts from Google Sheets |
| **Conversation Memory** | Maintains context throughout the session |
| **Smart Validation** | Always verifies contact info before sending |

---

## Workflow Architecture

### Components

```
┌─────────────────┐
│  Chat Trigger   │  User initiates conversation
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   MAILY Agent   │  AI orchestration layer
│   (LangChain)   │
└────────┬────────┘
         │
    ┌────┴────┬────────────┬──────────────┐
    │         │            │              │
    ▼         ▼            ▼              ▼
┌──────┐ ┌────────┐ ┌─────────┐ ┌─────────────────┐
│ Groq │ │ Gmail  │ │ Calendar│ │    Contacts     │
│ LLM  │ │  Tool  │ │  Tool   │ │ Database (GSheets)│
└──────┘ └────────┘ └─────────┘ └─────────────────┘
    │
    ▼
┌─────────────────┐
│ Simple Memory   │  Conversation buffer
└─────────────────┘
```

### Nodes

1. **Chat Trigger** - Entry point for user conversations
2. **MAILY Agent** - LangChain AI Agent that orchestrates all actions
3. **Groq LLM** - Language model for natural language understanding
4. **Gmail Tool** - Sends emails with AI-generated or user-specified content
5. **Google Calendar Tool** - Creates calendar events with attendees
6. **Contacts Database** - Google Sheets integration for contact lookup
7. **Simple Memory** - Buffer window memory for conversation context

---

## Screenshots

### Main Workflow
![Workflow](Workflow%20.png)

### Mail Agent Interface
![Mail Agent](Mail%20Agent.png)

### Mail Agent Logs
![Mail Agent Logs](Mail%20Agent%20Logs.png)

### Contacts Database
![Contacts Database](Contacts%20Database.png)

---

## Setup Instructions

### Prerequisites

- n8n installed (self-hosted or cloud)
- Google Workspace account
- Groq API account (free tier available)

### 1. Google Cloud Setup

#### Enable APIs
1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Enable the following APIs:
   - Gmail API
   - Google Calendar API
   - Google Sheets API

#### Create OAuth Credentials
1. Navigate to **APIs & Services** > **Credentials**
2. Create OAuth 2.0 Client ID
3. Add authorized redirect URIs for n8n
4. Download credentials

### 2. Groq API Setup

1. Sign up at [Groq Console](https://console.groq.com/)
2. Generate an API key
3. Copy the key for n8n configuration

### 3. Google Sheets Setup

1. Create a new Google Sheet named "Contacts"
2. Structure your sheet with columns:
   - Name
   - Email
   - Phone (optional)
   - Notes (optional)
3. Share the sheet with your Google account
4. Copy the Sheet ID from the URL

### 4. n8n Configuration

#### Import Workflow
1. Open n8n
2. Go to **Workflows** > **Import from File**
3. Select `MAILY.json`

#### Configure Credentials

**Gmail OAuth2:**
1. Click on Gmail node
2. Create new credential
3. Enter OAuth Client ID and Secret
4. Authorize access

**Google Calendar OAuth2:**
1. Click on Google Calendar node
2. Create new credential
3. Enter OAuth Client ID and Secret
4. Authorize access

**Google Sheets OAuth2:**
1. Click on Contacts Database node
2. Create new credential
3. Enter OAuth Client ID and Secret
4. Authorize access
5. Update the Document ID to your Contacts sheet

**Groq API:**
1. Click on Groq node
2. Create new credential
3. Enter your Groq API key

### 5. Customize System Prompt

Update the MAILY agent's system message (line 77 in JSON):

```
You are a helpful assistant

You must always look the contacts database before setting up an event or sending a mail. You need person's email address before doing the actions.

Never make up someone's email address. Look into contacts database tool.

Here is current date/time : {{ $now }}
```

Customize this to match your specific needs!

---

## Usage Examples

### Send an Email

```
You: Send an email to John thanking him for the meeting
MAILY: [Looks up John's email from contacts]
MAILY: [Sends email via Gmail]
✓ Email sent to john@example.com
```

### Schedule a Meeting

```
You: Schedule a meeting with Sarah tomorrow at 2pm for 1 hour
MAILY: [Looks up Sarah's email]
MAILY: [Creates calendar event with Sarah as attendee]
✓ Calendar event created
```

### Look Up Contact

```
You: What's David's email address?
MAILY: [Queries contacts database]
MAILY: David's email is david@example.com
```

### Multi-Step Tasks

```
You: Send a meeting invite to the engineering team for Friday at 10am and email them the agenda
MAILY: [Looks up team members]
MAILY: [Creates calendar event]
MAILY: [Sends email with agenda]
✓ Meeting scheduled and email sent
```

---

## Configuration

### Memory Settings

The workflow uses `Simple Memory` (Buffer Window) to maintain conversation context. To adjust:

- Edit the **Simple Memory** node
- Adjust buffer size for longer/shorter context
- Consider switching to `Memory Manager` for persistent storage

### LLM Model

The workflow uses Groq's LLM. Supported models:
- `llama-3.1-70b-versatile` (default)
- `llama-3.1-8b-instant`
- `mixtral-8x7b-32768`

Change model in the **Groq** node configuration.

### Email Options

Gmail node configuration:
- `sendTo` - Recipient email (AI-extracted)
- `subject` - Email subject (AI-generated)
- `message` - Email body (AI-generated)
- `appendAttribution` - Set to `false` (no "Sent via n8n")

### Calendar Options

Google Calendar node configuration:
- `calendar` - Your calendar ID
- `start` - Event start time (AI-parsed)
- `end` - Event end time (AI-parsed)
- `attendees` - List of attendees (AI-extracted)

---

## Workflow File

The workflow is defined in [`MAILY.json`](MAILY.json) and includes:

- 7 nodes (Chat Trigger, AI Agent, LLM, 3 Tools, Memory)
- LangChain integration
- AI tool calling capabilities
- Conversation memory buffer
- Tagged as "AI Agent"

---

## Security & Privacy

### Data Handling
- All conversations are processed through Groq's API
- Contact data is stored in your Google Sheets
- No data is stored outside your controlled services

### Credentials
- OAuth tokens are securely stored in n8n
- API keys should be kept confidential
- Use environment variables for sensitive data

### Best Practices
- Regularly audit sent emails
- Review calendar events created by the agent
- Keep contacts database up to date
- Set up Google Workspace alerts for unusual activity

---

## Troubleshooting

### Common Issues

**"Email address not found"**
- Ensure contact exists in Google Sheets
- Check spelling of name in request
- Verify sheet permissions

**"Failed to send email"**
- Verify Gmail OAuth credentials
- Check if Gmail API is enabled
- Ensure sender email is authorized

**"Calendar event creation failed"**
- Verify Google Calendar OAuth credentials
- Check calendar permissions
- Ensure attendee emails are valid

**"Agent not responding"**
- Check Groq API key validity
- Verify Groq API quota
- Review agent system prompt

### Debugging

Enable execution logging:
1. Open n8n workflow
2. Click **Settings**
3. Enable **Save manual executions**
4. Run test execution
5. Review execution logs

---

## Limitations

- Requires active internet connection
- Depends on third-party API availability (Groq, Google)
- Free tier Groq has rate limits
- No support for email attachments (yet)
- Cannot read existing emails
- Cannot update/delete calendar events

---

## Future Enhancements

### Planned Features
- [ ] Email attachment support
- [ ] Read and summarize incoming emails
- [ ] Update/delete calendar events
- [ ] Multi-calendar support
- [ ] Task management integration (Todoist, Notion)
- [ ] Slack/Teams integration
- [ ] Voice input support
- [ ] Scheduled reminders
- [ ] Email templates
- [ ] Analytics dashboard

### Contribution Ideas
- Add Microsoft 365 support
- Integrate with CRM systems
- Add sentiment analysis for emails
- Build mobile app interface
- Create custom calendar views

---

## Tech Stack

- **n8n** - Workflow automation platform
- **LangChain** - AI agent framework
- **Groq** - Fast LLM inference
- **Gmail API** - Email management
- **Google Calendar API** - Calendar management
- **Google Sheets API** - Contact database
- **OAuth 2.0** - Secure authentication

---

## Contributing

Contributions are welcome! Here's how you can help:

1. **Report Bugs** - Open an issue describing the problem
2. **Suggest Features** - Share ideas for improvements
3. **Submit PRs** - Fork, modify, and submit pull requests
4. **Share Templates** - Create variants for specific use cases
5. **Write Documentation** - Improve setup guides

---

## License

This project is open source and available under the MIT License.

---

## Resources

### n8n Documentation
- [n8n Official Docs](https://docs.n8n.io/)
- [LangChain Nodes](https://docs.n8n.io/integrations/langchain/)
- [AI Agents in n8n](https://docs.n8n.io/langchain/ai-agents/)

### API Documentation
- [Gmail API](https://developers.google.com/gmail/api)
- [Google Calendar API](https://developers.google.com/calendar)
- [Google Sheets API](https://developers.google.com/sheets/api)
- [Groq API](https://console.groq.com/docs)

### Tutorials
- [Building AI Agents with n8n](https://blog.n8n.io/tag/ai/)
- [LangChain Agent Guide](https://python.langchain.com/docs/modules/agents/)

---

## Acknowledgements

- **n8n community** for the amazing automation platform
- **Groq** for fast and affordable LLM inference
- **Google** for comprehensive API ecosystem
- **LangChain** for the agent framework

---

