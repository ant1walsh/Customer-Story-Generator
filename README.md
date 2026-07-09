# Customer Story Generator

An n8n workflow that turns a recorded customer interview or testimonial (MP4) into two review-ready assets: a long-form B2B case study and a set of social posts for X and LinkedIn. Transcription and drafting run on Google Gemini; both outputs are emailed to you as Markdown attachments.

This is a **templatized** version: all instance-specific values (recipient email, credential IDs, workflow/instance IDs) have been replaced with placeholders so it's safe to share and import into any n8n instance.

## What it does

1. You upload an MP4 through a web form and answer two permission questions (may we name the customer's organization / the individual?).
2. Gemini transcribes the recording.
3. The transcript **fans out to two parallel branches**: one drafts the case study, the other drafts the social posts. Neither depends on the other.
4. Each asset is converted to a `.md` file and emailed to you.

## Workflow structure

```
Upload recording1 → Transcribe a recording ─┬→ Basic LLM Chain  → Convert to File1  → Review case study1
                                            └→ Basic LLM Chain1 → Convert to File*1 → Review social post1
```

Both LLM chains read the transcript directly from `Transcribe a recording`, so a failure emailing one asset never blocks the other.

## Form inputs

- **Upload MP4 file** (required) — the interview/testimonial recording (`.mp4`).
- **Do you have permission to reference the customer's organization?** — Yes / No.
- **Do you have permission to reference the user's first and last name?** — Yes / No.

## Output

- `case_study.md` — a 900–1,200 word case study (Summary → Context → Challenge → Discovery & Evaluation → Customer Proofpoints → Next Steps).
- `social_posts.md` — an X post (≤260 chars) and a LinkedIn post (150–250 words), each with a placeholder link and one CTA.

Privacy-aware: when permission is "No," the organization is referenced by industry/scale and individuals by job title; names are used only when permission is "Yes." The prompts forbid fabricating metrics, quotes, or outcomes.

## Requirements

- An **n8n** instance (Cloud or self-hosted) with the LangChain nodes package (`@n8n/n8n-nodes-langchain`).
- A **Google Gemini API key** (for transcription and drafting).
- A **Gmail account** (to send the two emails).

## Setting up credentials

This template ships with placeholder credentials, so after import n8n will show the Gemini and Gmail nodes as needing a credential. Set them up once, then select them on each node.

### Google Gemini

Nodes that use it: **Transcribe a recording**, **Google Gemini Chat Model**, **Google Gemini Chat Model1**.

1. Get an API key: go to [Google AI Studio](https://aistudio.google.com/app/apikey), sign in, and click **Get API key → Create API key**. Copy it.
2. In n8n: **Credentials → Add credential → search "Google Gemini(PaLM) API"**.
3. Paste the key into the **API Key** field and **Save** (name it something like "Google Gemini API account").
4. Open each of the three Gemini nodes above and, in the **Credential to connect with** dropdown, select the credential you just created.

### Gmail

Nodes that use it: **Review case study1**, **Review social post1**.

1. In n8n: **Credentials → Add credential → search "Gmail OAuth2 API"**.
2. Connect your account:
   - **n8n Cloud:** click **Sign in with Google** and authorize the Gmail account you want to send from. Done.
   - **Self-hosted:** you'll need a Google Cloud OAuth client. In [Google Cloud Console](https://console.cloud.google.com/), create (or pick) a project → enable the **Gmail API** → **APIs & Services → Credentials → Create OAuth client ID (Web application)** → add n8n's **OAuth Redirect URL** (shown in the n8n credential screen) to the client's authorized redirect URIs. Paste the **Client ID** and **Client Secret** into the n8n credential, then click **Sign in with Google** and authorize.
3. **Save**, then open **Review case study1** and **Review social post1** and select this credential in each.

## Set the recipient email

Both Gmail nodes ship with `sendTo = your-email@example.com`. Open **Review case study1** and **Review social post1** and change the **To** field to the address that should receive the assets.

## Import and activate

1. In n8n: **Workflows → Import from File →** choose `Customer Story Generator (template).json`.
2. Set the Gemini and Gmail credentials (above) and the recipient email.
3. The workflow imports **inactive**. Open the **Upload recording1** form trigger to get the form URL, test a run, then toggle the workflow **Active**.

## Notes

- Models: `gemini-pro-latest` for transcription, `gemini-flash-latest` for drafting. Change these in the Gemini nodes if you prefer.
- The two social-post character limits are enforced by the prompt, not code — review before publishing.
- Keep the Gemini API key private; don't commit a filled-in copy of this workflow to a public repo.
