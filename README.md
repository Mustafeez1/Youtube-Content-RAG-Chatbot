YouTube Transcript RAG Question Answering System

A Retrieval-Augmented Generation (RAG) project that extracts a transcript from a YouTube video, splits it into manageable chunks, converts the chunks into embeddings, stores them in a FAISS vector store, retrieves relevant context, and uses an LLM to generate an answer based on the retrieved content.

Project Overview

This project demonstrates the basic RAG workflow using LangChain:

Fetch a YouTube video transcript.

Combine transcript segments into a single text.

Split the text into smaller chunks.

Generate embeddings for the chunks using Ollama's nomic-embed-text model.

Store the embeddings in a FAISS vector store.

Retrieve the most relevant chunks for a user question.

Add the retrieved context to a prompt.

Generate a final answer using OpenAI's gpt-3.5-turbo.

The notebook currently uses a YouTube video ID in the code and tests questions against the transcript content.

RAG Architecture

YouTube Video
     │
     ▼
YouTube Transcript API
     │
     ▼
Transcript Text
     │
     ▼
Text Chunking
     │
     ▼
Ollama Embeddings
(nomic-embed-text)
     │
     ▼
FAISS Vector Store
     │
     ▼
Retriever
     │
     ▼
Relevant Context
     │
     ▼
Prompt Template
     │
     ▼
OpenAI Chat Model
(gpt-3.5-turbo)
     │
     ▼
Final Answer

Technologies Used

Python

LangChain

YouTube Transcript API

Ollama

nomic-embed-text

FAISS

OpenAI ChatGPT

python-dotenv

Jupyter Notebook

The project dependencies are also listed in requirements(2).txt, including LangChain, python-dotenv, langchain-community, langchain_openai, langchain_huggingface, chromadb, faiss-cpu, wikipedia, and youtube-transcript-api.

Project Structure

.
├── rag.ipynb
├── requirements(2).txt
└── README.md

Installation

1. Clone the repository

git clone <your-github-repository-url>
cd <your-project-folder>

2. Create a virtual environment

python -m venv venv

Activate it:

macOS / Linux

source venv/bin/activate

Windows

venv\Scripts\activate

3. Install the dependencies

pip install -r "requirements(2).txt"

The notebook imports RecursiveCharacterTextSplitter, OllamaEmbeddings, and other LangChain components. Depending on the installed LangChain versions, additional LangChain integration packages may be required if an import is not available.

Configuration

Create a .env file in the project directory:

OPENAI_API_KEY=your_openai_api_key

The notebook loads environment variables using python-dotenv.

Security: Never upload your real API key to GitHub. Add .env to .gitignore.

Example .gitignore:

.env
venv/
__pycache__/
.ipynb_checkpoints/

Ollama Setup

The notebook uses:

OllamaEmbeddings(model="nomic-embed-text")

Make sure Ollama is installed and the embedding model is available locally before running the embedding section.

The required model is:

nomic-embed-text

How the RAG Pipeline Works

1. Load the YouTube transcript

The project uses YouTubeTranscriptApi to fetch the transcript from a YouTube video.

yt_api = YouTubeTranscriptApi()

video_id = "SfOaZIGJ_gs"
transcripts = yt_api.fetch(video_id)

2. Convert the transcript into text

The transcript segments are combined into one text string:

transcripts_texts = " ".join(doc.text for doc in transcripts)

3. Split the text into chunks

The transcript is divided into smaller chunks using RecursiveCharacterTextSplitter.

Current settings:

chunk_size = 500
chunk_overlap = 50

This creates overlapping chunks so that important information near chunk boundaries is less likely to be lost.

4. Create embeddings

The project uses Ollama's nomic-embed-text model:

embeddnigs = OllamaEmbeddings(model="nomic-embed-text")

Each text chunk is converted into a numerical vector representation.

5. Store embeddings in FAISS

The chunks and embeddings are stored in a FAISS vector store:

vectore_store = FAISS.from_texts(chunks, embeddnigs)

FAISS enables similarity-based retrieval of relevant chunks.

6. Retrieve relevant information

A retriever is created with:

retriever = vectore_store.as_retriever(
    search_kwargs={"k": 4}
)

The system retrieves the four most relevant chunks for a question.

7. Augment the prompt

The retrieved chunks are combined into context and inserted into a prompt template.

The prompt instructs the assistant to answer from the provided context and say that it does not know when the context is insufficient.

8. Generate the answer

The final prompt is passed to OpenAI's chat model:

llm = ChatOpenAI(
    model="gpt-3.5-turbo",
    temperature=0.2
)

The generated answer is then printed.

Example Questions

The notebook demonstrates questions such as:

Tell me about AI?

and:

How to start a company?

The system first retrieves relevant transcript sections and then generates an answer using those sections as context.

Key Concepts Demonstrated

Retrieval-Augmented Generation (RAG)

Document loading

YouTube transcript extraction

Text preprocessing

Text chunking

Embeddings

Vector databases

FAISS similarity search

Retriever

Prompt augmentation

Context-based question answering

Large Language Models (LLMs)

LangChain

Important Note

The current notebook is a learning implementation of a RAG pipeline. It focuses on understanding the individual stages of RAG rather than providing a production-ready application.

The notebook also contains imports for multiple embedding/vector-store options, while the demonstrated pipeline uses Ollama embeddings with FAISS.

Future Improvements

Possible extensions include:

Add a user interface with Streamlit.

Allow users to enter any YouTube URL instead of using a fixed video ID.

Add support for PDF and text documents.

Persist the FAISS index instead of recreating it every time.

Add source citations to retrieved transcript sections.

Improve prompt formatting and validation.

Add conversation history for follow-up questions.

Add error handling for videos without transcripts.

Add configurable chunk size and retrieval count.

Compare different embedding models.

Add a complete LangChain retrieval chain.

Learning Outcome

This project provides a practical introduction to how a RAG system connects external knowledge with an LLM:

Retrieve relevant information first → provide that information as context → generate an answer grounded in the retrieved context.
