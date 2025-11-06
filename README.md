# Mini-RAG Movie Plot System

A lightweight Retrieval-Augmented Generation (RAG) system built to answer questions about movie plots from a subset of the Wikipedia Movie Plots dataset. This project demonstrates the fundamental steps of a RAG pipeline: data processing, embedding, vector storage, retrieval, and structured LLM generation.

## 🌟 Objective

The goal was to build a clean, minimal RAG implementation that takes a user query, finds the most relevant movie plot sections from an internal knowledge base, and uses an LLM to generate a grounded, structured JSON answer.

## ✨ Features & Architecture

The RAG pipeline is implemented in a single **Jupyter Notebook** (`Mini_rag.ipynb`) and consists of the following components:

| Component | Technology Used | Configuration/Details |
| :--- | :--- | :--- |
| **LLM** (Generation) | **OpenAI API** | `gpt-4o` for efficient, structured generation. |
| **Embeddings** | **Sentence Transformers** | `all-MiniLM-L6-v2` (Fast and lightweight, 384 dimensions). |
| **Vector Store** | **FAISS** | In-memory `IndexFlatIP` for cosine similarity search. |
| **Data Source** | **Kaggle** | Subset of the **Wikipedia Movie Plots** dataset (500 movies). |
| **Chunking** | Custom | Plot texts are split into chunks of **300 words** with metadata (Title, plot). |
| **Retrieval Logic** | Custom | Retrieves **Top-K (3)** most relevant chunks. |
| **Output** | **Structured JSON** | The final answer is a JSON object containing `answer`, `contexts`, and `reasoning`. |

## 🚀 Local Setup and How to Run

### 1. Prerequisites

* Python 3.10+
* `pip`
* An **OpenAI API Key**

### 2. Clone the Repository

```bash
git clone https://github.com/AJakif/movie-retrive-mini-rag
cd movie-retrive-mini-rag
```

### 3. Environment Variables

Create a file named ```.env``` in the project root based on the provided .env.example:

.env.example
```
OPENAI_API_KEY="YOUR_API_KEY_HERE"
```
Replace "YOUR_API_KEY_HERE" with your actual OpenAI API Key. The notebook uses python-dotenv to securely load this key.

activate the environment :
```bash
python -m venv venv
source venv/bin/activate  # On Windows use `venv\Scripts\activate`
```

### 4. Install Dependencies
All required libraries are listed and installed in the first cell of the notebook.

``` pip install ipykernel pandas numpy faiss-cpu openai python-dotenv sentence-transformers lxml beautifulsoup4 ```

### 5. Execute the RAG System

The entire pipeline is runnable from the Mini_rag.ipynb notebook:

 1. Open the notebook in Jupyter, VS Code, or a similar environment.

 2. Run all cells sequentially.

 3. The final cells will initialize the RAG system, run a demonstration query, and print the structured JSON output.

## 💡 Example Output
The system produces a structured JSON object to ensure clean, parseable results:

```{
  "answer": "The movie that features a flying saucer landing in Washington D.C. is *The Day the Earth Stood Still*.",
  "contexts": [
    "When a flying saucer lands in Washington, D.C., the Army quickly surrounds it. A humanoid (Michael Rennie) emerges, announcing that he has come in pea..."
  ],
  "reasoning": "The question asked about a flying saucer. I searched the plots, found 'The Day the Earth Stood Still' with a flying saucer landing in Washington, D.C., and used it to form the answer."
}
```