# Spotify AI Support Agent

An AI-powered customer-support assistant built for the Hiver SDE Intern assignment.

The project processes Spotify customer-support messages, classifies the customer's issue, detects cases that may require human escalation, retrieves similar historical support conversations through a modular retrieval pipeline, and generates support responses using Google Gemini.

## Live Demo

**Streamlit App:**
https://spotify-ai-support-agent-8ayxy8hzodn4vhzkipz5un.streamlit.app/

## Problem Statement

Customer-support teams receive a large number of repetitive questions and complaints. Manually classifying every issue, searching previous conversations, and preparing an appropriate response can be time-consuming.

This project demonstrates an AI-assisted customer-support workflow that automates several stages of the support process:

1. Understand the customer's message.
2. Classify the issue.
3. Detect whether the issue may require human escalation.
4. Retrieve relevant historical support conversations.
5. Generate or draft an appropriate support response.
6. Provide feedback and support-ticket assistance.

The repository also includes evaluation scripts for measuring classification, escalation, retrieval, and response quality.

---

## Features

* Interactive Streamlit chat interface
* Spotify customer-support issue classification
* Google Gemini API integration
* AI-generated support responses
* TF-IDF-based retrieval of similar historical conversations
* Conversation reconstruction from customer-support data
* Rule-based escalation detection
* Human-agent escalation indicators
* Support-ticket summary generation
* Customer feedback controls
* Conversation history within the Streamlit session
* Evaluation scripts and baselines
* LLM-as-a-judge response evaluation
* Human-calibration examples for the LLM judge
* Streamlit Community Cloud deployment

---

## System Architecture

The repository contains two related workflows:

### Streamlit Application

The deployed Streamlit application provides the interactive customer-support experience:

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
Support Response
       |
       +----> Feedback
       |
       +----> Support Ticket
```

### Modular Support Pipeline

The repository also contains a modular pipeline used for retrieval and evaluation:

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
TF-IDF Retrieval
       |
       v
Similar Historical Conversations
       |
       v
Response Draft
```

The modular pipeline and the deployed Streamlit application are kept as separate components so that the retrieval and evaluation components can be tested independently.

---

## Project Structure

```text
spotify-ai-support-agent/
│
├── .devcontainer/
│
├── .gitignore
├── README.md
├── requirements.txt
│
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
│
└── sample.csv
```

---

## Dataset

The project uses a sample of the **Customer Support on Twitter** dataset containing customer-support conversations.

The dataset contains fields such as:

```text
tweet_id
author_id
inbound
created_at
text
response_tweet_id
in_response_to_tweet_id
```

The dataset is stored in:

```text
sample.csv
```

The `text` column contains the customer-support messages, while the response and conversation ID fields are used to identify relationships between customer messages and support responses.

The ingestion module reconstructs customer → support-response exchanges and can filter conversations associated with `SpotifyCares`.

---

## Main Components

### `app.py`

Provides the Streamlit user interface.

It handles:

* customer message input
* conversation history
* issue classification
* escalation detection
* Gemini response generation
* feedback controls
* support-ticket creation
* downloadable ticket summaries

The application is the entry point for the deployed Streamlit application.

Run it with:

```bash
python -m streamlit run app.py
```

---

### `classifier.py`

Implements keyword-based support-issue classification for the modular support pipeline.

The classifier identifies categories such as:

```text
PLAYBACK_TECH_ISSUE
ACCOUNT_LOGIN
BILLING_SUBSCRIPTION
HOW_TO_FEATURE_Q
COMPLAINT_NEGATIVE
PRAISE_THANKS
```

The classifier is intentionally lightweight and deterministic, making it useful as a baseline for evaluation.

---

### `intents.py`

Contains intent definitions and supporting intent metadata used by the support pipeline.

---

### `escalation.py`

Implements rule-based escalation detection.

The module considers signals such as:

* security concerns
* unauthorized charges
* fraud
* account compromise
* legal or sensitive issues
* repeated unresolved issues
* high-priority support scenarios

The output can be used to flag cases for human-agent review.

---

### `ingest.py`

Handles preparation of the customer-support dataset.

It reconstructs customer/support exchanges using the conversation and response identifiers contained in the dataset.

---

### `retrieval.py`

Implements similarity-based retrieval of historical support conversations.

The retrieval system uses:

```text
TF-IDF Vectorization
        +
Cosine Similarity
```

Given a new customer message, it can identify similar historical customer messages and return their associated support responses.

This provides a lightweight retrieval mechanism without requiring an external vector database.

---

### `reply_generator.py`

Provides deterministic response templates for the modular support pipeline.

Responses are generated based on the predicted support intent and escalation status.

This component is separate from the Gemini-based response generation used by the Streamlit application.

---

### `llm_client.py`

Provides the interface for communicating with the Google Gemini API.

The module is used to send prompts to Gemini and return generated responses.

---

### `pipeline.py`

Connects the main components of the modular support pipeline.

The pipeline combines:

```text
Classification
      ↓
Escalation Detection
      ↓
Historical Conversation Retrieval
      ↓
Response Drafting
```

This pipeline is primarily useful for evaluation and experimentation with the support-agent components.

---

### `llm_judge.py`

Implements an LLM-as-a-judge evaluation process for generated support responses.

Responses are evaluated across dimensions such as:

* groundedness
* correctness
* tone
* actionability

The evaluator produces an overall quality score and can also compare model-generated responses against human-calibrated examples.

The current calibration set is intentionally small and should be considered an initial evaluation rather than a statistically significant validation of the judge.

---

### `run_eval.py`

Runs evaluation experiments for the support-agent components.

The evaluation code includes checks for:

* intent classification
* baseline performance
* escalation detection
* support-pipeline behavior
* generated response quality
* LLM-as-a-judge scores

The evaluation scripts are intended for experimentation and development rather than production monitoring.

---

## Technologies Used

* **Python**
* **Streamlit**
* **Google Gemini API**
* **Pandas**
* **Scikit-learn**
* **Python-dotenv**

The retrieval system uses scikit-learn's TF-IDF vectorization and cosine similarity.

---

## Local Installation

### 1. Clone the repository

```bash
git clone https://github.com/ishika74/spotify-ai-support-agent.git
cd spotify-ai-support-agent
```

### 2. Create a virtual environment

```bash
python -m venv venv
```

### 3. Activate the virtual environment

#### Windows PowerShell

```powershell
.\venv\Scripts\Activate.ps1
```

#### macOS / Linux

```bash
source venv/bin/activate
```

### 4. Install dependencies

```bash
pip install -r requirements.txt
```

### 5. Configure the Gemini API key

Create a `.env` file in the project root:

```env
GOOGLE_API_KEY=your_gemini_api_key
```

Never commit the `.env` file or expose the API key publicly.

### 6. Run the Streamlit application

```bash
python -m streamlit run app.py
```

The application will open in your browser.

---

## Streamlit Community Cloud Deployment

The application can be deployed using Streamlit Community Cloud.

### Configuration

1. Connect the GitHub repository to Streamlit Community Cloud.
2. Select:

```text
Repository:
ishika74/spotify-ai-support-agent
```

3. Select the `main` branch.
4. Set the main application file to:

```text
app.py
```

5. Add the Gemini API key under Streamlit Secrets:

```toml
GOOGLE_API_KEY = "your_gemini_api_key"
```

6. Deploy the application.

The application should then be accessible through the generated Streamlit Community Cloud URL.

---

## Evaluation

The repository contains evaluation utilities for testing different parts of the support-agent workflow.

### Classification Evaluation

The classifier can be evaluated against labelled examples and compared with simple baselines such as:

* majority-class prediction
* keyword-based prediction

### Escalation Evaluation

The evaluation scripts test whether potentially sensitive or high-priority customer issues are correctly identified for human review.

### Retrieval

The retrieval module uses TF-IDF and cosine similarity to identify historically similar customer-support conversations.

### LLM-as-a-Judge

Generated responses can be evaluated using an LLM-based evaluator that considers:

```text
Groundedness
Correctness
Tone
Actionability
```

The project also includes a small human-calibrated set for comparing judge behavior with human assessments.

> Evaluation results may vary depending on the dataset, Gemini API availability, and API quota limits.

---

## Example Workflow

A customer might enter:

```text
My Spotify keeps stopping every few seconds. What is wrong?
```

The support workflow can identify the issue as a playback-related problem, check whether escalation is necessary, and generate an appropriate support response.

For the modular retrieval pipeline, the system can additionally search historical support conversations for similar messages and use the retrieved examples when drafting a response.

---

## Limitations

* The project uses a relatively small sample of customer-support conversations.
* The dataset may not cover every possible customer issue.
* Keyword-based classification can fail on ambiguous or unfamiliar wording.
* Rule-based escalation detection cannot replace human judgment.
* TF-IDF retrieval is less semantically powerful than modern embedding-based retrieval.
* Gemini-generated responses may require human review.
* The LLM-as-a-judge evaluation uses a small calibration set.
* Gemini API requests may be affected by quota or rate limits.
* The application is a prototype and is not connected to Spotify's internal customer-support infrastructure.
* Conversation history is session-based and is not backed by persistent storage.

---

## Future Improvements

Potential improvements include:

* Replace keyword classification with a trained or LLM-based classifier.
* Connect the retrieval pipeline directly to the deployed Streamlit application.
* Replace TF-IDF with embedding-based semantic retrieval.
* Add a persistent vector database.
* Add persistent conversation memory.
* Improve escalation detection using a hybrid rules + ML approach.
* Add human-agent handoff functionality.
* Add persistent support-ticket storage.
* Add authentication and user management.
* Add analytics and monitoring.
* Expand the evaluation dataset.
* Increase the human-calibration set for the LLM judge.
* Add automated feedback-based improvement.
* Add production-grade logging and observability.

---

## Security

API keys must be stored securely.

For local development:

```text
.env
```

should be used for environment variables.

For Streamlit Community Cloud:

```text
Streamlit Secrets
```

should be used.

The following should never be committed to the repository:

```text
.env
venv/
__pycache__/
```

API keys and other credentials should never be included in source code, README files, screenshots, or public commits.

---

## Author

**Ishika Razdan**
