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



## 🚀 Future Features & Advanced RAG Techniques

To evolve this minimal RAG prototype into a high-performance movie knowledge system, the following features and efficiency improvements are planned, with a focus on metadata-driven Hybrid Search and Automated System Tuning.

1. 🔍 Advanced Retrieval: Metadata-Driven Hybrid Search
The current system relies solely on semantic similarity of the plot text. Future implementation will upgrade the retrieval component to a powerful Hybrid Search leveraging movie-specific metadata.

 * Metadata Integration: Augment the dataset by pulling additional structured metadata (e.g., Genre, Director, Actors, Release Year) from external APIs (like TMDb) and storing it alongside the plot vectors in the vector store.

 * Hybrid Querying: The system will combine two search methods:

    1. Dense Retrieval (Vector Search): For semantic queries (e.g., "Find movies about loss of memory").

    2. Sparse Retrieval (Keyword/Metadata Filtering): For exact matches and filtering (e.g., "movies directed by Nolan" or "movies from the 1990s").

 * Re-ranking: After retrieval, a second-stage model (a cross-encoder) will re-rank the combined results to ensure the most contextually relevant and filter-matching chunks are prioritized for the LLM.

 2. 📊 Formal Evaluation and Quality Metrics
 To ensure system reliability, a formal evaluation loop will be implemented using established RAG metrics.

  * Metrics: Automatically calculate:

    1. Precision: How many of the retrieved chunks were truly relevant to answering the query.

    2. Recall: How many of the total relevant chunks were retrieved by the system.

    3. F1-Score: The harmonic mean of Precision and Recall, providing a single measure of overall retrieval accuracy.

  * Purpose: These metrics are essential for quantifying improvements made by different chunking strategies, embedding models, or re-rankers.

  3. ⚙️ Automated System Tuning and Adaptive Configuration
  The efficiency of a RAG system heavily depends on correctly setting hyperparameters (e.g., chunk size, k value, embedding model).

  * Automatic LLM Switching: Implement a rule-based or small LLM Router that decides which LLM to use based on the query:

    1. Use an expensive, powerful model (e.g., GPT-4) for complex, multi-step queries.

    2. Use a faster, cheaper model (e.g., GPT-3.5-turbo or a fast open-source alternative) for simple fact extraction where the context is provided.

  Automated Configuration Change: Integrate a framework like RAGAS or a custom evaluation script to run experiments. If a new configuration (e.g., reducing the overlap in chunking) shows a measurable increase in the F1-score on a test set, the system configuration is automatically updated. This enables continuous improvement.


  ## ⚡ How to Make it More Efficient
  Efficiency means achieving better results (accuracy/relevance) with less time and lower cost (fewer tokens/API calls).

  1. Context Distillation (Post-Retrieval):

    * Before sending the retrieved context chunks to the large LLM, use a smaller, cheaper LLM to summarize or compress the chunks into a single, highly-relevant paragraph. This drastically reduces the token count passed to the final generator, which lowers latency and cost.

  2. Semantic Chunking:

    * Instead of splitting plots by a fixed word count (e.g., 300 words), use a model to identify semantically coherent breakpoints (e.g., splitting by scene change or plot twist). This ensures that a single relevant concept is contained within a single chunk, maximizing retrieval quality.

  3. Use a Dedicated Production Vector DB (e.g., Chroma/Pinecone):

    * Switching from the in-memory FAISS index to a persistent vector database allows for incremental updates (adding new movies without re-indexing everything) and better indexing structures (like HNSW graphs), which improve search speed and recall consistency as the dataset grows.