Market Intelligence & Impact Analysis Framework

Agentic AI + MCP (Model Context Protocol)

Overview

This project implements an Agentic AI backend system that automates market research and impact analysis for a given industry (e.g., NBFC, Insurance, SaaS).
Multiple AI agents collaborate in a controlled pipeline to generate a structured Market Intelligence Report, while all external actions are executed through a dedicated MCP Tool Server.

The system strictly separates:

Reasoning (AI Agents)

Execution (MCP Tools)

This design ensures modularity, traceability, and non-hallucinatory behavior.

Key Objectives

Automate industry market research using AI agents

Enforce strict separation between AI reasoning and execution

Generate a structured, schema-compliant market intelligence report

Support grounded Q&A over generated reports

Use only open-source tools and models

High-Level Architecture
User Request
   │
   ▼
FastAPI (/analyze)
   │
   ▼
LangGraph Orchestrator
   │
   ├─ Collector Agent  → decides what to research
   ├─ Extractor Agent  → structures raw content
   ├─ Impact Agent     → scores impact & actions
   └─ Writer Agent     → compiles final report
   │
   ▼
MCP Server (Tools)
   ├─ Web Search
   ├─ URL Fetch & Cleaning
   ├─ Entity Extraction
   ├─ Impact Scoring
   └─ Report Generation


Important:
Agents never perform scraping, parsing, or scoring directly.
All execution happens via MCP tools.

Technology Stack (Open Source Only)

Language: Python 3.10+

LLM Inference: Ollama (Llama3)

Agent Orchestration: LangGraph

API Framework: FastAPI

Web Extraction: Trafilatura

HTML Parsing: BeautifulSoup

Storage: SQLite (optional)

Inference Mode: Local (no paid APIs)

Project Structure
market-intel-ai/
│
├── api/
│   └── routes.py          # FastAPI endpoints
│
├── agents/
│   ├── collector.py       # Research query generation
│   ├── extractor.py       # Content structuring
│   ├── impact.py          # Impact analysis
│   └── writer.py          # Final report synthesis
│
├── orchestrator/
│   └── graph.py           # LangGraph pipeline
│
├── mcp_server/
│   ├── tools.py           # Execution tools
│   └── server.py          # Tool logging
│
├── storage/
│   └── logs.txt           # MCP execution logs
│
├── main.py                # Application entry point
├── requirements.txt
└── README.md

API Endpoints
GET /health

Health check for API and runtime.

Response

{ "status": "ok" }

POST /analyze

Triggers the full agentic market research pipeline.

Request

{
  "industry": "NBFC",
  "from": "2026-01-01",
  "to": "2026-01-15"
}


Response

{
  "report_id": "R001",
  "report": { ...structured JSON report... }
}

POST /chat

Allows grounded Q&A over a generated report.

Request

{
  "report_id": "R001",
  "question": "What are the major regulatory risks?"
}


Response

{
  "answer": "Not available in report.",
  "citations": []
}


The system intentionally avoids hallucination.
If information is not present in the report, it is not invented.

Output Report Schema (Summary)

Executive summary

Market drivers

Competitors

Impact radar (events, scores, actions)

Opportunities (≥5)

Risks (≥5)

90-day action plan

Source URLs

The output is strictly structured JSON, not free-form text.

MCP Execution Logs

All tool calls are logged to:

storage/logs.txt


Example:

[Collector] calling tool: search_web
[Extractor] calling tool: extract_entities
[Impact] calling tool: impact_score


These logs demonstrate correct MCP usage and are required for evaluation.

Installation & Setup
1. Create Virtual Environment
python -m venv venv
venv\Scripts\activate

2. Install Dependencies
pip install -r requirements.txt

3. Install Ollama (Windows)

Download and install from:
https://ollama.com/download

Verify:

ollama --version

4. Pull Model
ollama pull llama3


(Ollama runs automatically in the background on Windows.)

Run the Application
uvicorn main:app --reload


Open Swagger UI:

http://127.0.0.1:8000/docs

Design Principles

Agent-Tool Separation: Agents reason, tools execute

Deterministic Execution: MCP tools control side effects

Non-Hallucination: Chat responses are strictly grounded

Open-Source Compliance: No paid or closed APIs

Extensibility: New agents or tools can be added easily