[[My Tasks]] ---> linked with todo list
[[Mentoroid Work Flow.canvas|Mentoroid Work Flow]]
## **Final Master Workflow: StudyMate**

### **Phase 1: Project Goals & Core Features**

This phase defines the application's functionality, derived from the user scenarios. The primary goal is to create an AI-powered academic assistant that allows students to perform conversational Q&A on one or more uploaded PDF documents.

- **Section 1.1: Core Features**
    
    - **Multi-PDF Q&A:** Users can upload multiple PDFs and ask questions across the entire document set.
        
    - **Source Traceability:** Every AI-generated answer is accompanied by the original text chunks from the PDF that were used as a source, allowing users to verify the information.
        
    - **Session History:** The UI maintains a running log of all questions and answers within a single session.
        
    - **Data Export:** Users can download the entire Q&A session history as a formatted `.txt` file for later review.
        

---

### **Phase 2: The AI Backend (Python & FastAPI)**

This is the core engine of the project, built as a modular pipeline. It handles all PDF processing, semantic retrieval, and AI answer generation.

- **Section 2.1: Setup & Configuration**
    
    1. **Environment:** Create a `backend` folder and set up a Python 3.10.x virtual environment.
        
    2. **Dependencies:** Install all required libraries: `fastapi`, `uvicorn`, `python-dotenv`, `PyMuPDF`, `sentence-transformers`, `faiss-cpu`, `google-cloud-aiplatform`, `numpy`.
        
    3. **Secure Configuration:** Create a `.env` file to store Google Cloud credentials securely.
        
        - `GOOGLE_APPLICATION_CREDENTIALS`: Path to the Google Cloud service account JSON file.
            
        - `GCLOUD_PROJECT_ID`: Your Google Cloud project ID.
            
    4. **API Server:** Initialize a FastAPI application in a `main.py` file and configure CORS to allow connections from the React frontend (running on `http://localhost:3000`).
        
- **Section 2.2: Milestone 1 - PDF Parsing & Chunking (`POST /upload` Endpoint)** This endpoint is responsible for converting unstructured PDFs into a queryable format.
    
    1. **Activity 1 - Text Extraction:**
        
        - The endpoint will accept one or more PDF files simultaneously.
            
        - For each uploaded file, use the
            
            **PyMuPDF** library (imported as `fitz`) to open it as a stream and process it page-by-page.
            
        - Concatenate the text from all pages into a single string, applying heuristic rules to normalize the text and handle formatting inconsistencies like headers, footers, and column layouts.
            
        - Aggregate the text from all uploaded PDFs into a single master corpus.
            
    2. **Activity 2 - Chunk Segmentation:**
        
        - Segment the master corpus into overlapping text chunks to maintain context continuity.
            
        - **Strategy:** Use a sliding window approach to create chunks of **500 words** with a **100-word overlap**.
            
        - Store each chunk with associated metadata (e.g., source filename, chunk number) for traceability.
            
- **Section 2.3: Milestone 2 - Embedding & Indexing (Continues in `/upload` Endpoint)** This part of the pipeline transforms the text chunks into a searchable vector index.
    
    1. **Activity 1 - Embedding Generation:**
        
        - Load the
            
            **`all-MiniLM-L6-v2`** model from the `sentence-transformers` library. This model should be pre-loaded and cached to improve performance.
            
        - The model will convert each text chunk into a
            
            **384-dimensional** vector optimized for semantic similarity.
            
        - Append all output vectors into a NumPy array, which forms the embedding matrix for the entire document set.
            
    2. **Activity 2 - FAISS Index Construction:**
        
        - Use the
            
            **FAISS** library to index the embedding matrix.
            
        - **Strategy:** Create a flat L2 index (**`IndexFlatL2`**) as it is highly effective for CPU-only environments.
            
        - Add each vector to the index.
            
        - Serialize and save the final FAISS index to a file, which will be loaded at runtime for querying.
            
- **Section 2.4: Milestone 3 - Retrieval & AI Generation (`POST /ask` Endpoint)** This endpoint takes a user's question, retrieves relevant context, and generates a final answer.
    
    1. **Activity 1 - Query Embedding & Retrieval:**
        
        - The endpoint receives a user's question via a JSON payload.
            
        - The same
            
            `all-MiniLM-L6-v2` embedding model is used to convert the question into a query vector.
            
        - This query vector is then compared against the FAISS index to retrieve the
            
            **top 3 (k=3)** most semantically similar chunks based on cosine similarity.
            
        - The retrieved chunks are de-duplicated and ranked before being passed to the next stage.
            
    2. **Activity 2 - Prompt Construction:**
        
        - Dynamically assemble a prompt that will be sent to the AI model.
            
        - **Format:**
            
            1. A system message instructing the model, e.g., "Answer based strictly on the following context".
                
            2. The retrieved context chunks, presented clearly.
                
            3. The user's original question.
                
            4. A final instruction to avoid making up information.
                
    3. **Activity 3 - Model Invocation (Google Vertex AI):**
        
        - Initialize the Google AI Platform client using the credentials from the `.env` file.
            
        - Send the constructed prompt to the **`Gemini 1.5 Flash`** model.
            
        - **Generation Parameters:**
            
            - `max_new_tokens`: 300
                
            - `decoding_method`: Greedy
                
            - `temperature`: 0.5 (to retain a factual tone)
                
        - Wrap the API call in exception-handling logic to manage potential timeouts, failures, or invalid responses from the model.
            
    4. **Activity 4 - Output Structuring:**
        
        - Decode the response from the Gemini API and strip any extraneous whitespace.
            
        - Return a JSON response to the frontend containing the final answer and the source paragraphs used to generate it, ensuring traceability.
            
- **Section 2.5: Backend Testing & Validation**
    
    - During development, print and manually check intermediate outputs of the chunking process to ensure sentence completeness and logical flow.
        
    - Manually examine retrieved chunks for various academic queries (e.g., "What is overfitting?") to validate semantic relevance.
        

---

### **Phase 3: The User Interface (React)**

This is the intuitive frontend built with React, focusing on minimalism and real-time responsiveness.

- **Section 3.1: Setup & Configuration**
    
    1. Use `npx create-react-app frontend` to initialize the project.
        
    2. Install `axios` for API communication.
        
- **Section 3.2: UI Layout & Component Implementation**
    
    1. **Main Layout:** The application will have a clean, vertical layout with four main sections: Header, PDF Uploader, Query Interaction, and Session History. Use embedded CSS for custom styling, including a background image.
        
    2. **`FileUpload.js`**: Implement a file uploader component that supports multi-file drag-and-drop.
        
    3. **`ChatWindow.js`**:
        
        - Display the AI-generated answer in a clearly styled response card, rendered using a Markdown library for readability.
            
        - Below the answer, include an expandable section titled "Referenced Paragraphs" that shows the original source chunks.
            
        - Display the full Q&A history at the bottom of the page.
            
    4. **`MessageInput.js`**: A simple text input box and a submit button for user queries.
        
    5. **`DownloadButton.js`**: A button that triggers the download of the session history.
        
    6. **Live Feedback:** Display status banners to inform the user about the application's state (e.g., upload success, processing, errors).
        
- **Section 3.3: State Management & Session History**
    
    1. Use React's
        
        `useState` hook to act as the `session_state`, storing every question and its corresponding answer as the user interacts with the app.
        
    2. The
        
        `DownloadButton` component will serialize this state variable into a structured `.txt` file, including delimiters between Q&A pairs, and trigger a download in the browser.
        

---

### **Phase 4: Final Integration & Testing**

- **Section 4.1: End-to-End Testing**
    
    - Run the Python backend server and the React frontend development server simultaneously.
        
    - Test the full pipeline: upload a PDF, ask a question, verify the answer and source, check the session history, and download the log.
        
    - Test edge cases like empty inputs, invalid file formats, and model timeouts to ensure error handling routines provide clear feedback to the user.



# Core concepts:

- **Python Virtual Environment (venv):** This is like an **empty, isolated project box**. You creating it to keep all the libraries (like FastAPI, PyMuPDF, etc.) for one specific project separate from all your other Python projects. This prevents version conflicts.
    
- **FastAPI:** This is a **tool** (a web framework library) that you **put inside** that project box. You use this tool to build your actual API (the `/upload` and `/ask` endpoints).


Think of it this way:

- If your **React frontend** is the customer in a restaurant, and the **Python backend** (with your AI logic) is the kitchen...
    
- Then **FastAPI is the waiter**. 🤵
    
The customer (frontend) can't just walk into the kitchen. They give an order (a "request") to the waiter (FastAPI). The waiter takes that request to the kitchen, gets the food (the data or AI prediction), and brings it back to the customer (the "response").