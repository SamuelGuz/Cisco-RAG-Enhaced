# 🤖 LLM Prompts to Build This RAG System

This guide contains prompts you can use with an LLM (like ChatGPT, Claude, or a local model) to help you build each component of the RAG system step by step.

---

## 📦 Block 1: Install Dependencies

**Prompt:**
```
I'm building a RAG system in Python using a Jupyter notebook. I need to install these libraries:
- requests (for HTTP calls)
- sentence-transformers (for embeddings)
- chromadb (vector database)
- pypdf (to read PDFs)

Write the pip install command for a Jupyter notebook cell. Use the quiet flag to reduce output.
```

---

## 📚 Block 2: Import Libraries

**Prompt:**
```
Write Python import statements for these libraries:
- requests (for HTTP calls)
- SentenceTransformer from sentence_transformers
- chromadb
- PdfReader from pypdf
- os (standard library)

Just the imports, no other code.
```

---

## ⚙️ Block 3: LM Studio Configuration

**Prompt:**
```
I'm using LM Studio as a local LLM server. It runs at http://127.0.0.1:1234 and has an OpenAI-compatible API.

Write a Python function called `chat_with_llm` that:
1. Takes a list of messages (each with "role" and "content"), max_tokens, and temperature as parameters
2. Sends a POST request to the /v1/chat/completions endpoint
3. Returns the response content as a string

Also initialize a SentenceTransformer model using 'all-MiniLM-L6-v2' for embeddings.
Print confirmation messages when configured.
```

---

## 🧪 Block 4: Test LM Studio Connection

**Prompt:**
```
Write Python code to test if LM Studio is running and responding.

Use the chat_with_llm function to send a simple test message like "Say 'OK' if you can hear me."

Handle these errors gracefully:
- ConnectionError: show instructions on how to start LM Studio
- Any other exception: show the error message

Print success or failure messages with clear instructions.
```

---

## 🎛️ Block 5: Configuration Parameters

**Prompt:**
```
Create a configuration section for a RAG system with these adjustable parameters:

Chunking:
- CHUNK_SIZE: maximum characters per chunk (default 500)
- CHUNK_OVERLAP: overlap between chunks (default 50)
- MIN_CHUNK_LENGTH: minimum chunk size to keep (default 50)

Retrieval:
- NUM_RESULTS: documents to retrieve per query (default 3)

Paths:
- DOCS_PATH: folder containing PDFs
- HR_PDF: filename for HR policies
- TECH_PDF: filename for tech policies

Metadata options (as a dictionary with True/False):
- source, page, chunk_id, char_count, category

Print the configuration values when loaded.
```

---

## 📄 Block 6: PDF Loading and Chunking

**Prompt:**
```
Write two Python functions for loading and chunking PDF documents:

1. `chunk_text(text, chunk_size, overlap)`:
   - Split text into overlapping chunks of specified size
   - Try to break at sentence boundaries (periods or newlines)
   - Return a list of chunk strings

2. `load_pdf(filepath, category)`:
   - Use PdfReader to extract text from each page
   - Apply the chunk_text function to each page
   - Filter out chunks shorter than MIN_CHUNK_LENGTH
   - For each chunk, create metadata dictionary based on METADATA_OPTIONS
   - Return a list of dictionaries with "text" and "metadata" keys

Then load two PDFs:
- Company Policies.pdf as HR (category="HR")
- Corporate Policies.pdf as TECH (category="TECH")

Also create a simple general_chunks list with one fallback message.
Print how many chunks were loaded from each source.
```

---

## 🗃️ Block 7: Initialize Vector Database

**Prompt:**
```
Write Python code to initialize ChromaDB as an in-memory vector database.

Create three collections:
- hr (for HR policies)
- tech (for tech policies)  
- general (for other queries)

Use get_or_create_collection to avoid errors if collections already exist.
Print confirmation when initialized.
```

---

## 📥 Block 8: Load Documents into Vector Database

**Prompt:**
```
Write a Python function `load_chunks_to_collection(collection, chunks, prefix)` that:

1. Extracts text from each chunk
2. Generates embeddings using the embedding_model.encode() function (show progress bar)
3. Adds each chunk to the collection with:
   - The embedding as a list
   - The document text
   - The metadata dictionary
   - A unique ID using the prefix and index

4. Prints how many chunks were loaded

Then call this function for hr_chunks, tech_chunks, and general_chunks.
```

---

## 🔍 Block 9: Intent Classifier

**Prompt:**
```
Create an intent classifier for a corporate assistant using an LLM.

The classifier should categorize questions into exactly 3 categories:
- HR: Questions about employees, attendance, leave, benefits, dress code, workplace behavior, harassment, conflicts, disciplinary procedures, grievances, employee handbook
- TECH: Questions about data protection, GDPR/CCPA, information security, acceptable use of technology, passwords, MFA, VPN, incident response, data retention, remote work security
- OTHER: Questions not related to HR or tech policies

Write:
1. A system prompt that instructs the LLM to respond with ONLY one word (HR, TECH, or OTHER)
2. A function `classify_intent(question)` that:
   - Sends the system prompt and user question to the LLM
   - Parses the response to ensure it's one of the valid categories
   - Returns "OTHER" as fallback if parsing fails
```

---

## 🔀 Block 10: Router (Switch)

**Prompt:**
```
Write a simple Python function `route_collection(intent)` that:

Takes an intent string (HR, TECH, or OTHER) and returns:
- For "HR": the hr_collection and the name "HR Base"
- For "TECH": the tech_collection and the name "IT Support Base"  
- For anything else: the general_collection and the name "General Base"

Return both the collection object and its display name as a tuple.
```

---

## 🔎 Block 11: Document Retrieval

**Prompt:**
```
Write a Python function `search_relevant_documents(collection, question, n_results)` that:

1. Generates an embedding for the question using the embedding_model
2. Queries the ChromaDB collection with:
   - The question embedding
   - Number of results (n_results)
   - Include documents, metadatas, and distances

3. Converts the results into a list of dictionaries, each containing:
   - text: the document content
   - source: from metadata
   - page: from metadata
   - chunk_id: from metadata
   - char_count: from metadata
   - category: from metadata
   - similarity: convert distance to similarity (1 - distance), rounded to 3 decimals

Return this list of dictionaries.
```

---

## 💬 Block 12: Response Generator

**Prompt:**
```
Create a response generator for a corporate HR & IT assistant.

Write a system prompt that instructs the LLM to:
- Act as the Official HR & IT Assistant
- Answer based ONLY on the provided context
- Give direct answers (don't start with "Based on the documents...")
- Cite sources at the end (e.g., "Source: Company Policies, Page 3")
- Admit when information is not found instead of inventing
- Use bullet points for lists
- Be professional, polite, and concise
- Include safety checks: no medical/legal advice, no passwords, refuse unethical requests

Then write a function `generate_response(question, context_docs, intent)` that:
1. Formats the context documents with their source citations
2. Customizes the context header based on intent (HR/TECH/OTHER)
3. Sends the system prompt and formatted user message to the LLM
4. Returns the response
```

---

## 🔗 Block 13: Complete RAG Pipeline

**Prompt:**
```
Write a function `rag_pipeline(question)` that orchestrates the entire RAG flow:

1. Print the question with a header
2. Step 1: Call classify_intent() and print the detected intent
3. Step 2: Call route_collection() and print the selected knowledge base
4. Step 3: Call search_relevant_documents() and print each document with:
   - Source, page, chunk ID
   - Similarity score and character count
   - A preview of the text (first 60 characters)
5. Step 4: Call generate_response() with the question, documents, and intent
6. Print the final answer with a clear header

Use emojis and separators to make the output easy to read.
Return the response.
```

---

## 🧪 Block 14: Test Examples

**Prompt:**
```
Write three test calls to the rag_pipeline function:

1. An HR question: "How many vacation days do I have per year?"
2. A TECH question: "How do I reset my corporate password?"
3. A general question: "Where is the cafeteria?"

Also create a cell where users can easily change a variable `my_question` to test their own questions.
```

---

## 📊 Block 15: Summary Documentation

**Prompt:**
```
Write a markdown summary of the RAG system that includes:

1. A flowchart showing the pipeline steps (using ASCII art or code blocks)
2. A list of components used:
   - LM Studio for local LLM
   - Sentence Transformers for embeddings
   - ChromaDB for vector storage
   - Requests for HTTP calls

3. Advantages of this approach:
   - 100% local/private
   - No API costs
   - Works globally
   - Grounded responses
   - Traceability

4. Possible improvements for future versions

Use emojis and formatting to make it visually appealing.
```

---

## 💡 Tips for Students

1. **Run blocks sequentially**: Each block depends on the previous ones
2. **Experiment with parameters**: Try different CHUNK_SIZE and NUM_RESULTS values
3. **Read the errors**: Most errors come from LM Studio not running or PDFs not found
4. **Check your PDFs**: Make sure they're in the Docs folder with the exact names
5. **Ask the LLM for help**: If code doesn't work, paste the error and ask for fixes!

---

## 🎯 Challenge: Extend the System

Once you have the basic RAG working, try these prompts:

**Add a confidence threshold:**
```
Modify search_relevant_documents to filter out documents with similarity below 0.3
```

**Add conversation history:**
```
Modify the pipeline to keep track of the last 3 questions and answers, and include them in the context for follow-up questions.
```

**Add a web search fallback:**
```
If no relevant documents are found (all similarities below 0.2), suggest that the user search the web instead.
```

---

Happy building! 🚀
