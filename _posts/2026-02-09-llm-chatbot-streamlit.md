---
layout: post
title: LLM-based ChatBot in Streamlit
date: 2026-02-09 12:00:00-0400
description: Build a Streamlit chatbot with Azure OpenAI, optional audio, and chat logging
categories: data-science
tags: streamlit, llm, azure-openai, tutorial
giscus_comments: false
related_posts: false
related_publications: true
---

This tutorial shows how to build a production-ready LLM chatbot in Streamlit using `st.chat_message` and `st.chat_input`, with optional audio input/output, session logging, and a simple admin panel. The code below is a cleaned-up, working template you can adapt for your own app.

### 1. What This App Does

- Chat UI with streaming responses
- Azure OpenAI backend
- Optional audio input (Whisper) + audio output (gTTS)
- Session logging with TinyDB
- Simple admin prompt editor and analytics

### 2. Install Dependencies

```bash
pip install streamlit openai python-dotenv tinydb pytz gTTS langdetect librosa whisper pandas
```

If you plan to use Whisper locally, make sure you can run it on your machine (it can be heavy).

### 3. Set Environment Variables

Create a `.env` file with:

```ini
AZURE_OPENAI_API_KEY=...
OPENAI_API_VERSION=...
AZURE_OPENAI_ENDPOINT=...
PASS_TOKEN=...
```

### 4. Full Example

```python
import os
import uuid
from io import BytesIO
from datetime import datetime

import pandas as pd
import pytz
import streamlit as st
from dotenv import load_dotenv
from tinydb import TinyDB
from langdetect import detect
from gtts import gTTS
import librosa
import whisper

from openai import AzureOpenAI

# ---------------------------------
# Config
# ---------------------------------
load_dotenv(override=True)
APP_ID = "sel"

st.set_page_config(layout="wide")

# ---------------------------------
# Models & Clients
# ---------------------------------
@st.cache_resource
def load_whisper_model():
    return whisper.load_model("small.en")

@st.cache_resource
def get_azure_openai_client():
    return AzureOpenAI(
        api_key=os.getenv("AZURE_OPENAI_API_KEY"),
        api_version=os.getenv("OPENAI_API_VERSION"),
        azure_endpoint=os.getenv("AZURE_OPENAI_ENDPOINT"),
    )

whisper_model = load_whisper_model()
client = get_azure_openai_client()

# ---------------------------------
# Helpers
# ---------------------------------
EASTERN_TZ = pytz.timezone("US/Eastern")

def get_eastern_time():
    return datetime.now(EASTERN_TZ).strftime("%Y-%m-%d %H:%M:%S")


def play_audio(text, autoplay=False):
    try:
        text_language = detect(text)
    except Exception:
        text_language = "en"
    tts = gTTS(text, lang=text_language)
    mp3_fp = BytesIO()
    tts.write_to_fp(mp3_fp)
    mp3_fp.seek(0)
    st.audio(mp3_fp, format="audio/mp3", autoplay=autoplay)


def input_audio_to_text():
    audio_data = st.audio_input(
        ":red[Click the microphone button to talk, then stop to submit]"
    )
    if audio_data:
        audio_array, _ = librosa.load(audio_data, sr=16000)
        text_msg = whisper_model.transcribe(audio_array)["text"]
        return text_msg
    return None

# ---------------------------------
# DB
# ---------------------------------
os.makedirs("user_logs", exist_ok=True)
db = TinyDB("user_logs/user_log.json")
db_table = db.table("sel_listening")

# ---------------------------------
# UI: Header
# ---------------------------------
st.markdown(
    '<p style="text-align:center; font-size:42px;">Your App Name</p>',
    unsafe_allow_html=True,
)

st.markdown("***")

st.markdown("### About")
st.markdown(
    "This is an example of creating a Streamlit chatbot using an LLM. "
    "For questions and support, contact: <jianganghao@gmail.com>"
)

# ---------------------------------
# Login
# ---------------------------------
st.markdown("### Login")
col_login = st.columns([2, 1, 2, 2])
userid = col_login[0].text_input("Please enter your username (nickname):")
access_token = col_login[2].text_input(
    "Please enter your access token:", type="password"
)
if access_token != os.getenv("PASS_TOKEN"):
    st.markdown(
        "#### :blue[Sorry, invalid login token. Please try again or contact the administrator.]"
    )
    st.stop()

# ---------------------------------
# Session State
# ---------------------------------
if "app_page" not in st.session_state:
    st.session_state.app_page = APP_ID
if "user_id" not in st.session_state:
    st.session_state.user_id = userid

if st.session_state.app_page != APP_ID or st.session_state.user_id != userid:
    st.session_state.clear()
    st.session_state.app_page = APP_ID

if "system_instruction" not in st.session_state:
    st.session_state.system_instruction = (
        "You are an experienced teacher on ***. "
        "Ask the user questions to check understanding. "
        "Keep responses concise and age-appropriate."
    )

if "session_id" not in st.session_state:
    st.session_state.session_id = str(uuid.uuid4())[:8]

if "messages" not in st.session_state:
    st.session_state.messages = []
    intro = {
        "role": "assistant",
        "content": "Hi, I am your AI learning assistant. Let's start!",
        "datetime": get_eastern_time(),
        "user_id": userid,
        "session_id": st.session_state.session_id,
        "app_id": APP_ID,
        "event_name": "chat",
    }
    st.session_state.messages.append(intro)
    db_table.insert(intro)

# ---------------------------------
# Admin Panel
# ---------------------------------
if userid == "admin":
    with st.expander("Admin: edit system instruction"):
        st.session_state.system_instruction = st.text_area(
            "", value=st.session_state.system_instruction, height=300
        )
        st.download_button(
            label="Download instruction",
            data=st.session_state.system_instruction.encode("utf-8"),
            file_name="prompt.txt",
            mime="text/plain",
        )

st.markdown("***")

# ---------------------------------
# Chat UI
# ---------------------------------
show_audio = st.checkbox("Enable Audio Chat")
container = st.container(height=550)

for message in st.session_state.messages:
    if message["event_name"] == "chat":
        with container.chat_message(message["role"]):
            st.markdown(message["content"])

prompt = input_audio_to_text() if show_audio else st.chat_input("Enter to chat")

if prompt:
    user_msg = {
        "role": "user",
        "content": prompt,
        "datetime": get_eastern_time(),
        "user_id": userid,
        "session_id": st.session_state.session_id,
        "app_id": APP_ID,
        "event_name": "chat",
    }
    st.session_state.messages.append(user_msg)
    db_table.insert(user_msg)

    with container.chat_message("user"):
        st.markdown(prompt)

    with container.chat_message("assistant"):
        try:
            stream = client.chat.completions.create(
                model="gpt-4o",
                messages=[
                    {"role": "system", "content": st.session_state.system_instruction}
                ]
                + [
                    {"role": m["role"], "content": m["content"]}
                    for m in st.session_state.messages
                ],
                stream=True,
            )
            response = st.write_stream(stream)
        except Exception:
            response = "Please avoid inappropriate content in classroom settings."
            st.write(response)

    assistant_msg = {
        "role": "assistant",
        "content": response,
        "datetime": get_eastern_time(),
        "user_id": userid,
        "session_id": st.session_state.session_id,
        "app_id": APP_ID,
        "event_name": "chat",
    }
    st.session_state.messages.append(assistant_msg)
    db_table.insert(assistant_msg)

    if show_audio:
        with container:
            play_audio(response, autoplay=True)

# ---------------------------------
# Analytics (Admin)
# ---------------------------------
if userid == "admin":
    st.markdown("### Analytics")
    with st.expander("Click to see details"):
        df = pd.DataFrame(st.session_state.messages)
        df["datetime"] = pd.to_datetime(df["datetime"])
        df["session_time"] = df.datetime - df.datetime.iloc[0]
        df["session_time"] = df.session_time.apply(lambda x: x.total_seconds())
        st.markdown(
            f"#### Total turns: {df.query('role==\"user\"').content.count() * 2 + 1}"
        )
        st.markdown(
            f"#### Total words by user: {df.query('role==\"user\"').content.apply(lambda x: len(x.split())).sum()}"
        )
        st.markdown(
            f"#### Total words by agent: {df.query('role==\"assistant\"').content.apply(lambda x: len(x.split())).sum()}"
        )
        st.dataframe(df)
```

### 5. Notes and Improvements

- **Security**: Use environment variables and rotate keys regularly.
- **Scaling**: Add rate limiting and store logs in a database (Postgres, SQLite, etc.).
- **UX**: Add a “Clear chat” button and default welcome messages per session.
- **Audio**: Whisper + gTTS are optional; disable for lighter deployments.

---

That’s it. You now have a solid Streamlit chatbot template with LLM streaming, logging, and optional audio support.
