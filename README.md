# Customer Story Generator

The **Customer Story Generator** is a content marketing automation that converts recorded customer interviews or testimonials into a publication-ready B2B case studies and social posts for **LinkedIn** and **X**. Upload an `.mp4`, declare your customer's privacy permissions, and the finished assets arrive in your inbox as Markdown files that can be opened as Google Docs. Built on **n8n**, the latest **Gemini Pro** and **Gemini Flash** models, and **Gmail**, it respects privacy permissions and anonymizes personally identifiable information.

---

## Core Features

- **Multimodal Transcription:** transcribes audio from `.mp4` recordings using the latest Gemini Pro model.
- **Dynamic Privacy Logic:** honors your permission choices to protect confidential and personally identifiable information.
- **Case Study Generator:** writes a performance-driven, technically accurate case study grounded in customer testimony using the latest Gemini Flash model.
- **Social Media Generator:** produces concise **X** copy and descriptive, results-oriented **LinkedIn** copy using the latest Gemini Flash model.
- **Human-in-the-Loop:** delivers Markdown (`.md`) files to your inbox for review — accessible, editable, downloadable, and exportable.

---

## Workflow Architecture

```
Upload recording1 → Transcribe a recording ─┬→ Basic LLM Chain  → Convert to File1  → Review case study1
                                            └→ Basic LLM Chain1 → Convert to File*1 → Review social post1
```

1. **Input** — an n8n Form collects the MP4 file and two privacy-permission answers.
2. **Transcription** — the recording is transcribed to text (latest Gemini Pro).
3. **Parallel drafting** — the transcript fans out to two independent branches: one drafts the case study, the other drafts the social posts (latest Gemini Flash). Both read the transcript directly from `Transcribe a recording`, so a failure emailing one asset never blocks the other.
4. **Formatting & delivery** — each asset is converted to a `.md` file and emailed via Gmail for review.

---

## Technical Specifications

| | |
| --- | --- |
| **Platform** | n8n (Cloud or self-hosted) |
| **Models** | `gemini-pro-latest` (transcription), `gemini-flash-latest` (drafting) |
| **Input format** | `.mp4` |
| **Output format** | Markdown (`.md`) |
| **Integrations** | Google Gemini (PaLM) API, Gmail OAuth2 |

---

## Requirements

- **n8n** (Cloud or self-hosted) with the LangChain nodes package (`@n8n/n8n-nodes-langchain`)
- **Google Gemini API key** — for transcription and drafting
- **Gmail OAuth2 credentials** — for automated email delivery

---

## Inputs

- **Upload MP4 file** (required) — the interview/testimonial recording (`.mp4`).
- **Do you have permission to reference the customer's organization?** — Yes / No.
- **Do you have permission to reference the user's first and last name?** — Yes / No.

Privacy-aware: when a permission is "No," the workflow references the organization by industry/scale and individuals by job title; names appear only when permission is "Yes." The prompts forbid fabricating metrics, quotes, or outcomes.

---

## Outputs

- `case_study.md` — a 900–1,200 word case study (Summary → Context → Challenge → Discovery & Evaluation → Customer Proofpoints → Next Steps).
- `social_posts.md` — an X post (≤260 characters) and a LinkedIn post (150–250 words), each with a placeholder link and one CTA.

---

## Setting Up Credentials

After import, n8n will show the Gemini and Gmail nodes as needing a credential. Set each up once, then select it on the relevant nodes.

### Google Gemini

Nodes that use it: **Transcribe a recording**, **Google Gemini Chat Model**, **Google Gemini Chat Model1**.

1. Get an API key from [Google AI Studio](https://aistudio.google.com/app/apikey): sign in and click **Get API key → Create API key**. Copy it.
2. In n8n: **Credentials → Add credential → search "Google Gemini(PaLM) API"**.
3. Paste the key into the **API Key** field and **Save** (e.g. name it "Google Gemini API account").
4. Open each of the three Gemini nodes above and select the credential in the **Credential to connect with** dropdown.

### Gmail

Nodes that use it: **Review case study1**, **Review social post1**.

1. In n8n: **Credentials → Add credential → search "Gmail OAuth2 API"**.
2. Connect your account:
   - **n8n Cloud:** click **Sign in with Google** and authorize the Gmail account to send from.
   - **Self-hosted:** create a Google Cloud OAuth client — in [Google Cloud Console](https://console.cloud.google.com/), pick/create a project → enable the **Gmail API** → **APIs & Services → Credentials → Create OAuth client ID (Web application)** → add n8n's **OAuth Redirect URL** (shown on the credential screen) to the authorized redirect URIs → paste the **Client ID** and **Client Secret** into n8n → **Sign in with Google** and authorize.
3. **Save**, then select this credential on **Review case study1** and **Review social post1**.

---

## Set the Recipient Email

Both Gmail nodes ship with `sendTo = your-email@example.com`. Open **Review case study1** and **Review social post1** and change the **To** field to the address that should receive the assets.

---

## Import and Run

1. In n8n: **Workflows → Import from File →** choose `Customer Story Generator.json`.
2. Configure the Gemini and Gmail credentials (above) and set the recipient email.
3. The workflow imports **inactive**. Open the **Upload recording1** form trigger for the public form URL, upload a recording to test a run, then toggle the workflow **Active**.

---

## Notes

- Models: the latest **Gemini Pro** (`gemini-pro-latest`) for transcription and the latest **Gemini Flash** (`gemini-flash-latest`) for drafting. Change these in the Gemini nodes if you prefer.
- The two social-post character limits are enforced by the prompt, not code — review before publishing.
- Keep your Gemini API key private; don't commit a filled-in copy of this workflow to a public repo.
