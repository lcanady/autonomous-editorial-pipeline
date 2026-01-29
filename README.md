# n8n Modular News Orchestrator

A modular n8n workflow system designed to scrape Daily Caller articles,
transform them for a specific Facebook demographic (60+), and curate relevant
images for Slack-based approval.

## 🚀 Workflows

This repository contains the following modular components:

- **[Master Orchestrator](file:///Users/kumakun/github/n8n-workflow/workflows/master_orchestrator.n8n)**:
  The entry point. Triggered by the `/signal` Slack Slash Command.
- **[Analysis Module](file:///Users/kumakun/github/n8n-workflow/workflows/analysis_node.n8n)**:
  Scrapes Daily Caller and generates a "Fresh Angle" for Facebook.
- **[Image Curation Module](file:///Users/kumakun/github/n8n-workflow/workflows/image_curation_node.n8n)**:
  Curates images from Unsplash based on analysis keywords.
- **[Final Slack Output](file:///Users/kumakun/github/n8n-workflow/workflows/final_slack_output_node.n8n)**:
  Generates the Slack Block Kit UI with an approval button.
- **[QC Supervisor Module](file:///Users/kumakun/github/n8n-workflow/workflows/qc_supervisor_node.n8n)**:
  Intercepts content to verify factual grounding and check for banned keywords.

## 🛠 Setup Instructions

### 1. n8n Installation

Ensure you have n8n installed and running. These workflows use the **JavaScript
Code** node which requires n8n version 0.190.0 or higher.

### 2. Slack App Configuration

1. Create a Slack App at [api.slack.com/apps](https://api.slack.com/apps).
2. **Slash Commands**: Add a new command `/signal`. Set the Request URL to your
   n8n **Master Orchestrator** production webhook URL.
3. **Bot Scopes**: Add `chat:write`, `incoming-webhook`, and `commands`.
4. **Install**: Install the app to your workspace and copy the Bot User OAuth
   Token.

### 3. Importing Workflows

1. Open n8n.
2. For each `.n8n` file in the `workflows/` directory:
   - Create a new workflow.
   - Click **Import from File...** and select the `.n8n` file.
3. **Authentication**: In n8n, create a "Slack API" credential and paste your
   Bot Token. Link this credential to the Slack nodes in the
   `Master Orchestrator` and `Final Slack Output` workflows.

### 4. Logic Configuration

- **Analysis**: No extra setup needed.
- **Image Curation**: Pulls from Unsplash NAPI (Live).
- **Distribution Webhook**: In the `Final Slack Output` workflow, update the
  **[Generate Block Kit]** node's button URL to point to your distribution
  webhook (e.g., `dlvr.it` or another n8n webhook).

## 📡 Usage

In any Slack channel, type: `/signal [URL to Daily Caller Article] analyze`

The orchestrator will:

1. Scrape the article.
2. Rewrite it for your audience.
3. Find an image.
4. **Run Quality Control**: Verify factual grounding and check for banned
   keywords.
5. Send a Block Kit message to your configured channel for approval (or review
   required alert if QC fails).
