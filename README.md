# Text-Summarization
Text Summarization Using Langchain.

Import all the required Libraries like Langchain, langchain_community, python_dotenv, requests, typing.

# Explanation

import os : Provides a way to interact with operating System like the functions related to it.
from dotenv import load_dotenv : Function to load env variables from .env file and it will keep our API's Safely.
from typing import Union : Used for type hinting, making the code clearer. Union[list[Document], None] means a function can return either a list of Document objects or nothing (None).
import requests # For URL validation : used to HTTP Request. In this project it helps in checking whether the URL is correct or not.

Langchain Imports
from langchain_groq import ChatGroq : Imports specific langchain class that allows us to connect and use Groq model.
from langchain.chains.summarize import load_summarize_chain : It is pre-built Langchain tool which is specially for Summarization Tasks.
from langchain_core.prompts import PromptTemplate : It is langchain tool which helps in creating reusable and flexible prompts to send to the language model.
from langchain_core.documents import Document : It is used to represent a piece of text in Langchain.
from langchain.text_splitter import RecursiveCharacterTextSplitter : It is used to make a bigger text into smaller, meaningful chunks
from langchain_community.document_loaders import UnstructuredURLLoader, WebBaseLoader, PyPDFLoader : These are tools for loading data from various sources into the LangChain Document format.
1. UnstructuredURLLoader: A sophisticated loader that tries to extract the main content from a webpage, ignoring ads, navigation bars, etc.
2. WebBaseLoader: A URL Loader
3. PyPDFLoader: PDF Loader.

1. Temperature : it is used to control the creativity of the model.
2. split_text_into_docs:- Helper Function.
3. chunk_size=4000: Sets the maximum size of each text chunk.
4. chunk_overlap=200: Each new chunk will contain the last 200 characters of the previous chunk. This helps maintain context between chunks.
5. docs = text_splitter.create_documents([text]): Performs the splitting and wraps each chunk in a Document object.
6. def load_docs_from_url(...): Defines the function.
7. requests.head(url, ...): This is a clever optimization. It sends a HEAD request, which fetches only the headers of the URL, not the full content. It's a fast way to check if the URL is accessible and valid before committing to a full download.
8. loader = UnstructuredURLLoader(urls=[url]): It first tries the UnstructuredURLLoader, which is good at parsing the main article content.
9. if docs and docs[0].page_content.strip():: This checks if the loader successfully extracted non-empty content.
10. loader = WebBaseLoader(url): If the first loader fails, it falls back to the simpler WebBaseLoader.
11. try...except: The entire process is wrapped in error handling to gracefully manage network errors (requests.exceptions.RequestException) or any other issues.
12. def load_docs_from_pdf(...): Defines the function.
13. if not os.path.exists(file_path):: A validation step to ensure the file actually exists at the given path.
14. if not file_path.lower().endswith(".pdf"):: Another validation to check if the file has a .pdf extension.
15. loader = PyPDFLoader(file_path): Initializes the PDF loader with the file path.
16. docs = loader.load(): This command extracts the text from the PDF. PyPDFLoader returns a list of Document objects, where each object typically corresponds to a single page in the PDF.
17. def summarize_text_stuff(...): Defines the function, taking the content, the LLM instance, an optional prompt, and the input_type as arguments.
18. if input_type == "url":: If the input is a URL, it calls load_docs_from_url.
elif input_type == "pdf":: If the input is a PDF, it calls load_docs_from_pdf.
elif input_type == "text":: If the input is just text, it wraps it in a Document object directly.
19. combined_content = "\\n\\n".join(...): For URLs and PDFs, which might be loaded as multiple documents (e.g., one per page), this line joins them all into a single, large string. This is necessary for the "stuff" chain type.
20. docs_to_summarize = [Document(page_content=combined_content)]: The combined text is put into a single Document object, which will be "stuffed" into the prompt.
21. prompt = PromptTemplate(...): This defines the instructions for the LLM. The "{text}" is a placeholder where the content to be summarized will be inserted.
22. chain = load_summarize_chain(llm, chain_type="stuff", ...): This is where the LangChain magic happens.
23. chain_type="stuff": This tells LangChain to use the simplest summarization strategy: take all the text and "stuff" it into a single prompt and make one API call to the LLM. This is fast but only works if the total text fits within the model's context window.
24. verbose=True: This is a great debugging tool. It prints out the intermediate steps of the chain, including the exact prompt that is sent to the LLM.
25. summary = chain.invoke(...): This executes the chain. It takes the document(s), inserts the content into the prompt, sends it to the Groq LLM, and gets the response.
26. return summary["output_text"]: The result from the chain is a dictionary. This line extracts the generated summary text and returns it.
