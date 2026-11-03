# UNIFY Chat Bot — RAG AI Knowledge Assistant

An AI-powered conversational knowledge assistant built with **n8n**, **Retrieval-Augmented Generation (RAG)**, **OpenAI GPT-4o**, **Milvus**, and **Cohere embeddings**. It answers questions using documents stored in a vector database and can automatically ingest newly added Google Drive files.

> **Project status:** Academic/personal prototype. The workflow is exported from n8n; configure your own credentials, vector database collection, and Google Drive folder before running it.

## Overview

The project combines two workflows:

1. **Document ingestion:** Detect a newly created file in a configured Google Drive folder, download it, extract PDF text, split it into chunks, generate embeddings, and insert the document vectors into Milvus.
2. **Question answering:** Receive a user's chat message, retrieve relevant chunks from Milvus, and pass the retrieved context to an AI agent powered by GPT-4o. A window memory node supports conversational context.

## Architecture

### A. Knowledge-base ingestion

`Google Drive Trigger → Download File → Extract from File (PDF) → Milvus (Insert)`

- Google Drive Trigger checks a selected folder for newly created files (configured to poll every minute in this export).
- Download File retrieves the new file.
- Extract from File extracts PDF content.
- Default Data Loader and Recursive Character Text Splitter prepare documents for embedding.
- Chunk settings in the export: **700 characters**, **60-character overlap**.
- Cohere `embed-multilingual-v3.0` generates embeddings.
- Milvus stores the embedded document chunks.

### B. Chat / RAG pipeline

`Chat Trigger → RAG Agent ← GPT-4o + Window Memory + Milvus Retriever`

- The n8n Chat Trigger receives user messages.
- The RAG Agent uses OpenAI GPT-4o as its language model.
- Milvus is connected as a retrieval tool (`vector_store`) with **Top K = 10**.
- Cohere embeddings are used to query the vector database.
- Window Buffer Memory provides conversation context.

## Technology Stack

- **Workflow automation:** n8n
- **AI agent / orchestration:** n8n AI Agent and LangChain nodes
- **LLM:** OpenAI GPT-4o
- **Embedding model:** Cohere `embed-multilingual-v3.0`
- **Vector database:** Milvus
- **Document source:** Google Drive
- **Document processing:** PDF extraction, Default Data Loader, Recursive Character Text Splitter

## Key Features

- Conversational question answering over uploaded knowledge-base documents
- Retrieval-based responses grounded in indexed document content
- Automatic ingestion of newly created Google Drive files
- PDF text extraction and chunking before indexing
- Semantic search using vector embeddings
- Conversational memory for follow-up questions
- Modular workflow that can be adapted to different document collections

## Setup (high level)

1. Install or access an n8n instance with the required community/AI nodes available.
2. Import the workflow JSON into n8n.
3. Create and configure credentials for:
   - Google Drive OAuth2
   - OpenAI API
   - Cohere API
   - Milvus
4. Select your own Google Drive folder and Milvus collection.
5. Confirm the collection's vector dimensions and embedding configuration match the Cohere model.
6. Test document ingestion with a sample PDF.
7. Open the n8n chat and ask questions based on the indexed content.
8. Review executions and logs, then activate the workflow when ready.

**Note:** This is a workflow export, not a one-click hosted application. Credentials and external services must be configured by each user.

## Potential Relevance to APCRDA

This project demonstrates skills that could be useful in public-sector and urban-development technology environments:

- **Document knowledge access:** A RAG assistant could help staff or citizens search approved policy documents, guidelines, FAQs, project reports, and other public information.
- **Information retrieval:** Semantic search can make long documents easier to discover without requiring exact keyword matches.
- **Workflow automation:** Google Drive-based ingestion reduces manual effort when new documents are added.
- **Scalable knowledge management:** A vector database provides a foundation for indexing and retrieving information from a growing document collection.
- **Responsible implementation:** A real deployment would require approved data sources, access controls, privacy/security review, source citations, human oversight, and testing for accuracy. This repository is a prototype and is **not an official APCRDA system**.

## Skills Demonstrated

- n8n workflow design and automation
- Retrieval-Augmented Generation (RAG)
- AI agent integration
- LLM and embedding model integration
- Vector databases and semantic retrieval
- PDF processing and text chunking
- API/credential configuration
- Debugging and workflow execution monitoring

## Repository Structure

```text
unify-rag-chatbot/
├── README.md
├── workflow/
│   └── unify-rag-chatbot.json   # Sanitized n8n workflow export
└── screenshots/
    └── workflow-overview.png     # Workflow screenshot
```

## Security & Privacy

Before publishing the workflow:

- Export a clean workflow and remove credential references/IDs, webhook IDs, instance metadata, and private folder/collection identifiers where possible.
- Never commit API keys, OAuth tokens, passwords, `.env` files, private documents, or personal data.
- Use GitHub Secrets or environment variables for secrets in any code-based deployment.
- Make sure documents indexed in the vector database are permitted for public or project use.
- Do not upload confidential government, organization, or personal records to a public repository or third-party AI service.

## Future Improvements

- Add source-document citations and links in answers.
- Add metadata filters (document title, category, date, department).
- Support more file formats and robust error handling.
- Add a document re-indexing/deletion process.
- Add access control and role-based retrieval.
- Evaluate answer quality, retrieval relevance, latency, and cost.
- Build a simple user-facing web interface.
- Add human feedback and unanswered-question tracking.

## Author

**Jeevan Kumar Pesala**  
B.Tech Computer Science (2026) | AI Automation & Full-Stack Developer

- Portfolio: https://jeevanweb.netlify.app/
- GitHub: https://github.com/JeevanKumar-2005
- LinkedIn: https://www.linkedin.com/in/jeevankumarpesala123

## Disclaimer

This repository is an independent learning/project demonstration. It is not affiliated with, endorsed by, or deployed for APCRDA. Any APCRDA-related use is a possible future application subject to official approval and security requirements.
