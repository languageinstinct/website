Experience Intelligence Portal

INFO 452 – Final Project
Built by Aidan Gantt

Where the Main Frontend Code Lives

All project logic for the frontend lives in:

src/pages/index.astro


This file contains:

the search bar

the JavaScript that makes the POST request to /query

the automatic weather request to /weather

the rendering logic for displaying results

Every other frontend folder (layouts, prose, lib, global CSS, etc.) is part of my Astro site template.
They only manage layout, fonts, and styling.
They do not contain any project logic and were not modified for this assignment.

Overview

This project is a lightweight internal search assistant. A user can type a natural sentence like:

“find the client feedback file”

“show me the support ticket notes”

and the system returns the closest matching internal document.

The backend searches through a collection of JSON “internal records” using TF-IDF and cosine similarity. After finding the best match, the system automatically fetches live weather for the location in that record.

The frontend displays:

the best matching record

the record’s location

the current weather at that location

Components Used

Astro frontend (this repo)

FastAPI backend (Hugging Face Space)

TF-IDF + cosine similarity (RAG)

JSON files acting as the document bank

Weather API using Open-Meteo (geocoding + current weather)

The frontend and backend communicate using simple POST API calls.

Architecture
Frontend

Renders the search bar

Sends the query to /query

Displays the returned record

Automatically calls /weather using the record’s location

Backend

Loads all JSON documents on startup

Builds a TF-IDF matrix

Uses cosine similarity to rank results

Uses small keyword boosts for more natural results

Calls geocoding + weather APIs for live weather

API Endpoints
POST /query     → returns matched internal record  
POST /weather   → returns live weather for that record’s location


Backend URL:
https://algaib-travel-backend.hf.space

How the System Works
1. Data (JSON Files)

Each internal record is a simple JSON file like:

{
  "title": "client feedback summary",
  "category": "feedback",
  "text": "summary of comments from the client...",
  "location": "New York"
}


These load into memory at backend startup.

2. Retrieval Logic (TF-IDF + Cosine Similarity)

The backend uses:

TfidfVectorizer(stop_words="english")
cosine_similarity(query_vector, X)


Small keyword boosts help guide results:

“client” + “feedback” → feedback files

“support” + “ticket” → support files

“training” + “session” → training files

This provides consistent, explainable behavior without needing a large ML model.

3. Weather API (Open-Meteo)

The weather endpoint performs two steps:

Step 1 — Geocoding:
Convert a location name to latitude/longitude

Step 2 — Weather:
Fetch current temperature and wind at those coordinates

Example response:

{
  "location": "New York",
  "latitude": 40.7,
  "longitude": -74,
  "current_weather": {
    "temperature": 54.3,
    "windspeed": 8.1
  }
}


The frontend displays it directly under the matched record.

Frontend → Backend API Calls
Search Request
fetch("https://algaib-travel-backend.hf.space/query", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ query: q })
});

Weather Request
fetch("https://algaib-travel-backend.hf.space/weather", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ location })
});


The frontend simply prints what the backend responds with.

Frontend Setup

Install:

npm install


Run locally:

npm run dev

Backend Setup

Clone the Hugging Face Space:

git clone https://huggingface.co/spaces/algaib/travel-backend


To update:

git add .
git commit -m "update backend"
git push


Hugging Face automatically rebuilds and redeploys after every push.

AI Assistance

AI was used for:

key-Word boosting

creating example JSON documents

fixing Hugging Face build errors

explaining TF-IDF and JSON issues

simplifying the DOM logic

helping format this README

What I Learned

TF-IDF works surprisingly well for small document sets

Cosine similarity is simple and effective

Geocoding and weather APIs were simpler than expected

JSON structure and wording matter for retrieval quality

Keeping the frontend minimal made debugging much easier
