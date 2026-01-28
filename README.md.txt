Problem: The goal was to create a chatbot that can answer questions and provide recommendations about movies based on a dataset, going beyond simple keyword matching to understand the user's intent and the movie content.

Solution - RAG System Implementation: The solution is built by building a Retrieval Augmented Generation (RAG) system in this notebook. The core idea of RAG is to augment a Language Model (LLM) with the ability to retrieve information from a custom knowledge base before generating a response.

Here's how it is achieved:

Data Preparation: started by loading the movie dataset, cleaning it by handling potential missing values and incorrect data types, and creating a comprehensive "description" for each movie.

Information Chunking and Indexing: split the movie descriptions into smaller text chunks and extracted relevant metadata (Title, Genre, Year, etc.). Later then generated numerical representations (vector embeddings) for these chunks and stored them in a searchable Vector Store (FAISS). Also created a BM25 index for keyword-based retrieval.

Hybrid Retrieval: To answer user queries, implemented a hybrid retrieval mechanism. This involves performing both a vector similarity search (finding semantically similar chunks) and a BM25 keyword search. The results from both methods are combined to get a more comprehensive set of relevant documents.

Language Model (LLM) and Prompting: used a Language Model (LLM) (OpenAI's GPT-4o) as the core of the chatbot. I defined a Prompt Template to instruct the LLM on how to use the retrieved documents as context to answer user questions.

Agent and Tools: Built a Langchain Agent powered by the LLM. The agent's role is to understand the user's natural language query and decide which tool to use. The agent is equipped with many Tools:

A tool for direct filtering (list_movies_by_filters) to handle simple requests for lists of movies based purely on metadata (like genre or year), bypassing the LLM for efficiency.
A tool for hybrid semantic and metadata search (semantic_search_movies_with_filters) to handle more complex queries requiring both understanding content and applying filters. This tool performs the combined vector and BM25 search and passes the results to the LLM for summarization.
A tool to fetch the plot of a specific movie (get_movie_plot) from an external API.
A tool to get the description of a specific movie (get_movie_description) from the dataset.
Session Memory : I also added Conversation Memory to the agent so it can maintain context and respond naturally to follow-up questions within a conversation.

User Interface: Used Gradio to create a simple web-based chat interface, allowing users to interact with the agent.

By combining these components, the chatbot can understand user queries, retrieve relevant movie information from its knowledge base using different methods (keyword, semantic, hybrid, direct lookup), and use the LLM to generate helpful and contextually relevant responses. Made the system more robust by handling potential data errors and making the filtering process more explicit for the agent.