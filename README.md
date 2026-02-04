# AI-Powered Telegram Bot for Personal Task Management

This repository contains the implementation of an AI-powered Telegram bot designed for managing personal tasks and reminders. Users can add tasks with dates and priorities (High, Medium, Low) via natural language messages in Telegram. Tasks are stored in a Google Sheet and can be queried by specific dates or months. The bot uses natural language processing to detect intents, extract details, and respond intelligently.

The workflow is built using [n8n](https://n8n.io/) as the automation tool, [Google Gemini](https://gemini.google.com/) as the LLM for intent detection and data extraction, and Google Sheets as the storage backend. This setup is free, accessible, and requires minimal coding.

## Features

- **Add Tasks:** Send a message like "Add a reminder for my friend's birthday on April 4" to add a task with automatic date extraction (defaults to 2026 if year not specified) and priority assignment (e.g., High for birthdays/deadlines).
- **Query Tasks:** Ask questions like "What tasks do I have in May?" or "Tasks for 2026-05-05?" to retrieve a list of tasks filtered by date or month.
- **Priority Levels:** Automatically assigns High (urgent like birthdays), Medium (standard tasks), or Low (optional tasks) based on context.
- **Storage:** Tasks are appended to a Google Sheet with columns: `task_date` (YYYY-MM-DD), `description`, `priority`.
- **Responses:** User-friendly replies with emojis for readability, sent back via Telegram.

## Technologies Used

- **n8n:** Workflow automation tool for orchestrating the bot logic (triggers, AI processing, branching, storage, and responses).
- **Google Gemini:** Free LLM integrated via n8n for natural language understanding, intent detection, and structured JSON output.
- **Google Sheets:** Simple database for storing and retrieving tasks.
- **Telegram API:** Handles incoming messages and outgoing responses.
- **JavaScript (in n8n):** Custom code nodes for JSON parsing, filtering, and formatting.

## Repository Contents

- `ai_agent_workflow.json`: The exported n8n workflow JSON file. Import this into n8n to recreate the workflow.
- `task_2_explanation.pdf`: Detailed PDF explanation of the workflow, including setup, prompt, node breakdown, and testing screenshots.

## Setup Instructions

1. **Create a Telegram Bot:**
   - Use [BotFather](https://t.me/botfather) to create a new bot and obtain its API token.

2. **Set Up n8n:**
   - Sign up for a free [n8n cloud account](https://n8n.io/) or self-host it.
   - Import the `ai_agent_workflow.json` file into n8n.
   - Configure credentials:
     - **Telegram API:** Add your bot token.
     - **Google Gemini (PaLM API):** Add your Google API key (free tier available).
     - **Google Sheets OAuth2:** Connect your Google account and grant access to Sheets.

3. **Google Sheets Setup:**
   - Create a new Google Sheet (e.g., named "daily_schedule").
   - Add headers: `task_date`, `description`, `priority`.
   - Update the workflow nodes (e.g., "Append row in sheet" and "Get row(s) in sheet") with your Sheet's ID and sheet name.

4. **Activate the Workflow:**
   - In n8n, activate the workflow. It will create a webhook for Telegram.
   - Set the webhook in Telegram using the bot token (n8n handles this automatically via the Telegram Trigger node).

5. **Test the Bot:**
   - Start chatting with your bot in Telegram.
   - Example: Send "Add a task: Meeting on May 5, 2026" → Bot adds it to the Sheet and confirms.
   - Query: "Tasks in May?" → Bot lists matching tasks.

## Workflow Breakdown

The workflow starts with a Telegram trigger and processes messages as follows:

1. **Telegram Trigger:** Captures incoming messages.
2. **AI Agent (Google Gemini):** Analyzes the message using a custom prompt to detect intent (`add_task` or `query_task`), extract date/description/priority, and output JSON.
3. **JavaScript Code (Parsing):** Cleans and parses the AI's JSON output.
4. **If Node:** Branches based on intent.
   - **Add Task Branch:** Appends to Google Sheets and sends a confirmation message.
   - **Query Task Branch:** Reads from Sheets, filters via JavaScript, formats the response, and sends it back.
   
For a detailed node-by-node explanation, refer to `task_2_explanation.pdf`.

## Testing

The workflow was tested with multiple scenarios:

- **Test 1: Meeting Reminder in May 2026**
  - Add: "Mayın 5-i iş yoldaşlarımla vacib bir görüşüm var..." (Added with High priority).
  - Query: "May ayında hansı tapşırıqlarım var?" (Lists tasks for the month).

- **Test 2: Shopping in June 2026**
  - Add: "12 Sentyabr vacib işim var. Zehmet olmasa əlavə et." (Added with High priority).
  - Query: "Sentyabr ayı ərzində xüsusi etməli olduğum bir tapşırıq var?" (Lists tasks).

All tests confirmed correct storage, retrieval, and responses. Screenshots are in the PDF.

**Notes:**
- The bot assumes 2026 for unspecified years to future-proof.
- Handles Azerbaijani language in tests but works with English too (prompt is language-agnostic).
- For birthdays, it assigns High priority as per the example scenario.

## Limitations

- Full Sheet reads are used for queries (no built-in partial filters in n8n for month-only), which is fine for small datasets.
- No automatic reminders (e.g., 3 days before); this is a basic add/query bot.
- Credentials (API keys) are not included; set them up in n8n.

## License

This project is licensed under the MIT License. Feel free to fork and modify!
