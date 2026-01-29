[[Ingestion Pipeline]] 
[[Pipeline.excalidraw]]

- Combining LLM's with retrival system ---> LLM's answers with general knowledge and with the provided material/resource knowledge
- also this method ensures ---> avoidance of overloading of LLM's with bigger prompts
## Context window - the amount of information/promts/englishwords/tokens the LLM's can process 

A company has **100s of docs**.  
An LLM can’t read _everything at once_ because it has a **context limit** (context window).

**RAG fixes this.**  
Instead of feeding all docs to the LLM, you:

1. Store the docs in a database/vector index
2. When you ask a question → RAG **searches only the most relevant doc parts**
3. It **pulls (retrieves)** just that small useful chunk
4. Then gives it to the LLM so it can answer **accurately** without overload

**Example:**  
You ask → _“What’s the company refund policy?”_  
RAG finds → only the refund section from 200+ policy docs  
LLM reads → just that part  
Answer comes → clean, fast, correct

So basically:

- **LLM = brain**
- **Docs = knowledge**
- **RAG = librarian who finds the exact page instead of throwing the whole library at the brain**

Result: You talk normally, it fetches smartly, you get real answers. 🚀

The context window of the recent models : [so large]
![[Pasted image 20251226134507.png]]

but the data the enterprice or large companies have is about TB's of data 
(about 1 quintillion data tokens so all cannot be dumped at once) so RAG is used 



