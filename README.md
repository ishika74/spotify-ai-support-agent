# Spotify AI Support Agent

An AI-assisted customer-support prototype for Spotify-related issues, built with Python, Streamlit, and Google Gemini. Developed as part of the Hiver SDE Intern assignment.

**Live Demo:** https://spotify-ai-support-agent-8ayxy8hzodn4vhzkipz5un.streamlit.app/

## Overview

Customer-support teams handle repetitive questions about playback, account access, subscriptions, billing, and other issues. Identifying the type of issue, recognizing cases that may need human attention, and preparing an appropriate response can take time.

This project demonstrates how AI and lightweight NLP techniques can assist with parts of that workflow.

The repository contains two related but separate components:

1. **Streamlit application:** An interactive support-chat interface that classifies issues, identifies potential escalations, and generates responses using Google Gemini.
2. **Modular support pipeline:** A separate set of components for intent classification, rule-based escalation, retrieval of historical conversations, response drafting, and evaluation.

The application is a prototype. It is not connected to Spotify's internal support systems and does not submit tickets to Spotify or transfer conversations to a live support agent.

## Features

### Streamlit application

- Interactive customer-support chat interface.
- Classification of customer-support issues.
- Rule-based detection of potentially sensitive or high-priority issues.
- Google Gemini integration for AI-generated responses.
- Conversation history maintained within the current Streamlit session.
- Feedback controls for responses.
- Generation and download of support-ticket summaries.

### Modular pipeline and evaluation

- Keyword-based intent classification.
- Rule-based escalation detection.
- Reconstruction of customer/support-response pairs from conversation data.
- TF-IDF and cosine-similarity retrieval of historical conversations.
- Deterministic, template-based response drafting.
- Evaluation utilities for classification, escalation, retrieval, and response quality.
- LLM-as-a-judge evaluation of response groundedness, correctness, tone, and actionability.
- A small human-calibrated set for preliminary evaluation of the LLM judge.

**Important:** Historical-conversation retrieval and template-based response drafting belong to the modular pipeline. They should not be assumed to power the deployed Streamlit application's Gemini responses.

## System Architecture

### 1. Streamlit application

The deployed application follows this high-level workflow:

```text
Customer Message
       |
       v
Issue Classification
       |
       v
Escalation Detection
       |
       v
Google Gemini
       |
       v
Generated Support Response
       |
       +----> Response Feedback Controls
       |
       +----> Support-Ticket Summary
                    |
                    v
              TXT Download
```

The application provides support-response assistance and indicates when an issue may require human attention. It does not perform a real human-agent handoff.

### 2. Modular support pipeline

The separate modular pipeline follows this workflow:

```text
Customer Message
       |
       v
Intent Classification
       |
       v
Escalation Detection
       |
       v
Historical Conversation Retrieval
       |
       v
Similar Customer/Support Exchanges
       |
       v
Template-Based Response Draft
```

This pipeline is intended for experimentation and evaluation of the individual components. It is separate from the live Streamlit application.

## Project Structure

```text
spotify-ai-support-agent/
├── .devcontainer/
├── .gitignore
├── README.md
├── requirements.txt
├── app.py
├── classifier.py
├── escalation.py
├── ingest.py
├── intents.py
├── llm_client.py
├── llm_judge.py
├── pipeline.py
├── reply_generator.py
├── retrieval.py
├── run_eval.py
└── sample.csv
```

### File descriptions

| File | Purpose |
|---|---|
| `app.py` | Streamlit application entry point. Handles chat interaction, session history, issue classification, escalation indicators, Gemini responses, feedback controls, and downloadable ticket summaries. |
| `classifier.py` | Keyword-based intent classifier used by the modular pipeline. Also serves as a lightweight baseline for evaluation. |
| `intents.py` | Defines support intents and related metadata used by the support pipeline. |
| `escalation.py` | Applies rules to identify issues that may need human review, such as sensitive account or billing problems. |
| `ingest.py` | Loads and prepares support-conversation data and reconstructs customer/support-response exchanges using conversation identifiers. |
| `retrieval.py` | Retrieves similar historical conversations using TF-IDF vectorization and cosine similarity. |
| `reply_generator.py` | Produces deterministic, template-based response drafts for the modular pipeline. |
| `llm_client.py` | Provides the Google Gemini API client used for model-generated responses. |
| `pipeline.py` | Coordinates classification, escalation detection, retrieval, and response drafting in the modular pipeline. |
| `llm_judge.py` | Implements LLM-based evaluation of generated responses, including quality dimensions and comparison with a small human-calibrated set. |
| `run_eval.py` | Contains evaluation experiments for the support-agent components. |
| `sample.csv` | Sample customer-support conversation data used by the data-ingestion and retrieval components. |
| `requirements.txt` | Lists the Python packages required by the project. |
| `.gitignore` | Specifies files and directories that Git should ignore, including local environment files and Python-generated files. |
| `.devcontainer/` | Development-container configuration directory. |
| `README.md` | Project documentation, setup instructions, architecture, and limitations. |

## Dataset

The project uses a sample of the **Customer Support on Twitter** dataset, which contains customer-support messages and associated conversation identifiers.

The documented dataset fields include:

```text
tweet_id
author_id
inbound
created_at
text
response_tweet_id
in_response_to_tweet_id
```

The `text` field contains message content. The response and conversation identifiers help establish relationships between customer messages and support replies.

The ingestion module reconstructs customer/support exchanges and can filter conversations associated with `SpotifyCares`.

The included data is used for experimentation. It should not be treated as a complete representation of Spotify's support knowledge or current official policies.

## Technology Stack

- **Python** — Application and pipeline logic.
- **Streamlit** — Interactive web interface.
- **Google Gemini API** — AI-generated support responses and LLM-based evaluation where configured.
- **Pandas** — Tabular data processing.
- **Scikit-learn** — TF-IDF vectorization and cosine-similarity utilities.
- **python-dotenv** — Local environment-variable configuration.

## Installation and Setup

### Prerequisites

- Python installed on your system.
- Git.
- A Google Gemini API key for functionality that uses the Gemini API.
- Internet access for installing dependencies and making API requests.

### 1. Clone the repository

```bash
git clone https://github.com/ishika74/spotify-ai-support-agent.git
cd spotify-ai-support-agent
```

### 2. Create a virtual environment

```bash
python -m venv venv
```

Activate it using the appropriate command.

**Windows PowerShell**

```powershell
.\venv\Scripts\Activate.ps1
```

**macOS/Linux**

```bash
source venv/bin/activate
```

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Configure the Gemini API key

Create a `.env` file in the repository root:

```env
GOOGLE_API_KEY=your_gemini_api_key
```

Replace the placeholder with your own API key. Do not commit the `.env` file or publish your credentials.

For Streamlit Community Cloud, configure the key through the application's Secrets settings instead of committing it to the repository.

Example secret:

```toml
GOOGLE_API_KEY = "your_gemini_api_key"
```

### 5. Run the Streamlit application

From the repository root, run:

```bash
python -m streamlit run app.py
```

Streamlit will provide a local URL in the terminal. Open that URL in your browser.

**Note:** The Streamlit application and the modular pipeline are separate entry points. Running `app.py` does not automatically run all the evaluation experiments.

## Evaluation

The repository includes evaluation utilities for experimentation with the support-agent components.

### Intent classification

Evaluates intent-classification behavior and compares it with simple baselines, such as majority-class and keyword-based predictions.

### Escalation detection

Examines whether potentially sensitive or high-priority issues are flagged for possible human review.

### Historical-conversation retrieval

Uses TF-IDF and cosine similarity to identify messages similar to a new customer query and retrieve their associated support responses.

### Response evaluation

The LLM-as-a-judge component evaluates responses across dimensions including:

- Groundedness.
- Correctness.
- Tone.
- Actionability.

A small human-calibrated set is included for preliminary comparison between judge assessments and human assessments.

### Running evaluations

The repository's evaluation entry point is `run_eval.py`. Before running it, confirm its current command-line options and required inputs in the source code.

The modular pipeline's dataset path must also match the actual location of `sample.csv`. Verify that configuration before running pipeline or evaluation commands.

Evaluation results depend on the selected data, test examples, model behavior, and API availability. The presence of evaluation scripts alone does not establish that the system is production-ready or that its performance has been independently validated.

## Example Use Case

A customer enters:

> My Spotify keeps stopping every few seconds. What should I do?

The application can classify the issue, check whether its escalation rules identify a potential need for human review, and use Gemini to generate a support response.

Separately, the modular pipeline can search historical conversations for similar messages and produce a template-based response draft.

The generated answer is an AI-assisted suggestion, not an official Spotify support decision.

## Limitations

- The project uses a sample of customer-support conversations rather than a comprehensive support knowledge base.
- Keyword-based classification may misclassify ambiguous messages or unfamiliar phrasing.
- Rule-based escalation detection may miss important cases or flag cases unnecessarily.
- TF-IDF retrieval is based on lexical similarity and may not identify semantically related messages with different wording.
- Historical support responses may be outdated or unsuitable for a new situation.
- Gemini-generated responses may be incorrect or inconsistent with current Spotify policies.
- The LLM-as-a-judge calibration set is small and does not establish reliable general evaluation performance.
- API availability, quotas, and rate limits can affect model-based functionality.
- Conversation history is session-based and is not backed by persistent conversation storage.
- Feedback controls do not, by themselves, establish persistent feedback storage or automatic model improvement.
- Generated ticket summaries are local artifacts; they are not submitted to Spotify's support infrastructure.
- The prototype does not implement a verified live-agent handoff.
- The modular retrieval pipeline is separate from the deployed Streamlit application's response-generation flow.

## Future Improvements

Potential improvements include:

- Integrating historical-conversation retrieval into the deployed application.
- Improving intent classification beyond keyword matching.
- Using embedding-based semantic retrieval.
- Expanding and validating the evaluation dataset.
- Increasing the size and quality of the human-calibration set.
- Adding persistent storage for conversations, feedback, and ticket summaries.
- Implementing a genuine human-agent handoff if an appropriate support integration becomes available.
- Adding stronger response-grounding and quality checks.
- Introducing structured logging, monitoring, and error handling.
- Adding automated tests for the major application and pipeline components.

These are possible future enhancements, not features claimed to be implemented.

## Security and Responsible Use

- Store API keys in local environment files or deployment secrets.
- Never commit API keys, credentials, or other secrets to source control.
- Keep `.env`, virtual environments, and generated Python cache files out of public commits.
- Avoid entering sensitive personal or payment information into the prototype.
- Treat generated responses and escalation indicators as decision-support outputs that may require human review.

## Author

**Ishika Razdan**

## Disclaimer

This is an independent AI-assisted customer-support prototype developed for educational and demonstration purposes. It is not an official Spotify product and is not affiliated with or endorsed by Spotify.

It does not access Spotify's internal customer-support systems, create actual Spotify support tickets, or guarantee the correctness of generated advice.
