# Radiant

Radiant is a retrieval-augmented chat prototype for rare disease knowledge exploration. It combines query expansion, vector search, document reranking, and large language model generation to answer questions about rare diseases with links back to retrieved source material.

Live demo: [radiant.rarepath.ai](https://radiant.rarepath.ai)

This project was presented at the Rare Disease AI Hackathon at GitHub HQ in San Francisco, sponsored by Research to the People. The latest production version is proprietary; this repository contains an earlier open-source implementation.

The current prototype focuses on hypophosphatasia and Ehlers-Danlos Syndrome. It was built as part of the RarePath/Radiant effort described in the Radiant white paper, "Leveraging AI to improve diagnosis and treatment of rare diseases: a chat agent for equitable and accessible healthcare," from Oregon State University collaborators.

> Medical safety note: this project is an experimental research prototype. It is not a medical device, does not provide medical diagnosis or treatment, and should not be used as a substitute for advice from qualified clinicians.

## What It Does

- Expands user questions into related biomedical search queries.
- Embeds expanded queries with OpenAI embeddings.
- Retrieves candidate passages from a Chroma vector database.
- Reranks retrieved passages with a MaxSim-style reranker.
- Generates responses with GPT-4o or Llama 3 through Amazon Bedrock.
- Returns source URLs from the retrieved documents when available.
- Maintains a compact conversation summary for follow-up questions.

## Tech Stack

- Backend: Python, Flask, OpenAI, Amazon Bedrock, Chroma, LangChain
- Frontend: Angular
- Retrieval: OpenAI embeddings, Chroma vector search, MaxSim-style reranking
- Example/evaluation assets: PDF questions and CSV response files

## Repository Layout

```text
.
├── app.py                         # Flask API used by the frontend
├── query_processing/              # RAG pipeline modules
│   ├── database_retrieval.py      # Embedding and Chroma retrieval helpers
│   ├── generation.py              # GPT and Llama response generation
│   ├── query_expansion.py         # Abbreviation expansion and paraphrasing
│   ├── query_processor.py         # End-to-end query pipeline
│   ├── reranker.py                # MaxSim document reranking
│   └── summarizer.py              # Follow-up context summarization
├── frontend/                      # Angular chat interface
├── Examples/                      # Example evaluation queries and responses
├── static/ and templates/         # Legacy Flask static/template files
└── requirements.txt               # Python backend dependencies
```

## System Requirements

- Python 3.10+
- Node.js 18+ and npm
- A running Chroma server on `localhost:8000`
- OpenAI API credentials
- AWS credentials with access to the Bedrock model configured in `query_processing/generation.py`

The backend expects a Chroma collection named `vector-store-rare-diseases1` with document text, embeddings, and metadata that includes `URL` when citation links are available.

## Backend Setup

```bash
git clone https://github.com/axay28/Radiant.git
cd Radiant
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
export OPENAI_API_KEY="your-openai-api-key"
flask --app app run
```

If you use the older `OPENAI_API` environment variable elsewhere, keep it in sync with `OPENAI_API_KEY`. The current OpenAI SDK reads `OPENAI_API_KEY` by default.

## Frontend Setup

```bash
cd frontend
npm install
npm start
```

The Angular app runs at `http://localhost:4200/` by default. The Flask backend runs at `http://localhost:5000/` by default.

## API

`POST /get_response`

Request body:

```json
{
  "modelSelection": "gpt",
  "userQuery": "What are symptoms of hypophosphatasia?",
  "currentSummary": ""
}
```

`modelSelection` may be `gpt`, `llama`, or any other value to request both configured models.

## White Paper

For more background, see the Radiant white paper: [radiant.rarepath.ai/whitepaper](https://radiant.rarepath.ai/whitepaper)

## Development Notes

- Do not commit generated dependency folders such as `node_modules/`, Angular `.angular/` cache files, virtual environments, or local OS files.
- Example CSV/PDF files are included for reproducibility and evaluation context.
- The notebook is exploratory and may require model downloads or GPU resources.

## Contact

For inquiries or collaboration, contact [mulgunda@oregonstate.edu](mailto:mulgunda@oregonstate.edu).

## Citation And Project Context

If you use this repository in research or demos, cite the related Radiant/RarePath work and link back to the public repository:

```text
Radiant: retrieval-augmented AI chat for rare disease knowledge exploration.
GitHub: https://github.com/axay28/Radiant
```
