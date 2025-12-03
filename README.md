Experience Intelligence Portal

INFO 452 – Final Project
Built by Aidan Gantt

Overview

This project is a lightweight internal search assistant designed for teams who need to quickly find specific internal records using natural language. Instead of manually looking through files, a user can type a sentence (like “find the client feedback file”) and the system returns the most relevant internal document.

The project uses:

a custom Astro frontend (this repo, deployed on Vercel)

a FastAPI backend (separate Hugging Face Space)

a simple RAG-style retrieval pipeline using TF-IDF + cosine similarity

a collection of internal JSON records acting as the knowledge base

The two systems communicate through a POST API call.

Architecture
Frontend (Astro)            Backend (FastAPI, HF Space)

User types query  ->   POST /query
Render results    <-  JSON record returned

Frontend

Built in Astro

Contains the search bar UI and JavaScript that calls the backend

Backend

Hosted as its own Hugging Face Space (separate Git repo)

Loads JSON files, vectorizes them, runs similarity search, and returns the best match

Endpoint:

https://huggingface.co/spaces/algaib/travel-backend

How the System Works
1. Data

Internal records are stored as .json files in the backend:

{
  "title": "client feedback summary",
  "category": "feedback",
  "text": "summary of comments from the client regarding...",
  "location": "region a"
}

2. RAG Retrieval Logic

The backend uses:

TfidfVectorizer (scikit-learn)

cosine_similarity

a small keyword-boosting rule

This creates a simple, explainable retrieval system based on the content of the documents.

3. API Call

The frontend sends a POST request:

fetch("https://algaib-travel-backend.hf.space/query", {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({ query: q }),


The backend responds with:

{
  "type": "blog",
  "title": "...",
  "text": "...",
  "location": "..."
}


The frontend renders that on the page.

Frontend Setup
Install Dependencies
npm install

Run Dev Server
npm run dev


Make sure the frontend is pointing to the correct backend URL.

Backend Setup (Hugging Face Space)

The backend is its own repo inside Hugging Face Spaces.
To modify it:

git clone https://huggingface.co/spaces/algaib/travel-backend


Then:

git add .
git commit -m "update backend"
git push


HF auto-redeploys.

AI Assistance 

AI was used during development to:

debug failing Hugging Face builds

guide troubleshooting when deployments broke


What I Learned

retrieval systems don’t need heavy ML models

TF-IDF + cosine similarity is more effective than I expected

small changes to JSON data structure massively impact results

frontend/backend communication needs clean, consistent JSON

simplifying a system usually improves it
