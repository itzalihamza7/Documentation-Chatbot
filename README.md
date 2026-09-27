# Documentation Chatbot (RAG)

Ask questions about your own Markdown documentation and get answers grounded in it. The project indexes documents into a Chroma vector database with OpenAI embeddings, retrieves the passages most relevant to each question, and has an OpenAI chat model answer using only those passages, citing the source files.

## How it works

1. **Index** (`create_database.py`) loads every `.md` file in `data/books/`, splits it into 300-character chunks with 100 characters of overlap, embeds the chunks with OpenAI and stores them in a local Chroma database (`chroma/`). Chunks are written in batches of 500 so large document sets stay within API limits.
2. **Ask** (`query_data.py`) embeds your question and retrieves the 3 most similar chunks. If the best match has a relevance score of at least 0.7, the chat model answers from that context only; otherwise the script says it found no match. Answers are printed with their source files.
3. **Explore** (`compare_embeddings.py`) prints an embedding vector and the embedding distance between two words, a quick way to see how retrieval measures similarity.

## Tech stack

Python · LangChain · OpenAI embeddings and chat · Chroma · Unstructured

## Getting started

Requires Python 3.9+ and an OpenAI API key.

```bash
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
pip install "unstructured[md]"
```

> `chromadb` depends on `onnxruntime`. If `pip` cannot install it on macOS, run `conda install onnxruntime -c conda-forge` first. On Windows, install the Microsoft C++ Build Tools.

Create a `.env` file:

```
OPENAI_API_KEY=your-key
```

Put the Markdown documents you want to query (for example product or machine manuals) in `data/books/`, then:

```bash
python create_database.py   # build the vector database
python query_data.py        # type your question when prompted
```

## Project structure

```
create_database.py      Load, chunk, embed and store documents
query_data.py           Retrieve relevant chunks and answer questions
compare_embeddings.py   Inspect embeddings and word similarity
requirements.txt
```

## Credits

Built on [pixegami's LangChain RAG tutorial](https://github.com/pixegami/langchain-rag-tutorial). Changes in this version: batched indexing for large document sets, automatic download of the NLTK data the Markdown loader needs, `.env`-based API key checks and an interactive question prompt.
