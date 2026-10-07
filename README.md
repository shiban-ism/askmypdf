# 📄 AskMyPDF

**AskMyPDF** is a Flask-based **PDF Question Answering (RAG) web application** that allows users to upload a PDF and ask questions about its content.
The system answers **only from the uploaded PDF**, using semantic search and a local LLM, with clear source page references.

---

## 🚀 Features

- 📤 Upload a PDF file
- 🧠 Semantic search over PDF content (vector embeddings)
- 💬 Chat-style interface (Instagram / WhatsApp style)
- 📚 Answers strictly grounded in the PDF (no hallucination)
- 📄 Source page references for each answer
- 🔄 Reset session (PDF + chat)
- ⚡ No page reloads (AJAX / Fetch API)
- 🔐 Session-based chat memory
- 🧠 Local LLM inference (no OpenAI key required)

---

## 🏗️ Architecture Overview

This project follows a **Retrieval-Augmented Generation (RAG)** architecture:

```
PDF → Text Extraction → Chunking → Embeddings → Vector DB (Chroma)
                                  ↓
User Question → Semantic Search → Relevant Chunks → LLM → Answer
```

### Tech Stack

| Layer           | Technology                     |
| --------------- | ------------------------------ |
| Backend         | Flask (Python)                 |
| Frontend        | HTML, CSS, JavaScript          |
| Templating      | Jinja2                         |
| Vector DB       | ChromaDB                       |
| Embeddings      | Sentence-Transformers (MiniLM) |
| LLM             | Ollama (Gemma 3)               |
| PDF Parsing     | PyMuPDF                        |
| Session Storage | Flask Sessions                 |

---

## 📂 Project Structure

```
askmypdf/
├── app.py                # Flask application
├── rag.py                # RAG pipeline (index + retrieval + LLM)
├── uploads/              # Uploaded PDF files
├── chroma_store/         # Persistent vector database
├── templates/
│   ├── base.html
│   └── index.html
├── static/
│   ├── style.css
│   └── app.js
├── config/
│   └── .env
├── .gitignore
├── LICENSE
├── requirements.txt
└── README.md
```

---

## 🧠 How It Works (Step by Step)

1. User uploads a PDF
2. PDF text is extracted using **PyMuPDF**
3. Text is split into overlapping chunks
4. Each chunk is converted into a vector embedding
5. Vectors are stored in **ChromaDB**
6. User asks a question
7. Most relevant chunks are retrieved using semantic similarity
8. LLM verifies if the answer exists in the PDF
9. LLM generates a final answer **only from the PDF**
10. Source page numbers are returned

---

## 🛡️ Anti-Hallucination Strategy

- LLM is **not allowed** to use outside knowledge
- A verification step checks whether the answer exists in the retrieved text
- If not found, the system replies:

```
Answer is not in this PDF.
```

---

## 🖥️ User Interface

- Chat bubbles (user → right, assistant → left)
- Smooth scrolling chat
- Fixed bottom input bar
- 🎤 Voice input (speech-to-text)
- Status messages (uploading, thinking, done)
- PDF name badge in navbar
- Reset button clears PDF + chat without reload

---

## ⚙️ Installation & Setup

### 1️⃣ Clone the repository

```bash
git clone https://github.com/khaledelsayed2003/askmypdf.git
cd askmypdf
```

### 2️⃣ Create a virtual environment

```bash
python -m venv venv
source venv/bin/activate    # macOS / Linux
venv\Scripts\activate       # Windows
```

### 3️⃣ Install dependencies

```bash
pip install -r requirements.txt
```

### 4️⃣ Install Ollama

Download from:
👉 [https://ollama.com](https://ollama.com)

Pull the model:

```bash
ollama pull gemma3:4b
```

### 5️⃣ Environment variables

Create `config/.env`:

```env
FLASK_SECRET_KEY=your-secret-key
```

### 6️⃣ Run the app

```bash
python app.py
```

Open in browser:

```
http://127.0.0.1:5000
```

---

## 🧪 Example Usage

1. Upload a PDF (CV, paper, report, book)
2. Ask:

   - _“What programming languages are listed?”_
   - _“What is the project objective?”_
   - _“Who is the author?”_

3. Get:

   - A concise answer
   - Exact source page(s)

---

## 🔄 Reset Behavior

- Clears:

  - Uploaded PDF
  - Vector collection
  - Chat history

- UI resets instantly (no reload)

---
## 📸 Screenshots & Demo Walkthrough
This section demonstrates the full workflow of AskMyPDF, from uploading a document to asking questions, validating answers, handling unsupported queries, and resetting the session.

1. Initial Home Screen (No PDF Uploaded)
When the application starts, no document is loaded. The user is prompted to upload a PDF before asking any questions.

<img width="1277" height="616" alt="01-home-empty" src="https://github.com/user-attachments/assets/4d411d7a-0257-43a6-96bb-c744b7e70e2d" />


2. Uploading a PDF
The user uploads a PDF file, which is:

Saved locally
Indexed into a vector database (ChromaDB)
Prepared for semantic search

<img width="1276" height="618" alt="02-upload-pdf" src="https://github.com/user-attachments/assets/07ebdaaa-bf66-42ae-be42-d350fae3eb18" />


3. Asking a Question
Once a PDF is uploaded, the user can ask questions using a chat-style interface. User messages appear on the right, similar to modern messaging apps.

<img width="1276" height="616" alt="03-chat-question" src="https://github.com/user-attachments/assets/7566ef74-f727-4a2b-9412-494f92da31a9" />


4. Answer Generated from PDF
The assistant retrieves relevant content from the PDF and generates an answer. Each answer includes the exact source page number(s) + it might give also the source page for the second highest retrieved vector.

<img width="1276" height="616" alt="04-chat-answer" src="https://github.com/user-attachments/assets/b19c0b41-b77a-446b-b97c-4d06b7f1d27c" />


5. Multiple Questions in a Single Session
Users can ask multiple questions without reloading the page. The full conversation history is preserved until reset.

<img width="1276" height="617" alt="05-multiple-qa-demo" src="https://github.com/user-attachments/assets/9066904c-b597-47ac-8759-8d124bbb0541" />


6. Hallucination Prevention (Unsupported Questions)
If a question is not answered explicitly in the PDF, the system responds with:

"Answer is not in this PDF."

This ensures the assistant never fabricates information.

<img width="1280" height="617" alt="07-negative-queries-not-in-pdf" src="https://github.com/user-attachments/assets/0bb0a363-1fea-4333-bb7f-0e5686ad7d75" />


7. Resetting the Session
The reset button:

Clears the uploaded PDF
Deletes the vector index
Clears chat history
Returns the app to its initial state

<img width="1277" height="617" alt="08-reset-state" src="https://github.com/user-attachments/assets/8c0600e3-eb7d-411c-89b8-5476adbc4ac9" />

---

## 🧾 Limitations

- Works with **text-based PDFs** (not scanned images)
- Large PDFs may take longer to index
- Requires local LLM (Ollama) to be running

---

## 🛠️ Future Improvements

- 📑 Multi-PDF support
- 🔍 Highlight answer text inside PDF
- 📊 Confidence scoring
- 🌐 Deployment (Docker / Cloud)

---

## 📜 License

This project is licensed under the **MIT License**.
You are free to use, modify, and distribute it.

---

## 👤 Author

**Shiban Etoum**

- 📧 Email: shiban.etoum.2003@gmail.com
- 🧠 Built for learning, research, and real-world RAG systems

---
