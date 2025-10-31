🧠 Content Recommendation System (MVP)
Overview

This project is a personalized content recommendation system designed to suggest relevant content to users based on their interests and interactions.
It leverages embeddings, FAISS for similarity search, and SQLite for persistence — forming a lightweight yet powerful prototype for scalable recommendation systems.

🚀 Features

Embedding Generation – Uses transformer-based models to encode content (title, description, etc.) into dense vector embeddings.

Database Storage – Stores both embeddings and content metadata in an SQLite database.

Dynamic Updates – Automatically updates or inserts new embeddings if content changes.

User Personalization – Builds a user embedding profile by averaging embeddings of liked content.

FAISS Indexing – Enables efficient nearest-neighbor search using Euclidean distance.

Ranking Module – Adjusts recommendation results based on recency and popularity.

API Ready – Designed for integration with a FastAPI backend (JSON-based response).

🧩 System Architecture
┌──────────────────────────────┐
│   New or Updated Content     │
│  (title, description, etc.)  │
└──────────────┬───────────────┘
               │
               ▼
      🧠 Embedding Job (your script)
     ──────────────────────────────
     - Uses model.encode(text)
     - Stores Embedding_Vector + Content_ID in SQLite
     - Handles updates (INSERT OR REPLACE)
               │
               ▼
     📦 SQLite: content_embeddings.db
     ──────────────────────────────
     | Content_ID | Embedding_Vector |
     ──────────────────────────────
               │
               ▼
      ⚡ FAISS Index Builder
     ──────────────────────────────
     - Loads embeddings from SQLite
     - Builds FAISS index
     - Saves to faiss_index.index
     - Stores content IDs to content_ids.npy
               │
               ▼
     🔍 ANN (Approx. Nearest Neighbor) Search
     ──────────────────────────────
     - Loads FAISS index in memory
     - Given a query vector → returns top-k similar content
               │
               ▼
      🎯 Ranking + API Layer
     ──────────────────────────────
     - Adjusts recommendations (recency, popularity)
     - Returns ranked results as JSON to frontend

⚙️ Core Components
1. Embeddings Module

Encodes content text fields using a transformer model (e.g., sentence-transformers).
Stores:

content_id

embedding_vector (as BLOB)

title, description, upload_date, etc.

Handles:

New content insertion

Updates using INSERT OR REPLACE

Incremental embedding generation

2. User Likes Module

Fetches a user’s liked content from users_likes table.

Computes the average embedding from liked items.

Supports updates (when a user likes new content, embedding recalculates automatically).

3. FAISS Index Builder

Extracts embeddings from content_embeddings.db.

Converts stored blobs back to float32 NumPy arrays.

Builds an IndexFlatL2 index for Euclidean similarity.

Saves:

faiss_index.index → index file

content_ids.npy → mapping file

Automatically rebuilds the index if new content is added.

4. Recommendation Function

Loads the FAISS index and content mapping.

Computes similarity between the user’s embedding and stored content embeddings.

Returns top-k most similar content.

Maps FAISS indices back to content metadata from the database.

5. Ranking Module

Enhances recommendations using:

Recency → Based on upload_date (newer = higher rank)

Popularity → Based on total number of likes per content

Combined ranking score:

\text{final_score} = \alpha \cdot \text{similarity} + \beta \cdot \text{recency} + \gamma \cdot \text{popularity}
6. API Layer (To Be Implemented)

Will expose a REST API using FastAPI:

GET /recommendations/{username}

Returns JSON of top recommendations

Sample response:

{
  "user": "griffin",
  "recommendations": [
    {"content_id": 12, "title": "AI in Education", "score": 0.93},
    {"content_id": 7, "title": "Understanding Transformers", "score": 0.89}
  ]
}

🧱 Database Schema

content_embeddings

Column	Type	Description
content_id	INT	Unique ID for content
title	TEXT	Content title
description	TEXT	Content description
embedding	BLOB	Vector representation
upload_date	TEXT	Date of upload

users_likes

Column	Type	Description
username	TEXT	User’s unique name
content_id	INT	ID of liked content
🧮 Workflow Summary

Add or update content

Generate embeddings → store in SQLite

Build or refresh FAISS index

Compute user embedding (based on likes)

Search for similar content in FAISS

Rank results by recency & popularity

Return as JSON response

🧠 Future Improvements

Add caching layer (per-user or per-topic)

Implement hybrid ranking (content-based + collaborative)

Integrate Redis for faster embedding lookup

Add authentication for API endpoints

Deploy on Render or Railway with CI/CD

🧑‍💻 Author

Griffin Githambo (Griffo)
Passionate about Data Science, AI, and intelligent systems design.
Built this MVP as part of learning and developing practical AI-powered recommendation solutions.
