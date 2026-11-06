# SettleNL (Group 5, NWI-IMK016)

SettleNL helps newcomers to the Nijmegen region understand official Dutch letters and local taxes.
This repository contains our Rasa CALM implementation for sub-assignment 5.

## Implemented conversation (happy path)

| Turn | Speaker | Example | Flow / slot |
|---|---|---|---|
| 1 | User | "Hi" | `greet` |
| 2 | Bot | "Hi! How can I help you today?" | `utter_greet` |
| 3 | User | "I got a letter from the municipality but I don't understand what it's about. It mentions the term 'aanslag gemeentelijke belastingen'" | `ask_term_meaning`, slots `term`, `document`, `sender` |
| 4 | Bot | explains the term and offers to read the letter | `action_lookup_term`, `utter_explain_term` |
| 5 | User | "Ah okay. Is waste tax the same for everyone?" | `ask_waste_tax_info`, slot `tax_type` |
| 6 | Bot | answers and offers more info or to read the letter | `utter_waste_tax_answer` (collects `waste_tax_followup`) |
| 7 | User | "Ah now it's clear thnx" | `waste_tax_followup = confirm_understanding` |
| 8 | Bot | "That's great to hear! Is there anything else you want to ask?" | `offer_more_help` |
| 9 | User | "No that's it for now" | `anything_else = false` |
| 10 | Bot | says goodbye | `utter_goodbye` |

Other branches from the flow diagram that are implemented:
- **Term not recognised**: the bot asks for the exact wording; after more than 2 failed attempts it apologises and refers the user to the sender of the letter (or the Municipality of Nijmegen, 14 024).
- **More waste tax info**: extra explanation, then the user can share the letter, say it is clear, or ask for more (referral).
- **Share letter content** (`share_letter_content`): the user pastes the letter; `action_explain_letter` explains the terms, amounts and dates it recognises.
- **Not understood** (`pattern_cannot_handle`): "Sorry, could you rephrase that?"; after more than 2 fails, apology + referral.
- **Small talk** (`pattern_chitchat`) is answered briefly, then the ongoing flow continues.
- **Goodbye** (`end_conversation`) at any point.

Files: `data/flows.yml`, `data/patterns.yml`, `domain.yml`, `actions/actions.py` (glossary of official terms + custom actions).

Validate the project with `rasa data validate --endpoints endpoints.yml`.

---


# Getting Started
It's recommended to run this repository in GitHub Codespaces (in GitHub, press the green 'Code' button and then Codespaces). If you prefer to work in VS Code or another IDE, you need to manually run these commands:

```
pip install -U pip uv
uv venv
source .venv/bin/activate
uv pip install --extra-index-url https://europe-west3-python.pkg.dev/rasa-releases/rasa-pro-python/simple rasa-pro
uv pip install -r requirements.txt
```

Create a file called `.env` containing your license key:
```
RASA_LICENSE=<the Rasa license key you received when creating your developer account>
```

---

## Installation
**Train the Rasa model**

   ```bash
   rasa train
   ```

---

## Running the Application

You can run the application in two ways:

### Option 1: Using the convenience script

```bash
./run.sh
```

This script will:

- Start the Rasa server with proper CORS settings on port 5005
- Start the Flask server (frontend) on port 5000
- Press `Ctrl+C` to stop both servers

### Option 2: Starting servers manually

1. **Start the Flask server**

   ```bash
   python app.py
   ```

2. **Start the Rasa server (in a separate terminal)**

   ```bash
   rasa run --enable-api --cors "*"
   ```

   > **IMPORTANT**: The `--cors "*"` parameter is essential for the frontend to communicate with the Rasa API. Without it, you'll encounter CORS errors in your browser.

3. **Access the application**

   Open your browser and navigate to `<your-codespace-id-xxxxx>-5000.app.github.dev` if you are running the app in Github codespaces, or `http://localhost:5000` if you are running it locally.

---
