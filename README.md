# PDF Chat Assistant

A **Retrieval-Augmented Generation (RAG)** chatbot that lets you upload your own PDF document and ask questions about its content. The app uses **LangChain** to split and index the document in a **Chroma** vector store, retrieves the most relevant passages for each question, and generates answers with a **Meta Llama** model served through **IBM watsonx.ai** (or optionally the **Hugging Face** Inference API).

> Built as part of the IBM Skills Network guided project **"Build a Chatbot for Your Data"**.

---

## Features

- **PDF upload:** Upload any PDF and the chatbot answers questions based on its content.
- **Retrieval-Augmented Generation:** Relevant document chunks are retrieved with MMR search and passed to the LLM for accurate, grounded answers.
- **Conversation memory:** Keeps a short chat history so follow-up questions have context.
- **Two LLM options:** IBM watsonx.ai (Llama 4 Maverick) or Hugging Face (Llama 3.1 8B Instruct).
- **Web interface:** Chat UI built with HTML, CSS and JavaScript, with light/dark mode and voice recording.
- **Docker support:** Package and run the whole app in a container.

---

## How It Works

1. **Upload:** The user uploads a PDF through the web interface.
2. **Load & split:** `PyPDFLoader` reads the document and `RecursiveCharacterTextSplitter` splits it into chunks (1024 characters, 64 overlap).
3. **Embed & store:** Each chunk is converted to a vector with `sentence-transformers/all-MiniLM-L6-v2` and stored in a Chroma vector database.
4. **Retrieve:** For each question, the most relevant chunks are retrieved using MMR (Maximal Marginal Relevance) search.
5. **Answer:** A LangChain `RetrievalQA` chain sends the question and retrieved context to the LLM, and the answer is shown in the chat.

---

## Tech Stack

| Component       | Technology                                   |
|-----------------|----------------------------------------------|
| Backend         | Python, Flask                                |
| LLM Framework   | LangChain                                    |
| LLM             | Meta Llama via IBM watsonx.ai / Hugging Face |
| Embeddings      | sentence-transformers/all-MiniLM-L6-v2       |
| Vector Store    | Chroma                                       |
| Frontend        | HTML, CSS, JavaScript (jQuery, Bootstrap)    |
| Deployment      | Docker                                       |

---

## Project Structure

```
├── server.py            # Flask server and API routes
├── worker.py            # LLM setup, PDF processing and question answering
├── requirements.txt     # Python dependencies
├── Dockerfile           # Container configuration
├── templates/
│   └── index.html       # Chat interface layout
└── static/
    ├── script.js        # Frontend logic (messages, file upload, dark mode)
    └── style.css        # Styling and loading animations
```

### API Routes

| Route               | Method | Description                                 |
|---------------------|--------|---------------------------------------------|
| `/`                 | GET    | Serves the chat interface                   |
| `/process-document` | POST   | Uploads and processes a PDF document        |
| `/process-message`  | POST   | Sends a question and returns the bot's answer |

---

## Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/iremkursun/PDF-Chat-Assistant.git
   cd PDF-Chat-Assistant
   ```

2. **Create and activate a virtual environment**
   ```bash
   pip3 install virtualenv
   virtualenv my_env
   source my_env/bin/activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

---

## Configuration

### Option 1: IBM watsonx.ai
The default `worker.py` uses `ChatWatsonx` with the `meta-llama/llama-4-maverick` model.
In the IBM Skills Network Cloud IDE, `project_id="skills-network"` works without an API key.
To run it elsewhere, you need your own watsonx.ai API key and project ID.

### Option 2: Hugging Face
Set your Hugging Face token as an environment variable (never hard-code it in the source):
```bash
export HUGGINGFACEHUB_API_TOKEN="your_token_here"
```
You can create a token at [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens).

---

## Usage

### Run locally
```bash
python3 server.py
```
Open **http://127.0.0.1:8000** in your browser, upload a PDF, and start asking questions.

### Run with Docker
```bash
docker build --no-cache -t build_chatbot_for_your_data .
docker run -p 8000:8000 build_chatbot_for_your_data
```
Then open **http://localhost:8000**.

---

## Learning Outcomes

- Understand the basics of LangChain and RAG applications.
- Build a Flask backend with routes for document upload and chat.
- Process PDFs into embeddings and store them in a vector database.
- Integrate open-source LLMs (Meta Llama) through IBM watsonx.ai and Hugging Face.
- Containerize and deploy an AI application with Docker.

---

## License

Lab content is licensed under the [Apache 2.0 License](https://www.apache.org/licenses/LICENSE-2.0).
