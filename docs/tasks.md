# Tasks - V2 Sprint

## Phase 1: Foundation [DONE]
- [x] PRD, Architecture, Tasks docs
- [ ] Setup RDS Postgres + pgvector extension
- [ ] Create HNSW index

## Phase 2: Backend API
- [ ] FastAPI /query with Redis cache
- [ ] pgvector retriever + Cohere rerank
- [ ] Claude 3.5 Sonnet prompting with citations
- [ ] /upload endpoint -> S3

## Phase 3: Async Ingestion
- [ ] SQS queue + worker
- [ ] Chunking 512/50 + Titan embed

## Phase 4: Eval & Deploy
- [ ] RAGAS eval pipeline >0.85
- [ ] Docker + ECS Fargate Multi-AZ
- [ ] CloudWatch metrics

Status: Docs phase complete, starting backend
