# Verification Notes

These checks were run on June 17, 2026 while preparing the public GitHub repository.

## Local Repository

- Python syntax check passed with bytecode redirected outside the repo:

```bash
PYTHONPYCACHEPREFIX=/private/tmp/radiant-pycache python3 -m compileall -q query_processing app.py
```

- Angular dependencies installed successfully:

```bash
cd frontend
npm ci
```

- Angular production build succeeded:

```bash
npm run build
```

The build completed with budget warnings for the initial bundle and `chat.component.css`, plus two Bootstrap selector warnings. These warnings do not block the build.

## End-To-End Runtime Requirements

The full RAG path cannot run from a clean checkout unless these external services and data are available:

- OpenAI API credentials for query expansion, embeddings, summarization, and GPT generation.
- AWS credentials with access to the configured Bedrock Llama model.
- A Chroma server on `localhost:8000`.
- A populated `vector-store-rare-diseases1` collection with document text, embeddings, and source URL metadata.

## Live Site Probe

The public site at `https://radiant.rtx.ai/` loads a Vite/React 18 frontend. The bundle exposes:

- `/` welcome route
- `/chat` authenticated/premium route using Clerk
- `/guest` guest chat route
- `/whitepaper` embedded white paper route
- `/survey` Qualtrics redirect
- `https://radiant.rtx.ai/llm/conversation` guest chat API
- authenticated chat endpoints under `/llm/chats/...`

A test request to the guest chat endpoint returned an application-level generation error:

```json
{
  "currentSummary": "",
  "data": [
    {
      "agentName": "LLaMa 3.1",
      "agentResponse": "Error generating response. Please try again later.",
      "urls": []
    }
  ],
  "title": ""
}
```

That means the live frontend and API are reachable, but the live generation path appeared unhealthy during this check.
