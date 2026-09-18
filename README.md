# Gen AI RAG – Document Question Answering System

## Overview

This project is a simple **Retrieval-Augmented Generation (RAG)** application that lets you ask questions about a PDF or text document.

The program first loads the document, breaks it into smaller text chunks, creates embeddings for those chunks, and stores them in **ChromaDB**. When a question is asked, the system finds the three most relevant chunks and gives them to a local **Llama 3.1** model to generate an answer.

The main goal of the project is to make document-based question answering more reliable by making the LLM answer only from the retrieved document context.

## How It Works

The project follows this basic flow:

**Document → Text Extraction → Chunking → Embeddings → ChromaDB → Similarity Search → Llama 3.1 → Answer**

### 1. Document Processing

The program accepts a file path from the user.

- PDF files are loaded using `PyPDFLoader`.
- Other text files are loaded using `TextLoader`.
- The program checks whether the given file exists before processing it.

### 2. Text Chunking

The extracted document content is divided into smaller chunks using `RecursiveCharacterTextSplitter`.

Current settings:

- **Chunk size:** 1000 characters
- **Chunk overlap:** 200 characters

The overlap helps keep some context between consecutive chunks.

### 3. Embeddings

The project uses the Ollama embedding model:

`nomic-embed-text`

Each text chunk is converted into a numerical vector representation.

### 4. Vector Database

The generated embeddings are stored in **ChromaDB**.

The database is saved in:

`./chroma_db`

This allows the document chunks to be used for similarity-based retrieval.

### 5. Question Retrieval

When the user enters a question, the system performs similarity search and retrieves the **top 3 relevant chunks**.

For each retrieved chunk, the program displays:

- Chunk number
- Distance score
- Page number, when available
- A short preview of the retrieved content

### 6. Answer Generation

The project uses:

`llama3.1`

through `ChatOllama`.

The prompt instructs the model to use **only the supplied document context**. If the requested information cannot be found, it is instructed to respond:

> The requested information is not available in the document.

This helps reduce unsupported answers and speculation.

## Technologies Used

- **Python**
- **LangChain**
- **Ollama**
- **Llama 3.1**
- **nomic-embed-text**
- **ChromaDB**

Main Python packages used in the project include:

- `langchain-community`
- `langchain-text-splitters`
- `langchain-ollama`
- `langchain-chroma`
- `langchain-core`

## Project Structure

A simple setup can look like this:

```text
project/
│
├── Gen AI RAG.py
├── sample.pdf
└── chroma_db/
```

`Gen AI RAG.py` is the main application file, while `chroma_db/` is used for the vector database created by the program.

## Setup

### 1. Install Python

Make sure Python is installed on your system.

### 2. Install the required packages

Install the libraries used by the program:

```bash
pip install langchain-community langchain-text-splitters langchain-ollama langchain-chroma langchain-core
```

### 3. Install and prepare Ollama

The application expects Ollama to provide the local models used for embeddings and question answering.

The code uses:

```text
nomic-embed-text
llama3.1
```

Make sure these models are available in your Ollama installation before running the program.

## Running the Project

Run the Python file:

```bash
python "Gen AI RAG.py"
```

The program will ask:

```text
Enter path to your document (e.g., sample.pdf):
```

Enter the path of the PDF or text document you want to use.

After indexing the document, the system will display:

```text
RAG SYSTEM READY: Type your question or 'exit' to quit.
```

You can then enter questions about the document.

To stop the program, type:

```text
exit
```

You can also use:

```text
quit
```

or:

```text
q
```

## Example

Suppose the input document is:

```text
sample.pdf
```

Run:

```bash
python "Gen AI RAG.py"
```

Then enter:

```text
Enter path to your document (e.g., sample.pdf): sample.pdf
```

After processing, ask something like:

```text
Ask a question: What is the main topic of the document?
```

The system first performs a similarity search, shows the relevant chunks, and then generates a final answer using the retrieved context.

## Important Features

- Supports PDF and text documents.
- Automatically splits documents into manageable chunks.
- Uses embeddings for semantic retrieval.
- Stores embeddings in ChromaDB.
- Retrieves the top 3 relevant chunks for each question.
- Shows similarity-search information before generating the answer.
- Uses a local Llama 3.1 model.
- Restricts the final response to the retrieved document context.
- Provides an interactive question-and-answer loop.

## Code Organization

The program is divided into a few main parts:

### `ingest_and_index(file_path)`

This function handles the document-processing stage.

It:

1. Checks the file.
2. Loads the document.
3. Splits it into chunks.
4. Creates embeddings.
5. Stores the chunks in ChromaDB.

### `format_docs(docs)`

This function combines the retrieved document chunks into a single text context that can be passed to the LLM.

### `run_qa_loop(vector_db)`

This function handles the question-answering stage.

It:

1. Creates the Llama 3.1 model.
2. Creates the prompt.
3. Sets up the retriever.
4. Builds the RAG chain.
5. Accepts questions from the user.
6. Performs similarity search.
7. Generates and prints the final answer.

## Notes

- The application is designed around local models through Ollama.
- The vector database is stored at `./chroma_db`.
- The current retriever uses `k=3`, so three relevant chunks are used for answering.
- The chunking configuration is currently 1000 characters with a 200-character overlap.
- The application is intended for document-based question answering rather than general-purpose chat.

## Conclusion

This project demonstrates the basic working of a **Retrieval-Augmented Generation (RAG) pipeline** using LangChain, Ollama, and ChromaDB. Instead of sending the complete document directly to the language model, the system retrieves relevant information first and then uses that information to generate a grounded response.

It is a useful starting point for building document assistants, PDF question-answering tools, and other knowledge-based GenAI applications.
