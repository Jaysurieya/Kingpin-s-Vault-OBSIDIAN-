# Tokens:

==What are tokens?==

In the context of language models, a token is a uok tell enit of text that the LLM model processes.

Tokens can be as short as one character or as long as one word, depending on the language
and structure of the text

For example, the word "hello" is one token, while the phrase "I'm" is typically broken down
into two tokens: "I" and '''m."

==what are vector embeddings:==

- They are the mathmatical representation of the tokens 
- eg: cat = [34,8,7.5]
- each number in the representation is called ==dimention== 
- each dimension represents a characteristic of that particular word
- cat = [34,8,7.5] , kitten = [32,8,2] , since both the words are similar to each other they both have close and similar representation

![[Pasted image 20251226144213.png]]
vector embedding graph (example)

- 3 dimensions is just for example but in realtime the dimension ranges are large [eg: openai - above 3000 (3072) dimension]
- irrespective of the size of the token - all tokens have the same fixed embedding
[eg : cat also have 3072 dimensions and a para also have 3072 dimensions]

advantage of more dimensions --> more accurate 
disadvantage --> tough and costly to store 

## popular embedding models 

openai [2 models ] ---> mostly used in industries
voyage
mistral ---> open source and free
cohere

## vector database:

A database used for storing the vector embeddings is called ==vector database==
eg: pinecone , ==FAISS== , chromaDB\

FAISS - built by meta


==You MUST use the same embedding model for both your documents AND your user queries.==

Think of embeddings like different languages. If you embed your documents using
text-embedding-3-small but embed user queries using text-embedding-3-large, they literally can't
understand each other

Even within the same model, you must use the exact same dimensions everywhere.

For example: If you embed your documents using text-embedding-3-large with 1536 dimensions,
you MUST also embed user queries with text-embedding-3-large at exactly 1536 dimensions.

## REQUIRED PACKAGES AND USES:
cmd to install all --> [poetry add langchain langchain-community langchain-text-splitters langchain-openai chromadb redis python-dotenv fastapi uvicorn]

- langchain
- langchain-community
- langchain_text_splitters - used for chunking 
- langchain_openai - embedding model 
- chromadb - vector database

==Ingestion Pipeline:==

```
import os
from langchain_community.document_loaders import TextLoader, DirectoryLoader
from langchain_text_splitters import CharacterTextSplitter
from langchain_openai import OpenAIEmbeddings
from langchain_chroma import Chroma
from dotenv import load_dotenv

load_dotenv()

def load_documents(docs_path="docs"):
    """Load all text files from the docs directory"""
    print(f"Loading documents from {docs_path}...")
    
    # Check if docs directory exists
    if not os.path.exists(docs_path):
        raise FileNotFoundError(f"The directory {docs_path} does not exist. Please create it and add your company files.")
    
    # Load all .txt files from the docs directory
    loader = DirectoryLoader(
        path=docs_path,
        glob="*.txt",
        loader_cls=TextLoader
    )
    
    documents = loader.load()
    
    if len(documents) == 0:
        raise FileNotFoundError(f"No .txt files found in {docs_path}. Please add your company documents.")
    
   
    for i, doc in enumerate(documents[:2]):  # Show first 2 documents
        print(f"\nDocument {i+1}:")
        print(f"  Source: {doc.metadata['source']}")
        print(f"  Content length: {len(doc.page_content)} characters")
        print(f"  Content preview: {doc.page_content[:100]}...")
        print(f"  metadata: {doc.metadata}")

    return documents

def split_documents(documents, chunk_size=1000, chunk_overlap=0):
    """Split documents into smaller chunks with overlap"""
    print("Splitting documents into chunks...")
    
    text_splitter = CharacterTextSplitter(
        chunk_size=chunk_size, 
        chunk_overlap=chunk_overlap
    )
    
    chunks = text_splitter.split_documents(documents)
    
    if chunks:
    
        for i, chunk in enumerate(chunks[:5]):
            print(f"\n--- Chunk {i+1} ---")
            print(f"Source: {chunk.metadata['source']}")
            print(f"Length: {len(chunk.page_content)} characters")
            print(f"Content:")
            print(chunk.page_content)
            print("-" * 50)
        
        if len(chunks) > 5:
            print(f"\n... and {len(chunks) - 5} more chunks")
    
    return chunks

def create_vector_store(chunks, persist_directory="db/chroma_db"):
    """Create and persist ChromaDB vector store"""
    print("Creating embeddings and storing in ChromaDB...")
        
    embedding_model = OpenAIEmbeddings(model="text-embedding-3-small")
    
    # Create ChromaDB vector store
    print("--- Creating vector store ---")
    vectorstore = Chroma.from_documents(
        documents=chunks,
        embedding=embedding_model,
        persist_directory=persist_directory, 
        collection_metadata={"hnsw:space": "cosine"}
    )
    print("--- Finished creating vector store ---")
    
    print(f"Vector store created and saved to {persist_directory}")
    return vectorstore

def main():
    """Main ingestion pipeline"""
    print("=== RAG Document Ingestion Pipeline ===\n")
    
    # Define paths
    docs_path = "docs"
    persistent_directory = "db/chroma_db"
    
    # Check if vector store already exists
    if os.path.exists(persistent_directory):
        print("✅ Vector store already exists. No need to re-process documents.")
        
        embedding_model = OpenAIEmbeddings(model="text-embedding-3-small")
        vectorstore = Chroma(
            persist_directory=persistent_directory,
            embedding_function=embedding_model, 
            collection_metadata={"hnsw:space": "cosine"}
        )
        print(f"Loaded existing vector store with {vectorstore._collection.count()} documents")
        return vectorstore
    
    print("Persistent directory does not exist. Initializing vector store...\n")
    
    # Step 1: Load documents
    documents = load_documents(docs_path)  

    # Step 2: Split into chunks
    chunks = split_documents(documents)
    
    # # Step 3: Create vector store
    vectorstore = create_vector_store(chunks, persistent_directory)
    
    print("\n✅ Ingestion complete! Your documents are now ready for RAG queries.")
    return vectorstore

if __name__ == "__main__":
    main()




# documents = [
#    Document(
#        page_content="Google LLC is an American multinational corporation and technology company focusing on online advertising, search engine technology, cloud computing, computer software, quantum computing, e-commerce, consumer electronics, and artificial intelligence (AI).",
#        metadata={'source': 'docs/google.txt'}
#    ),
#    Document(
#        page_content="Microsoft Corporation is an American multinational corporation and technology conglomerate headquartered in Redmond, Washington.",
#        metadata={'source': 'docs/microsoft.txt'}
#    ),
#    Document(
#        page_content="Nvidia Corporation is an American technology company headquartered in Santa Clara, California.",
#        metadata={'source': 'docs/nvidia.txt'}
#    ),
#    Document(
#        page_content="Space Exploration Technologies Corp., commonly referred to as SpaceX, is an American space technology company headquartered at the Starbase development site in Starbase, Texas.",
#        metadata={'source': 'docs/spacex.txt'}
#    ),
#    Document(
#        page_content="Tesla, Inc. is an American multinational automotive and clean energy company headquartered in Austin, Texas.",
#        metadata={'source': 'docs/tesla.txt'}
#    )
# ]
```

## import statements and uses:
from langchain_community.document_loaders import TextLoader, DirectoryLoader
- Directory loader for loading directory to get the source
- Textloader is used for loading the text docs from that particular directory 
from langchain_text_splitters import CharacterTextSplitter
- used for chunking 
from langchain_openai import OpenAIEmbeddings
- embeddings
from langchain_chroma import Chroma
- vector db
from dotenv import load_dotenv
- used for loading details form .env files

## ==Load docs function:==

// Load all .txt files from the docs directory
loader = DirectoryLoader(
	path=docs_path,
	glob=" * .txt ", ---> acesses all the .txt files from the directory
	loader_cls=TextLoader ---> textloader class from the imports
)

documents = loader.load() ---> this will load a list of documents like the following structure

```
documents = [
   Document(
       page_content="Google LLC is an American multinational corporation and technology company focusing on online advertising, search engine technology, cloud computing, computer software, quantum computing, e-commerce, consumer electronics, and artificial intelligence (AI).",
       metadata={'source': 'docs/google.txt'}
   ),
   Document(
       page_content="Microsoft Corporation is an American multinational corporation and technology conglomerate headquartered in Redmond, Washington.",
       metadata={'source': 'docs/microsoft.txt'}
   ),
   Document(
       page_content="Nvidia Corporation is an American technology company headquartered in Santa Clara, California.",
       metadata={'source': 'docs/nvidia.txt'}
   ),
   Document(
       page_content="Space Exploration Technologies Corp., commonly referred to as SpaceX, is an American space technology company headquartered at the Starbase development site in Starbase, Texas.",
       metadata={'source': 'docs/spacex.txt'}
   ),
   Document(
       page_content="Tesla, Inc. is an American multinational automotive and clean energy company headquartered in Austin, Texas.",
       metadata={'source': 'docs/tesla.txt'}
   )
]

```