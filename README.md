Experience Intelligence Portal

INFO 452 – Final Project
Built by Aidan Gantt

Overview

This project is a lightweight internal search assistant. A user can type a natural sentence like “find the client feedback file” or “show me the support ticket notes”, and the system returns the closest matching internal record.

The backend reads a collection of JSON “internal documents,” searches through them with TF-IDF + cosine similarity, and returns the best match.
After a record is found, the system automatically pulls live weather data for the location listed in that record.

The final output shows:

the best-matching record

the record’s location

live weather at that location

Components Used

Astro frontend (this repo)

FastAPI backend (Hugging Face Space)

TF-IDF + cosine similarity (manual RAG-style retrieval)

JSON files acting as the document bank

Weather API using Open-Meteo (geocoding + forecast)

The frontend and backend communicate using a simple POST API.

Architecture
Frontend

Shows the search bar

Sends the query to the backend’s /query endpoint

Displays the matched document

Automatically calls /weather using the returned location

Backend

Loads all JSON documents on startup

Creates a TF-IDF matrix once

Uses cosine similarity for search

Light keyword-boosting rules for better accuracy

Weather endpoint uses geocoding + live forecast

API Endpoints
POST /query     → returns matched internal record
POST /weather   → returns live weather for that record’s location


Backend URL:
https://algaib-travel-backend.hf.space

How the System Works
1. Data (JSON Files)

Each internal record looks like:

{
  "title": "client feedback summary",
  "category": "feedback",
  "text": "summary of comments from the client...",
  "location": "New York"
}


These are loaded once at startup and stored in memory.

2. RAG-Style Retrieval (TF-IDF + Cosine Similarity)

The backend uses:

TfidfVectorizer(stop_words="english")

vectorizer.fit_transform(docs)

cosine_similarity(query_vector, X)

There are small keyword boosts:

“client + feedback” → feedback docs

“support + ticket” → support docs

“training + session” → training docs

This improves results without needing a full ML model.

3. Weather API (Open-Meteo)

The weather endpoint does two steps:

Convert the record’s location name → latitude/longitude
using Open-Meteo’s free geocoding API

Use those coordinates to request current weather data

Example returned JSON:

{
  "location": "New York",
  "latitude": 40.7,
  "longitude": -74,
  "current_weather": {
    "temperature": 13.4,
    "windspeed": 8.1
  }
}


The frontend displays this under the matched record.

API Call (Frontend → Backend)
Query request
fetch("https://algaib-travel-backend.hf.space/query", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ query: q })
});

Weather request
fetch("https://algaib-travel-backend.hf.space/weather", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ location })
});


The frontend only renders what the API returns.

Frontend Setup

Install:

npm install


Run locally:

npm run dev

Backend Setup

Clone your Hugging Face Space:

git clone https://huggingface.co/spaces/algaib/travel-backend


To update:

git add .
git commit -m "update backend"
git push


Hugging Face automatically rebuilds and redeploys.

AI Assistance:

fixing broken Hugging Face builds

explaining errors in TF-IDF and JSON handling

Helping with DOM

What I Learned

TF-IDF works well for small document sets

Cosine similarity is simple and effective

Using real APIs (geocoding + weather) wasn’t as complicated as expected

JSON structure matters for consistent results

Keeping the frontend simple made the project easier to maintain

Reading build logs is a real skill when using cloud platforms
