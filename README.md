# n8n Workflow: YouTube Video to Multiple Shorts Automation

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This repository contains a free n8n workflow template designed to automate the process of converting long-form YouTube videos into multiple YouTube Shorts. It integrates with external services for video analysis/rendering and uses a Large Language Model (LLM) for metadata generation, finally uploading and scheduling the shorts to your YouTube channel.

**Save time and streamline your content repurposing!**

## Features

*   **Form Trigger:** Easily start the process by providing a YouTube Video ID, scheduling parameters, and optional styling info via an n8n form.
*   **External API Integration:** Uses HTTP Request nodes to interact with **your chosen** external video processing service API for (Swiftia.io, Opus clip, klap app, getmunch, or spikes studio):
    *   Analyzing long videos to identify potential short clips.
    *   Rendering individual short clips, optionally applying custom caption styling/branding.
*   **Flexible LLM Integration:** Leverages n8n's LangChain nodes to generate optimized titles, descriptions, tags, and YouTube category IDs using **your preferred LLM provider** (OpenAI, Google Gemini, Groq, etc.).
*   **Automatic Scheduling:** Calculates and sets publication dates for each Short based on your specified start time and interval.
*   **Direct YouTube Upload:** Downloads rendered shorts and uploads them directly to your YouTube channel using the official YouTube Data API v3 (resumable uploads).
*   **Adaptable Template:** Built as a foundation – adapt the API calls to your specific video service.

## Disclaimer

*   This is a **template workflow**. You **will need to adapt** the HTTP Request nodes (`generateShorts`, `get_shorts`, `renderShort`, `getRender`) to match the specific API endpoints, request formats, and authentication methods of **your chosen external video processing service**.
*   I have **no affiliation** with any specific video processing or LLM provider mentioned or implied.

## Screenshot (Optional - Add a picture of your workflow)

<!-- ![Workflow Screenshot](link_to_your_screenshot.png) -->

## Prerequisites

Before you start, ensure you have the following:

1.  **n8n Instance:** A running instance (self-hosted or Cloud).
    *   **\[Self-Hosted Users]** Video processing can be memory-intensive. Consider increasing allocated RAM or setting the environment variable `N8N_DEFAULT_BINARY_DATA_MODE=filesystem` (ensure sufficient disk space).
2.  **Video Analysis/Rendering Service:** An account and API Key/Credentials for **a service capable of identifying clips in long videos and rendering them via API**. *You need to choose this service yourself.*
3.  **Google Account & YouTube Channel:** The target channel for uploads.
4.  **Google Cloud Platform (GCP) Project:**
    *   YouTube Data API v3 Enabled.
    *   OAuth 2.0 Credentials (Client ID & Secret).
5.  **LLM Provider Account & API Key:** An API key for your chosen provider (e.g., OpenAI, Google AI/Gemini, Groq, Anthropic).
6.  **n8n Credentials:** Ready to configure credentials within n8n for the services above.
7.  **n8n LangChain Nodes:** (If required by your LLM provider) Ensure the `@n8n/n8n-nodes-langchain` package (or similar) is available in your n8n instance.
8.  **(Optional) Caption Styling Info:** Knowledge of the format (e.g., JSON) required by *your chosen video service* for caption styling.

## Setup Instructions

1.  **Download/Clone:** Get the `video_to_shorts_Automation.json` file from this repository.
2.  **Import:** Import the workflow file into your n8n instance.
3.  **Create n8n Credentials:**
    *   **Video Service Auth:** Configure the necessary authentication credential in n8n for your chosen video service (e.g., Header Auth, API Key, OAuth2).
    *   **YouTube:** Create a "YouTube OAuth2 API" credential using your GCP OAuth details and authenticate it.
    *   **LLM Provider:** Create the appropriate n8n credential for your chosen LLM provider (e.g., "OpenAI API", "Google Gemini API").
4.  **Configure Workflow Nodes:**
    *   **IMPORTANT - Adapt HTTP Requests:** Modify the following HTTP Request nodes to match **your chosen video service's API documentation**:
        *   `generateShorts` (Initiate analysis)
        *   `get_shorts` (Check analysis status)
        *   `renderShort` (Initiate rendering)
        *   `getRender` (Check rendering status)
        *   *You'll need to update URLs, request body structures (JSON), and select/configure the correct Authentication method using the credential you created.*
    *   **Select Credentials:** In the YouTube nodes (`setupMetaData`, `Sendshorttoyoutube`) and the LLM Chat Model node, select the corresponding credentials you created in n8n.
    *   **LLM Node:** The template uses "Google Gemini Chat Model". If using a different provider, **delete** this node and **add** the appropriate one (e.g., "OpenAI Chat Model"). Connect it correctly within the LangChain steps and select your LLM credential.
    *   **Review ALL Nodes:** Double-check all nodes for any remaining placeholder values (like URLs, keys in headers if not using credentials properly) and replace them.
5.  **Activate:** Save and activate the workflow.

## Running the Workflow

1.  Once active, locate the "Webhook URL" provided by the "n8n Form Trigger" node in the workflow editor.
2.  Open this URL in your browser.
3.  Fill out the form:
    *   YouTube Video ID (e.g., `dQw4w9WgXcQ`)
    *   First publication date/time (ISO 8601 format, e.g., `2025-05-10T08:00:00Z`)
    *   Interval between shorts (in hours)
    *   (Optional) Caption styling information (as required by your video service)
4.  Submit the form. n8n will start the process.

## Important Notes

*   **API Keys:** Never commit your actual API keys or sensitive credentials directly into the workflow JSON or your repository. Use n8n's credential manager.
*   **Costs:** Be mindful of potential costs associated with your chosen video processing service, the YouTube Data API (beyond free quotas), and your LLM provider.
*   **Testing:** **Strongly recommended:** Initially set the `privacyStatus` in the `setupMetaData` node to `private` for testing purposes before using `publishAt` for scheduled public/unlisted shorts.
*   **Error Handling:** This template has basic checks but can be enhanced with more robust error handling using n8n's built-in features.

## Customization

Feel free to modify and enhance this workflow:

*   Adjust the prompt in the `generatingMetaData` node for different LLM outputs.
*   Change the maximum number of shorts processed in the `maxShortsnumber` node.
*   Add notification steps (e.g., Slack, Discord) upon completion or failure.
*   Improve error handling logic.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contributing (Optional)

Contributions, issues, and feature requests are welcome! Feel free to check [issues page](link-to-your-issues-page).

## Support (Optional)

If you have questions, please open an issue on the [GitHub repository issues page](link-to-your-issues-page).
