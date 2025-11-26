# Veo3 Prompt-to-Video Automation Agent

A workflow that takes a topic, generates a video idea + caption, writes a Veo3-ready cinematic prompt, sends it to FAL’s Veo3 API to create a video, and logs everything to Google Sheets.  
Built in **n8n** using **OpenAI LLMs**, **FAL Veo3**, and **Google Sheets**.

---

## 🚀 What It Does

- Generates one structured video idea (Idea, Caption, Environment, Status)
- Formats the idea into a Veo3-style cinematic video prompt
- Sends the prompt to **FAL Veo3** to generate a 9:16 video
- Waits, then polls the Veo3 queue for the final result
- Logs:
  - idea  
  - caption + hashtags  
  - environment description  
  - production status  
- Updates the same row later with:
  - final video URL  
  - production status = `done`

All of this runs as a single n8n workflow.

---

## 🧰 Tech Stack

- **n8n** – workflow orchestration  
- **OpenAI (gpt-4.1)** – idea + caption generation  
- **LangChain nodes in n8n** – structured output parsing & agent logic  
- **FAL AI – Veo3** – video generation (`https://queue.fal.run/fal-ai/veo3`)  
- **Google Sheets** – logging ideas + final video links  

---

## 🔧 Workflow Overview

1. *(Optional)* **Schedule Trigger** starts the workflow on a schedule.  
2. **Ideas** node (LangChain agent) calls `gpt-4.1` to generate:
   - `Idea` (short concept)  
   - `Caption` (title + hashtags)  
   - `Environment` (where/how it’s happening)  
   - `Status` (`for production`)  
3. **Structured Output Parser** enforces the JSON schema for the idea.  
4. **Log The Idea** appends a row into Google Sheets with:
   - id, idea, caption, environment_prompt, production  
5. **Prompts** node generates a detailed Veo3 cinematic prompt from the idea + environment.  
6. **Create Video** sends the prompt to FAL Veo3 queue API.  
7. **Wait** pauses briefly.  
8. **Get Video** polls the Veo3 request endpoint to fetch the final video result.  
9. **Log Final** updates the existing row in Google Sheets with:
   - `production = "done"`  
   - `final_output = <video url>`  

---

📝 Setup

1.OpenAI Key – set up OpenAI credentials in n8n (for gpt-4.1).

2.FAL API Key – configure HTTP Header Auth for the Veo3 endpoints.

3.Google Sheets OAuth – connect a Sheets account in n8n.

4.Create a Google Sheet with columns matching:
 id, idea, caption, environment_prompt, production, final_output

5.Import veo3_automation.json into n8n.

6.Update:

Google Sheet URL in the Sheets nodes

Any prompt text you want to customize.

---

▶️ Usage

Open n8n and import the workflow JSON.

Add credentials for OpenAI, FAL, and Google Sheets.

Run the workflow once manually to verify it:

Check that a new row appears in Sheets.

Confirm a video URL is written back after completion.

(Optional) Enable the Schedule Trigger to generate new ideas + videos on a schedule.

---

❓ Why I Built This

To automate turning a single topic into a complete Veo3-ready video clip, without manually writing prompts or tracking outputs.
