# useless_project
just being creative
# EchoLens-Most Used Word
## Basic Details
**Team Name**:UselessDuo

**Team Member**
1. Member:Hanan K Hilal-Mar Athanasius College Of Engineering, Kothamangalm
   
**Project Description**
A web application which access the microphone and the user can speak. The app analyses the speech and display the mostly used word. Displays mostly used word, count and mock the person with that word.

**The Problem (that doesn't exist)**
Difficulty to undesrtand the favourate word of a person,

**The Solution (that nobody asked for)**
Decoding the speech and displaying the word, its count and also mocking them to prevent the overuse of that word.

## Technical Details
**Technologies/Components Used**

Languages used: PYTHON, HTML, CSS, JS
Frameworks used: Flask
Libraries used: Flask-CORS, json, datatime, collections.Counter
Tools used: VS Code, Web Speepch API, Web Browser, Terminal

**Implementation**
from flask import Flask, request, jsonify
from flask_cors import CORS
import json, os, re
from datetime import datetime
from collections import Counter

app = Flask(__name__)
CORS(app)
DATA_FILE = "sessions.json"

# ---------------------------
# Helpers
# ---------------------------
def load_sessions():
    if not os.path.exists(DATA_FILE):
        return []
    with open(DATA_FILE, "r", encoding="utf-8") as f:
        try:
            return json.load(f)
        except json.JSONDecodeError:
            return []

def save_sessions(sessions):
    with open(DATA_FILE, "w", encoding="utf-8") as f:
        json.dump(sessions, f, ensure_ascii=False, indent=2)

def get_most_used_word():
    """Scans all saved sessions for the most used word."""
    sessions = load_sessions()
    all_words = []

    for s in sessions:
        if "message" in s and isinstance(s["message"], str):
            words = re.findall(r"\b\w+\b", s["message"].lower())
            all_words.extend(words)

    if not all_words:
        return None

    counter = Counter(all_words)
    return counter.most_common(1)[0][0]  # (word, count)

def generate_mock_comment(word):
    """Generate a playful mocking comment for the given word."""
    if not word:
        return "No words to mock yet 😏"
    return f"Bruh, do you *ever* stop saying '{word}'? It's like your catchphrase now 😂"

# ---------------------------
# Routes
# ---------------------------
@app.route("/", methods=["GET"])
def home():
    return jsonify({"message": "Flask backend is working!"})
@app.route("/save", methods=["POST"])
def save_session():
    payload = request.get_json()
    if not payload:
        return jsonify({"error": "invalid json"}), 400

    # Save with timestamp
    sessions = load_sessions()
    payload["timestamp"] = datetime.utcnow().isoformat() + "Z"
    sessions.append(payload)
    save_sessions(sessions)

    # Get most used word and mocking comment
    most_word = get_most_used_word()
    mock_comment = generate_mock_comment(most_word)

    return jsonify({
        "status": "ok",
        "saved": payload,
        "most_used_word": most_word,
        "mock_comment": mock_comment
    }), 201

@app.route("/history", methods=["GET"])
def history():
    return jsonify(load_sessions())

# ...existing code...

@app.route("/health", methods=["GET"])
def health():
    print("Health check endpoint called")
    """Health check route."""
    return jsonify({"status": "ok"})

@app.route("/stats", methods=["GET"])
def stats():
    """Return patience stats + most used word mocking."""
    sessions = load_sessions()
    patience_stats = []
    for s in sessions:
        patience_stats.append({
            "day": s.get("timestamp", "Unknown"),
            "patience": s.get("patienceLevel", 0)
        })
    most_word = get_most_used_word()
    return jsonify({
        "stats": patience_stats,
        "most_used_word": most_word,
        "mock_comment": generate_mock_comment(most_word)
    })

if __name__ == "__main__":
    app.run(debug=True, port=5000)

## Installation

## Run

**Project Documentation**

## Screeshots
NA

## Diagrams
NA

## Schematic and Circuit
NA

## Build photos
NA 

**Project Demo**
NA

## Video
NA

## Additional Demos
NA

**Team Contributions**
Solo
