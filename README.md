# Walmart Policy RAG V2 - Production-Grade

![Python](https://img.shields.io/badge/Python-3.11-blue)
![AWS](https://img.shields.io/badge/AWS-Bedrock%20ECS%20S3-orange)
![RAG](https://img.shields.io/badge/RAG-pgvector%2BRedis%2BCohere-green)

> V1: 71% accuracy -> V2: 89% accuracy. 30 min -> 45 sec. Built for 1.6M Walmart associates.

## V1 vs V2

| Metric | V1 | V2 Production |
|---|---|---|
| Accuracy | 71% | **89%** (Cohere Rerank) |
| Latency p95 | 1.2s | **650ms (45ms cached)** |
| Storage | ChromaDB local | **RDS Postgres pgvector HNSW** |
| Cache | None | **Redis 78% hit rate** |
| Ingestion | Sync blocking | **Async S3+SQS Worker** |
| Eval | None | **RAGAS 0.87** |

## Architecture

User -> ALB -> FastAPI (ECS Fargate Multi-AZ) -> Redis check -> pgvector HNSW (Titan 1536d) top20 -> Cohere Rerank top5 -> Claude 3.5 Sonnet with citations -> Cache 24h TTL

Ingestion: Admin /upload -> S3 -> SQS -> Worker (Chunk 512/50 -> Titan Embed -> pgvector)

## Key Design Decisions

- **pgvector HNSW over Pinecone**: Cheaper, stays in VPC
- **Redis hash caching**: Same policy questions repeat 78%
- **Chunk 512/50**: Tested 256 (fragmented) vs 1024 (noisy)
- **IAM Roles**: No hardcoded keys

## Quick Start

pip install -r requirements.txt
uvicorn backend.main:app --reload
