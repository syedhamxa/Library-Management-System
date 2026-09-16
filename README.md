# Library Management System

A full-stack library management application for cataloguing books, searching the collection, and handling borrow/return transactions. The project uses a **Flask REST API** backend with **JSON file persistence** and a responsive **single-page web UI** built with HTML, JavaScript, and Tailwind CSS.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Architecture](#architecture)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Running the Application](#running-the-application)
- [Usage Guide](#usage-guide)
- [API Reference](#api-reference)
- [Data Model](#data-model)
- [Error Handling](#error-handling)
- [Development Notes](#development-notes)
- [Future Enhancements](#future-enhancements)
- [License](#license)

---

## Overview

Library Hub provides a centralized interface for managing a book catalogue. Librarians or administrators can:

- View the entire catalogue in a sortable table
- Search books by title or author
- Register new books with metadata (title, author, ISBN, publication year)
- Borrow available books (with borrower name tracking)
- Return borrowed books

All data is stored locally in `library_data.json`, making the project easy to run without a database server. The repository ships with **203 sample books** for demonstration and testing.

---

## Features

| Feature | Description |
|---------|-------------|
| **Catalogue listing** | Displays all books with ID, title, author, ISBN, year, and availability status |
| **Search** | Case-insensitive search by title or author; empty query returns all books |
| **Add books** | Modal form to register new books with auto-generated IDs |
| **Borrow books** | Marks a book as borrowed and records the borrower's name |
| **Return books** | Clears borrow status and borrower information |
| **Status indicators** | Visual distinction between available (green) and borrowed (red) books |
| **Connection feedback** | User-friendly modals for success, validation, and server errors |
| **Responsive UI** | Mobile-friendly layout powered by Tailwind CSS |

---

## Tech Stack

### Backend
- **Python 3**
- **Flask 3.1.2** — REST API server
- **Flask-CORS 6.0.1** — Cross-origin support for the frontend

### Frontend
- **HTML5** — Structure and modals
- **Vanilla JavaScript** — API calls and DOM rendering
- **Tailwind CSS** (CDN) — Styling and responsive design

### Data Storage
- **JSON file** (`library_data.json`) — Simple, file-based persistence

---

## Architecture

```
┌─────────────────┐         HTTP (REST)          ┌──────────────────────┐
│   index.html    │  ─────────────────────────►  │  library_backend.py  │
│  (Web Browser)  │  ◄─────────────────────────  │   Flask @ :5000      │
└─────────────────┘         JSON responses       └──────────┬───────────┘
                                                              │
                                                              ▼
                                                   ┌──────────────────────┐
                                                   │  library_data.json   │
                                                   │   (Book catalogue)   │
                                                   └──────────────────────┘
```

1. The browser loads `index.html` and sends API requests to `http://localhost:5000/api`.
2. Flask handles CRUD and transaction logic, reading/writing `library_data.json`.
3. CORS is enabled globally so the frontend can run from a separate origin (e.g. opened directly as a file or served on another port).

---

## Project Structure

```
Library Management System/
├── index.html              # Frontend UI (single-page application)
├── library_backend.py      # Flask REST API server
├── library_data.json       # Persistent book catalogue (JSON)
├── README.md               # Project documentation
├── venv/                   # Python virtual environment (local)
├── .venv/                  # Alternate virtual environment (local)
└── .vscode/                # Editor settings
```

---

## Prerequisites

- **Python 3.8+** (3.10+ recommended)
- A modern web browser (Chrome, Firefox, Edge, Safari)
- Internet connection (for Tailwind CSS CDN on first load)

---

## Installation

### 1. Clone or download the project

```bash
git clone <repository-url>
cd "Library Management System"
```

### 2. Create and activate a virtual environment

**Windows (PowerShell):**

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

**Windows (Command Prompt):**

```cmd
python -m venv venv
venv\Scripts\activate.bat
```

**macOS / Linux:**

```bash
python3 -m venv venv
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install flask flask-cors
```

Or pin to the versions used in this project:

```bash
pip install Flask==3.1.2 flask-cors==6.0.1
```

---

## Running the Application

The application requires **two components**: the Flask backend and the frontend HTML page.

### Step 1 — Start the backend server

From the project root with your virtual environment activated:

```bash
python library_backend.py
```

You should see:

```
Starting Flask server...
Access the UI at: http://localhost:5000/
 * Running on http://127.0.0.1:5000
```

The API is now available at `http://localhost:5000/api`.

> **Note:** Flask serves the API only. The UI is in `index.html` and must be opened separately (see Step 2).

### Step 2 — Open the frontend

Choose one of the following:

**Option A — Open directly in browser (simplest)**

Double-click `index.html` or open it in your browser via `File → Open`.

**Option B — Serve with a local HTTP server (recommended for development)**

```bash
# Python 3
python -m http.server 8080
```

Then visit `http://localhost:8080/index.html`.

### Verify everything works

1. The catalogue table should populate with books from `library_data.json`.
2. If the backend is not running, a **Connection Error** modal will appear.

---

## Usage Guide

### View all books

Books load automatically when the page opens. Click **Reset** after a search to show the full catalogue again.

### Search the catalogue

1. Enter a title or author (or partial text) in the search box.
2. Click **Search**.
3. Matching books appear in the table; non-matches show a "No books found" message.

### Add a new book

1. Click **+ Register New Book**.
2. Fill in **Title**, **Author**, **ISBN**, and **Publication Year**.
3. Click **Add Book**.
4. The new book appears in the catalogue with status **Available** and an auto-assigned ID.

### Borrow a book

1. Find an **Available** book in the table.
2. Click **Borrow**.
3. Enter the borrower's name in the prompt.
4. Click **Borrow** to confirm.

### Return a book

1. Find a borrowed book (status shows "Borrowed by: …").
2. Click **Return**.
3. Confirm in the dialog.

---

## API Reference

Base URL: `http://localhost:5000/api`

All request/response bodies use JSON unless noted otherwise.

### GET `/books`

Returns the full book catalogue.

**Response `200`:**

```json
{
  "books": [
    {
      "id": 1,
      "title": "The Hitchhiker's Guide to the Galaxy",
      "author": "Douglas Adams",
      "isbn": "978-0345391803",
      "publication_year": 1979,
      "is_borrowed": false,
      "borrowed_by": null
    }
  ]
}
```

---

### GET `/books/search?query=<string>`

Search books by title or author (case-insensitive substring match).

| Parameter | Type   | Required | Description                          |
|-----------|--------|----------|--------------------------------------|
| `query`   | string | No       | Search term; empty returns all books |

**Example:**

```
GET /api/books/search?query=orwell
```

**Response `200`:** Same shape as `GET /books`.

---

### POST `/books/add`

Add a new book to the catalogue.

**Request body:**

```json
{
  "title": "Book Title",
  "author": "Author Name",
  "isbn": "978-0000000000",
  "publication_year": 2024
}
```

**Response `201`:**

```json
{
  "message": "Book added successfully",
  "book": { "...": "..." }
}
```

**Response `400`:** Invalid or missing fields.

---

### POST `/transaction/borrow`

Borrow a book by ID.

**Request body:**

```json
{
  "id": 1,
  "borrower_name": "Jane Smith"
}
```

**Response `200`:**

```json
{
  "message": "Book 'Title' borrowed successfully by Jane Smith."
}
```

| Status | Condition                                      |
|--------|------------------------------------------------|
| `404`  | Book not found                                 |
| `409`  | Book already borrowed                          |
| `400`  | Invalid request (missing fields, bad ID, etc.) |

---

### POST `/transaction/return`

Return a borrowed book by ID.

**Request body:**

```json
{
  "id": 1
}
```

**Response `200`:**

```json
{
  "message": "Book 'Title' returned successfully by Jane Smith."
}
```

| Status | Condition                         |
|--------|-----------------------------------|
| `404`  | Book not found                    |
| `409`  | Book is not currently borrowed    |
| `400`  | Invalid request                   |

---

## Data Model

Each book is stored as a JSON object:

| Field              | Type           | Description                              |
|--------------------|----------------|------------------------------------------|
| `id`               | integer        | Unique identifier (auto-incremented)     |
| `title`            | string         | Book title                               |
| `author`           | string         | Author name                              |
| `isbn`             | string         | ISBN identifier                          |
| `publication_year` | integer        | Year of publication                      |
| `is_borrowed`      | boolean        | Whether the book is currently borrowed   |
| `borrowed_by`      | string \| null | Borrower name when borrowed; else `null` |

**Example `library_data.json` structure:**

```json
{
  "books": [
    {
      "id": 1,
      "title": "1984",
      "author": "George Orwell",
      "isbn": "978-0451524935",
      "publication_year": 1949,
      "is_borrowed": false,
      "borrowed_by": null
    }
  ]
}
```

---

## Error Handling

### Backend

- Missing or corrupt `library_data.json` → initializes with an empty `"books": []` array.
- Empty JSON file → treated as empty catalogue.
- Borrow/return conflicts → `409 Conflict` with a descriptive error message.
- Malformed requests → `400 Bad Request`.

### Frontend

- Failed API connection → modal prompting user to start `library_backend.py`.
- Failed transactions → modal showing the server error message.
- Empty borrower name on borrow → validation modal before submission.

---

## Development Notes

- **Debug mode:** Flask runs with `debug=True` in `library_backend.py`. Disable this in production.
- **Port:** The server listens on port **5000**. Change it in `library_backend.py` and update `API_BASE_URL` in `index.html` if needed.
- **ID generation:** New book IDs are computed as `max(existing ids) + 1`.
- **No authentication:** This is a portfolio/demo project with no user login or role-based access.
- **Data backup:** Copy `library_data.json` before testing destructive operations.

### Quick API test with curl

```bash
# List all books
curl http://localhost:5000/api/books

# Search
curl "http://localhost:5000/api/books/search?query=austen"

# Add a book
curl -X POST http://localhost:5000/api/books/add \
  -H "Content-Type: application/json" \
  -d "{\"title\":\"New Book\",\"author\":\"Test Author\",\"isbn\":\"978-1234567890\",\"publication_year\":2024}"

# Borrow
curl -X POST http://localhost:5000/api/transaction/borrow \
  -H "Content-Type: application/json" \
  -d "{\"id\":1,\"borrower_name\":\"Alice\"}"

# Return
curl -X POST http://localhost:5000/api/transaction/return \
  -H "Content-Type: application/json" \
  -d "{\"id\":1}"
```

---

## Future Enhancements

Possible improvements for a production-ready version:

- [ ] SQLite or PostgreSQL database instead of JSON file storage
- [ ] User authentication and librarian/member roles
- [ ] Due dates, overdue tracking, and fine calculation
- [ ] Book edit and delete endpoints
- [ ] Pagination and sorting for large catalogues
- [ ] Input validation (ISBN format, duplicate ISBN checks)
- [ ] Serve `index.html` from Flask for a single-command startup
- [ ] Unit and integration tests
- [ ] Docker containerization
- [ ] `requirements.txt` for reproducible installs

---

## License

This project is part of a personal portfolio. Use and modify it freely for learning and demonstration purposes. Add a specific license file if you plan to distribute or publish the project formally.
