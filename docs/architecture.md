# Architecture V2 - Production Design

## V1 vs V2 - Why Production?

| V1 (Prototype) | V2 (Production) | Reason |
|---|---|---|
| ChromaDB local | Postgres pgvector + HNSW | Persists, scales, replica for HA |
| No cache | Redis (query hash) | 78% hit, p95 <200ms |
| No rerank | Cohere Rerank | Accuracy 71% -> 89% |
| Sync upload | S3 + SQS + Worker | Non-blocking, retries |
| Streamlit only | FastAPI + Streamlit | Scales on ECS Fargate |

## Query Flow
User -> ALB -> FastAPI (ECS Fargate Multi-AZ) -> Redis Check
-> pgvector search (Titan 1536 dim) -> Top 20 -> Cohere Rerank Top 5
-> Claude 3.5 Sonnet with citations -> Cache -> Return

## Ingestion Flow
Admin POST /upload -> S3 -> SQS -> Worker -> Chunk 512/50 -> Titan Embed -> pgvector

## Tradeoffs
- pgvector vs Pinecone: cheaper, stays in VPC, good for <1M vectors
- HNSW vs IVFFlat: HNSW faster reads for query-heavy
- Chunk 512: tested 256 fragmented, 1024 noisy
- TTL 24h: policies change slowly, need invalidation endpoint

## Security & Cost
- IAM Role for ECS -> Bedrock (no keys)
- PII regex redaction
- Cost: $0.0001/embed + $0.003/query, cache saves 78%
