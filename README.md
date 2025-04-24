# Adaptive LLM powered Document Segmentation for RAG

## Overview

This project explores a novel approach to document chunking for Retrieval-Augmented Generation (RAG) systems. Inspired by the semantic chunking capabilities demonstrated in the LumberChunker paper and the query-adaptive nature of the Mix-of-Granularity paper, this project uses a Large Language Model (LLM) to:

1.  **Generate two distinct semantic segmentations** of input documents: one optimized for specific, fact-based queries (finer granularity) and another for broader, topic-based queries (coarser granularity).
2.  **Classify incoming user queries** as either "Specific" or "Broad".
3.  **Dynamically select the appropriate chunk set** for retrieval based on the query classification, aiming to improve the relevance and quality of retrieved context for the RAG pipeline.

The goal is to enhance RAG performance by better aligning the granularity of retrieved information with the user's query intent.

## Key Concepts

*   **LLM-Guided Semantic Chunking:** Using an LLM (e.g., Llama 2 7B) to identify semantic boundaries within documents, similar to LumberChunker, but guided by different prompts and input context sizes (`theta`) for different granularities.
*   **Dual Granularity Generation:** Creating two parallel sets of chunks for each document – "Specific" (capturing finer semantic shifts) and "Broad" (capturing major topic shifts).
*   **LLM-Based Query Classification:** Utilizing an LLM to determine if a user query requires detailed facts ("Specific") or a broader overview ("Broad"), replacing a dedicated trained router.
*   **Query-Adaptive Retrieval:** Selecting the vector index (Specific or Broad chunks) to query based on the classified query type.

## Basic Workflow

1.  **Preprocessing:**
    *   For each document:
        *   Run LLM chunking process with `Prompt_Specific` and `θ_specific` -> Generate "Specific Chunks".
        *   Run LLM chunking process with `Prompt_Broad` and `θ_broad` -> Generate "Broad Chunks".
    *   Embed "Specific Chunks" -> Store in `Index_Specific`.
    *   Embed "Broad Chunks" -> Store in `Index_Broad`.
2.  **Runtime:**
    *   Receive User Query.
    *   Use LLM to classify Query as "Specific" or "Broad".
    *   If "Specific", query `Index_Specific`.
    *   If "Broad", query `Index_Broad`.
    *   Retrieve top-k relevant chunks.
    *   (Implicit) Pass chunks and query to a generator LLM for the final RAG response.

## Dataset

Evaluation is planned using standard QA datasets suitable for retrieval evaluation. The primary candidate is:

*   **NarrativeQA## Evaluation

