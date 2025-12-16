# Fraudvisor — AI Agent for E‑commerce Scam Detection

AI Agent for Ecom Scam Detection built with TypeScript. Fraudvisor analyzes product listings, seller metadata, reviews, transaction patterns and communication snippets to detect likely scam/fraud signals and surface explainable reasons and remediation suggestions.

This repository contains the TypeScript backend/agent logic, prompts, and integration scaffolding for connecting an LLM + vector store + feature extractor pipeline to run automated scam detection for e‑commerce platforms.

- Repository: [aniketsingh98571/Fraudvisor_Mumbai_Hacks_2025](https://github.com/aniketsingh98571/Fraudvisor_Mumbai_Hacks_2025)
- Language: TypeScript (primary)
- Purpose: Provide an extensible AI agent that ingests e‑commerce data and flags suspicious listings/sellers with actionable explanations.

Table of contents
- Features
- Architecture & how it works
- Quick start
- Environment variables (.env.example)
- Running locally
- API / Usage examples
- Deployment (Docker)
- Testing
- Contributing
- Security & privacy
- License
- Contact

---

## Features
- Multi‑signal fraud detection combining textual (descriptions, messages, reviews) and structured signals (seller age, price deviations, shipping patterns).
- LLM-powered reasoning to generate human-readable explanations and remediation steps.
- Vector search support for prior reports / historical incidents (similarity-based retrieval).
- TypeScript codebase with clear folder structure and typed interfaces.
- Extensible adapters for different LLM providers and vector stores.

---

## Architecture & how it works (high level)
1. Data ingestion
   - Product listing metadata, seller profiles, transaction logs, review texts and conversations are collected.
2. Feature extraction
   - Deterministic rules and lightweight ML features are computed (price anomalies, sudden seller creation, common spam phrases).
3. Context building & retrieval
   - For each case, a context bundle is created. Optionally, a vector DB is queried for similar historical fraud cases to provide grounding evidence.
4. Agent / LLM orchestration
   - The agent composes a prompt with features + retrieved examples and asks the LLM to:
     - Score the risk (e.g., low / medium / high)
     - Provide supporting evidence (text highlights & features)
     - Suggest remediation and triage steps
5. Output
   - Structured JSON (risk, confidence, reasons, recommended actions) + human readable summary.

This pattern enables explainability while leveraging an LLM for nuanced reasoning.

---

## Quick start (development)
Prerequisites
- Node.js 18+ (or compatible)
- npm or yarn or pnpm
- An LLM provider key (OpenAI, Anthropic, etc.) or local LLM adapter
- Optional: Vector DB (Pinecone / Weaviate / Chroma) and a persistence DB if you want to store cases

Clone
```bash
git clone https://github.com/aniketsingh98571/Fraudvisor_Mumbai_Hacks_2025.git
cd Fraudvisor_Mumbai_Hacks_2025
```

Install
```bash
# npm
npm install

# or yarn
yarn
```

Build
```bash
npm run build
```

Run (dev)
```bash
npm run dev
```

Run (production)
```bash
npm start
```

---

## Environment variables (.env.example)
Create a `.env` file at the repository root. Example:
```bash
# Server
PORT=3000

# LLM provider (OpenAI, Anthropic, etc.)
LLM_PROVIDER=openai
OPENAI_API_KEY=sk-...

# Vector DB (optional)
VECTOR_DB_PROVIDER=pinecone
PINECONE_API_KEY=your_pinecone_api_key
PINECONE_ENVIRONMENT=us-west1-gcp

# Database / persistence (optional)
DATABASE_URL=postgres://user:pass@host:5432/dbname

# App config
LOG_LEVEL=info
NODE_ENV=development
```

---

## Configuration & adapters
The codebase is organized to allow swapping:
- LLM provider (OpenAI, Anthropic, local LLMs)
- Vector store (Pinecone, Weaviate, Chroma)
- Storage (Postgres / Mongo / file)

Check the `src/adapters/` directory to add or modify provider implementations.

---

## API / Usage examples
The project typically exposes an HTTP API (e.g., Express or Fastify). Replace endpoints below with actual routes in your implementation.

Example: submit a case for analysis
```bash
curl -X POST http://localhost:3000/api/analyze \
  -H "Content-Type: application/json" \
  -d '{
    "listing": {
      "id": "L-12345",
      "title": "Brand new phone at 80% off",
      "description": "Limited time, contact via WhatsApp",
      "price": 49.99,
      "currency": "USD"
    },
    "seller": {
      "id": "S-999",
      "createdAt": "2025-12-01T00:00:00Z",
      "rating": 2.1
    },
    "reviews": [
      {"text": "Fast shipping, but seller asked for direct payment", "rating": 2}
    ]
  }'
```

Sample JSON response (structured)
```json
{
  "risk": "high",
  "confidence": 0.92,
  "reasons": [
    "Price is 80% below market",
    "Seller account created < 2 weeks ago",
    "Seller asks for off‑platform contact"
  ],
  "actions": [
    "Block the listing until manual review",
    "Request seller verification documents",
    "Flag buyer protections & refund eligibility"
  ],
  "explainable_summary": "This listing exhibits multiple high‑risk markers..."
}
```

---

## Running with Docker
A Dockerfile and docker-compose (if present) can containerize the app. Example commands:
```bash
# build
docker build -t fraudvisor:latest .

# run
docker run -e OPENAI_API_KEY=sk-... -p 3000:3000 fraudvisor:latest
```

If you have docker-compose:
```bash
docker-compose up --build
```

---

## Testing
Run unit and integration tests:
```bash
npm test
```
Add tests under `tests/` or `__tests__/`. Mock LLM and vector adapters for CI to avoid external API calls.

---

## Contributing
Thank you for contributing! Please:
1. Fork the repo
2. Create a feature branch (feature/my-feature)
3. Write tests for new behavior
4. Open a PR describing the change and rationale

Contributor checklist:
- Follow TypeScript types and eslint rules
- Keep secrets out of commits (use .env)
- Document new adapters or environment variables

---

## Security & privacy
- Do NOT commit API keys or personal data.
- Use encryption / secure vaults for credentials in production.
- When sending user data to third‑party LLM providers, consider data handling and compliance requirements.
- Provide an opt‑out for storing personally identifiable information in vector stores.

---

## Roadmap / ideas
- Fine‑tune downstream classifier on labeled fraud cases
- Add UI dashboard for queues and triage
- Expand to support messaging platforms (WhatsApp, email) ingestion
- Plugin for marketplace platforms (Shopify, MercadoLibre, etc.)

---

## License
MIT License — see [LICENSE](./LICENSE) for details.

---

## Contact
Project maintained by [aniketsingh98571](https://github.com/aniketsingh98571). For questions or partnerships, open an issue or submit a PR.

Thank you for checking out Fraudvisor — if you run it, please share feedback and example cases (sanitized) so we can improve detectors and explainability.
