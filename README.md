# Customer Story Generator

The **Customer Story Generator** is a content marketing engine that automatically generates customer case studies and social media posts for **LinkedIn** and **X**. Simply upload a recorded user interview and state your customers' privacy permissions, then the content will be sent to your inbox as Markdown files, which can be opened as Google Docs. 

This workflow was developed with **N8N** as the agent orchestrator and the multimodal **Google Gemini 3 & 3.1 Pro** LLM with **Gmail** enabled for tool use. It's also capable of respecting privacy permissions and anonymizing personally identifiable information.

---

## Core Features

- **Multimodal Transcription:** Transcribe audio from `.mp4` files and convert into text using **Gemini 3 Flash**.
- **Dynamic Privacy Logic:** Designate privacy permission preferences to protect confidential and personally identifiable information.
- **Case Study Generator:** Develop case studies that are performance-driven, technically accurate, and verified by customer testimonials using the **Gemini 3.1 Pro** model.
- **Social Media Generator:** Generate social media posts with clear, concise copy for **X** and descriptive, results-oriented **LinkedIn** posts using the **Gemini 3.1 Pro** model.
- **Human-in-the-Loop:** Sends text as **Markdown (.md)** files to your inbox -- which can be accessed, edited, downloaded and exported.

---

## Workflow Architecture

1. **Input Stage:** A user completes an n8n Form, uploading a video file and selecting privacy preferences.
2. **Transcription:** The video is converted to text.
3. **Phase 1 (Case Study):** An AI agent writes the long-form case study.
4. **Formatting & Review:** A JavaScript code node cleans the text, converts it to a file, and emails it to the reviewer.
5. **Phase 2 (Social Media):** The social media agent generates channel-specific copy.
6. **Final Delivery:** Social posts are formatted and sent via Gmail for final review.

---

## Technical Specifications

- **Platform:** n8n
- **LLM:** `gemini-3.1-pro-preview`
- **Input Format:** `.mp4`
- **Output Format:** Markdown (`.md`)
- **Integrations:** Google Gemini (PaLM) API, Gmail OAuth2

---

## ⚙️ Setup Instructions

1. **Credentials:** Ensure you have valid credentials configured in n8n for the **Google Gemini (PaLM) API** and **Gmail OAuth2**.
2. **Form Configuration:** The "Upload recording" node is set as a Webhook. Ensure your production URL is accessible to users.
3. **Reviewer Email:** Update the `sendTo` field in the **Generate case study** and **Generate social posts**  nodes to your preferred email address.
