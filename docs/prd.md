# PRD - Walmart Policy RAG V2 (Production)

## 1. Problem
1.6M Walmart associates waste 30+ mins searching 1000+ policy PDFs. HR gets 500+ repeat questions/day.

## 2. Users
- Store Associates (primary)
- HR Admins (upload policies)

## 3. User Stories
- As associate, I ask "what is dress code for 2024?" and get answer in <1s with source page number
- As associate, I ask follow-up "what about tattoos?" (multi-turn chat)
- As HR admin, I upload new PDF via /upload and it is searchable in 2 mins async

## 4. Functional Requirements
- RAG with citations (page numbers)
- Multi-turn chat history (Redis)
- Async ingestion via SQS
- PII redaction (no employee data in logs)
- Evaluation: RAGAS > 0.85

## 5. Non-Functional
- Latency p95 < 800ms (with cache < 200ms)
- Availability 99.9% (Multi-AZ ECS + Postgres replica)
- Cost < $100/month for 10k queries
- Secure: IAM roles, no hardcoded keys

## 6. Success Metrics
- 89% answer accuracy (vs 71% V1)
- 78% cache hit rate
- Time-to-answer: 30min -> 45 seconds
