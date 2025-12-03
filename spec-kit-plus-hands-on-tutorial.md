# Spec-Kit-Plus Hands-On Tutorial: Production Multi-Agent RAG

## 🎯 What You'll Build

A production-grade, scalable multi-agent RAG system with:
- Distributed document processing with Ray
- Stateful agents using Dapr actors
- OpenAI Agents SDK with MCP tools
- Kubernetes deployment with horizontal scaling
- Real-time streaming responses
- ADR and PHR tracking
- FastAPI backend + Next.js frontend

**Time:** 45-60 minutes  
**Difficulty:** Advanced  
**Target:** Production-ready enterprise system

---

## 📋 Prerequisites

### Required Software
```bash
# Check versions
python --version         # 3.11+
docker --version         # 20.10+
kubectl version          # 1.28+
helm version             # 3.12+

# Install if missing
pip install specifyplus
```

### Development Environment
- **AI Agent**: Claude Code (recommended) or Cursor
- **Kubernetes**: Minikube, Kind, or cloud cluster (AWS EKS, GKE, AKS)
- **OpenAI API Key**: For agents and embeddings

### Optional (Enhances Experience)
- Docker Desktop with Kubernetes enabled
- Lens (Kubernetes IDE)
- K9s (terminal UI for Kubernetes)

---

## 🚀 Step-by-Step Tutorial

### Step 1: Environment Setup (5 minutes)

```bash
# Install spec-kit-plus
pip install specifyplus

# Verify
sp check

# Start local Kubernetes (if using Docker Desktop)
# Or use minikube
minikube start --cpus=4 --memory=8192

# Verify cluster
kubectl cluster-info

# Install Dapr
dapr init -k

# Verify Dapr
kubectl get pods -n dapr-system

# Create project
sp init production-rag-system --ai claude
cd production-rag-system

# Set up environment
cp .env.example .env
# Edit .env: Add OPENAI_API_KEY
```

**Expected Output:**
- New directory with `.specify/` structure
- Kubernetes cluster running
- Dapr control plane pods ready
- Environment configured

---

### Step 2: Define Production Governance (4 minutes)

Launch Claude Code:
```bash
claude
```

In Claude, run:

```
/constitution Create comprehensive governance for production multi-agent RAG system:

ARCHITECTURE PRINCIPLES:
- Cloud-Native First: Kubernetes for orchestration, Dapr for actor model and state
- Distributed Compute: Ray for CPU/GPU-intensive tasks (PDF extraction, embeddings)
- Event-Driven: Pub/sub for agent communication, async processing
- Resilient: Circuit breakers, retries with exponential backoff, graceful degradation
- Observable: OpenTelemetry for tracing, structured logging with trace IDs
- Scalable: Horizontal Pod Autoscaling based on CPU/memory/custom metrics

AGENT DESIGN STANDARDS:
- Single Responsibility: Each agent has one clear purpose
- Stateless Services: Retrieval, Synthesis, Citation agents are stateless
- Stateful Actors: Ingestion and Orchestrator use Dapr actors for state management
- MCP Integration: All agents use Model Context Protocol for tool access
- A2A Communication: Agent-to-Agent messaging with typed Pydantic schemas
- OpenAI Agents SDK: Standard for all LLM-based agents
- Streaming: All user-facing responses stream via Server-Sent Events

CODE QUALITY:
- Python: Type hints everywhere, no implicit Any
- Pydantic v2: All request/response schemas, agent messages
- Testing: 85% minimum coverage
  * Unit tests: Mock LLM calls with fixtures
  * Integration tests: Test agent workflows end-to-end
  * Load tests: Validate 500 concurrent users
- Documentation: Docstrings for all public APIs
- Error Handling: Typed exceptions, structured error responses

AI/LLM GOVERNANCE:
- Model Strategy:
  * Primary: GPT-4-turbo for synthesis
  * Fallback: GPT-3.5-turbo on failure
  * Embeddings: text-embedding-3-large
- Streaming: Server-Sent Events for all LLM responses
- Token Tracking:
  * Per agent per request
  * Aggregated per tenant per day
  * Alerts when >90% of quota
- Retry Logic:
  * 3 attempts maximum
  * Exponential backoff: 1s, 2s, 4s
  * Circuit breaker: open after 5 consecutive failures
- Prompt Management:
  * All prompts in PHR (Prompt History Records)
  * Version control for prompt changes
  * A/B testing framework for prompt optimization

DEPLOYMENT:
- Containerization:
  * Multi-stage Docker builds
  * Base images: python:3.11-slim
  * Non-root user in containers
  * Health checks for all services
- Kubernetes:
  * Helm charts for all components
  * Separate namespaces: dev, staging, prod
  * Resource requests and limits on all pods
  * HPA for stateless services (min: 3, max: 10)
- CI/CD:
  * GitHub Actions for builds
  * Automated tests before deploy
  * Canary deployments for production
  * Rollback capability within 5 minutes
- Secrets Management:
  * Kubernetes Secrets for API keys
  * Sealed Secrets for GitOps
  * Rotation every 90 days

INFRASTRUCTURE:
- Vector Database: Qdrant
  * Deployed as StatefulSet
  * Persistent volumes for data
  * Multi-tenant collections with isolation
  * Backup to S3 daily at 2 AM UTC
- State Store: Redis Cluster
  * Separate instances for Dapr, Celery, app cache
  * Persistence with AOF
  * Automatic failover with Sentinel
- Object Storage: S3-compatible
  * Presigned URLs for uploads
  * Lifecycle policies: delete after 90 days
  * Versioning enabled

SECURITY:
- Zero-Trust: mTLS between all services via Dapr
- Authentication: JWT with 1-hour expiry, 7-day refresh
- Authorization: RBAC per tenant
- API Security:
  * Rate limiting: 100 requests/minute per user
  * Input validation: Pydantic on all inputs
  * SQL injection prevention: Parameterized queries only
  * XSS prevention: Content Security Policy headers
- Secrets: Never in code, logs, or responses
- Audit: All agent actions logged with user ID and trace ID
- Compliance: GDPR-ready (data portability, right to deletion)

PERFORMANCE:
- SLAs:
  * Document processing: <2min for 50-page PDF (p95)
  * Query response: <3s (p95)
  * System uptime: 99.9%
- Caching:
  * Embeddings: Cache for 24 hours
  * Search results: Cache for 5 minutes
  * API responses: ETags for conditional requests
- Database:
  * Connection pooling: Min 5, Max 20 per service
  * Query optimization: EXPLAIN ANALYZE all slow queries
  * Indexes: On all frequently queried fields
- Agent Optimization:
  * Parallel execution where possible
  * Request deduplication for identical queries
  * Batch processing for document uploads

OBSERVABILITY:
- Logging:
  * Structured JSON logs (structlog)
  * Log levels: DEBUG (dev), INFO (staging), WARN (prod)
  * Include in every log: timestamp, trace_id, service, level, message
  * Centralized: FluentBit → Elasticsearch
- Tracing:
  * OpenTelemetry automatic instrumentation
  * Trace every request end-to-end
  * Sample rate: 100% (dev), 10% (prod)
  * Backend: Jaeger
- Metrics:
  * Prometheus metrics for all services
  * Custom metrics: agent_invocations, token_usage, query_latency
  * Grafana dashboards for visualization
  * Alerts: PagerDuty for critical issues
- Health Checks:
  * Liveness: /health/live (service running?)
  * Readiness: /health/ready (service ready for traffic?)
  * Startup: /health/startup (initialization complete?)

ARCHITECTURE DECISION RECORDS (ADR):
- Create ADR for every significant decision:
  * Technology choices (Why Dapr over Istio?)
  * Deployment strategies (Why Kubernetes over serverless?)
  * Database selections (Why Qdrant over Pinecone?)
- Template:
  * Context: What problem are we solving?
  * Decision: What did we decide?
  * Consequences: Positive and negative impacts
  * Alternatives: What else did we consider?
- Location: .specify/specs/{feature}/architecture/adr-*.md
- Review: ADRs reviewed in every architecture review

PROMPT HISTORY RECORDS (PHR):
- Track all agent prompts:
  * Version number and date
  * Full prompt text
  * Performance metrics (accuracy, latency, tokens)
  * Changes from previous version
  * A/B test results
- Location: .specify/specs/{feature}/prompts/phr-*.md
- Update: On every prompt change
- Review: Monthly prompt optimization session
```

**What This Creates:**
- `.specify/memory/constitution.md` - Your project's "laws"
- Foundation for all subsequent decisions

**Validation:**
```bash
cat .specify/memory/constitution.md
```

---

### Step 3: Specify System Requirements (6 minutes)

```
/specify Build AgentRAG Pro - Enterprise Multi-Agent RAG Platform

SYSTEM OVERVIEW:
AgentRAG Pro is a production-grade, scalable RAG platform powered by a sophisticated multi-agent architecture. Built for enterprises requiring high throughput, multi-tenancy, and compliance. Deploys on Kubernetes with Dapr, Ray, and OpenAI Agents SDK.

MULTI-AGENT ARCHITECTURE:

1. Ingestion Agent (Dapr Actor - Stateful)
   Technology: Dapr actor with Ray workers
   Purpose: Process uploaded documents into searchable chunks
   
   Workflow:
   - Receives document upload event via Dapr pub/sub
   - Initializes actor state: {status: "processing", progress: 0}
   - Submits PDF to Ray worker for text extraction (parallel pages)
   - Submits text to Ray worker for semantic chunking (512 tokens, 50 overlap)
   - Submits chunks to Ray workers for embedding generation (batch size: 32)
   - Stores vectors in Qdrant with metadata (doc_id, page, chunk_index)
   - Updates actor state: {status: "completed", chunks: N}
   - Publishes "document.completed" event
   - Handles failures: Retry 3x, then mark as failed, send alert
   
   Scaling: Actor per document, automatic state persistence

2. Orchestrator Agent (Dapr Workflow - Stateful)
   Technology: Dapr workflow with OpenAI Agents SDK
   Purpose: Coordinate multi-agent query processing
   
   Workflow:
   - Receives user query via API
   - Creates workflow instance with trace_id
   - Step 1: Call Retrieval Agent via A2A (with retry policy)
   - Step 2: Call Synthesis Agent via A2A (with streaming)
   - Step 3: Call Citation Agent via A2A (with timeout)
   - Aggregates results into structured response
   - Stores query history in database
   - Returns to user with trace_id
   - On failure: Executes fallback workflow (simpler search)
   
   Scaling: Workflow instance per query, automatic state persistence

3. Retrieval Agent (OpenAI Agent with MCP - Stateless)
   Technology: OpenAI Agents SDK with MCP tools
   Purpose: Find relevant document chunks for query
   
   MCP Tools:
   - vector_search(query: str, top_k: int, filters: dict)
   - keyword_search(query: str, top_k: int)
   - hybrid_search(query: str, alpha: float, top_k: int)
   - rerank(query: str, chunks: List[str])
   
   Workflow:
   - Receives query from Orchestrator
   - Agent decides which tool(s) to use based on query
   - Calls vector_search MCP tool → Qdrant semantic search
   - Calls rerank MCP tool → Cross-encoder reranking
   - Returns top 5 chunks with scores and metadata
   - Logs: query, tools_used, chunks_found, duration_ms
   
   Scaling: Kubernetes HPA (3-10 pods), load balanced

4. Synthesis Agent (OpenAI Agent with Streaming - Stateless)
   Technology: OpenAI Agents SDK, GPT-4-turbo, SSE streaming
   Purpose: Generate comprehensive answer from retrieved chunks
   
   Workflow:
   - Receives query + chunks from Orchestrator
   - Constructs prompt with chunks as context (PHR-003)
   - Calls GPT-4-turbo with streaming
   - Yields tokens via async generator
   - Tracks: tokens_used, latency, grounding_rate
   - On failure: Falls back to GPT-3.5-turbo
   
   Prompt (PHR-003):
   "You are a synthesis agent. Answer based ONLY on provided context.
   Context: [chunks]
   Question: [query]
   Answer in 2-3 paragraphs with references [0], [1]..."
   
   Scaling: Kubernetes HPA (3-10 pods), rate limited per tenant

5. Citation Agent (OpenAI Agent - Stateless)
   Technology: OpenAI Agents SDK, GPT-3.5-turbo
   Purpose: Extract and format citations from synthesis output
   
   Workflow:
   - Receives synthesized answer + original chunks
   - Identifies which chunks were actually used
   - Extracts relevant sentences from each chunk
   - Formats as: [Document Name, Page X]
   - Adds metadata: chunk_id, relevance_score
   - Returns structured citations array
   
   Scaling: Kubernetes HPA (3-10 pods)

6. Monitoring Agent (Background Worker - Singleton)
   Technology: Celery beat scheduler
   Purpose: Track system health and costs
   
   Tasks:
   - Every 5min: Aggregate token usage per tenant
   - Every 15min: Calculate response time percentiles
   - Every hour: Detect anomalies (sudden spikes)
   - Every day: Generate cost report, email to admins
   - On anomaly: Send Slack alert to #ops channel
   
   Scaling: Single instance, scheduled tasks

CORE FEATURES:

Document Management:
- Upload: Drag-drop or API, PDFs up to 100MB
- Processing: Real-time progress via WebSocket
  * Status: pending → extracting → chunking → embedding → ready
  * Progress bar: "Processing page 23/50..."
- Storage: S3 with presigned URLs, 90-day retention
- Metadata: Auto-extract title, author, date, language
- Batch: Upload up to 10 documents simultaneously
- Retry: Automatic retry on transient failures
- Quota: 10GB storage per tenant, 1000 documents max
- Supported: PDF (primary), DOCX (future), TXT (future)

Intelligent Querying:
- Input: Natural language, up to 500 characters
- Real-time Agent Visualization:
  * Show active agent: "Retrieval Agent: Searching..."
  * Show progress: "Found 5 relevant chunks"
  * Show next agent: "Synthesis Agent: Generating..."
- Streaming: Character-by-character display (SSE)
- Confidence: Score 0-100% per answer
- Related Questions: Auto-generate 3 follow-up questions
- Context: Maintain conversation history (5 turns)
- Filters: Filter by document, date range, language
- Advanced: Support boolean operators (AND, OR, NOT)

Citations & Sources:
- Format: [Document Name, Page X]
- Interactive: Click citation → opens modal with page
- Highlighting: Yellow highlight on relevant text
- Context: Show 2 sentences before/after highlighted text
- Navigation: Jump between citations with arrow keys
- Export: Download citations as BibTeX or JSON
- Sharing: Generate shareable link for specific answer

Multi-Tenancy:
- Isolation: Separate Qdrant collections per tenant
- Quotas: Configurable per tenant (docs, storage, queries)
- Branding: Custom logo, colors per tenant
- Users: RBAC (admin, member, viewer)
- Audit: All actions logged per tenant
- Billing: Track usage for cost allocation
- API Keys: Per-tenant API keys with scopes

Admin Dashboard:
- Real-time Metrics:
  * Agent invocations per second
  * Query latency histogram (p50, p95, p99)
  * Token usage per agent (pie chart)
  * Active users (last 5 minutes)
- Cost Analysis:
  * Daily/weekly/monthly spend
  * Breakdown: embeddings vs. LLM calls
  * Cost per query, cost per document
  * Forecast: Estimated monthly cost
- System Health:
  * Kubernetes pod status
  * Dapr sidecar health
  * Ray worker utilization
  * Qdrant query performance
- Error Logs:
  * Filterable by agent, time, severity
  * Expandable for full stack trace
  * One-click: reprocess failed documents
- Alerts:
  * Configurable thresholds
  * Channels: Email, Slack, PagerDuty

USER STORIES:

Story 1: Enterprise Document Upload
As a knowledge worker, I upload our 80-page product manual
- I drag PDF into upload zone
- System shows: "Uploading... 15MB/15MB"
- Ray workers process 80 pages in parallel
- System shows: "Extracting text: Page 42/80..."
- System shows: "Generating embeddings: Batch 3/8..."
- Document ready in 90 seconds
- I see document in library with preview thumbnail

Story 2: Multi-Agent Query Flow
As a user, I ask "What are the safety warnings?"
- I type question, press Enter
- Orchestrator Agent receives query (trace_id: abc123)
- UI shows: "Retrieval Agent: Searching..." (animated)
- Retrieval Agent calls vector_search MCP tool
- Qdrant returns 5 chunks in 200ms
- UI shows: "Synthesis Agent: Generating answer..."
- Synthesis Agent streams response word-by-word
- UI displays answer as it generates
- Citation Agent extracts sources
- UI shows clickable citations: [Product Manual, Page 23]
- Total time: 2.8 seconds

Story 3: Agent Failure Recovery
As a system, when Synthesis Agent times out after 5s:
- Orchestrator detects timeout
- Workflow retries with exponential backoff
- Second attempt: Success with GPT-3.5-turbo (fallback)
- User sees answer with warning: "Generated with simplified model"
- Monitoring Agent logs anomaly
- Ops team receives Slack alert
- PHR updated: "GPT-4 timeouts increased 5x"

Story 4: Horizontal Auto-Scaling
As infrastructure, when query load increases to 200 qps:
- Kubernetes HPA detects CPU >70% on Synthesis pods
- HPA scales from 3 pods to 7 pods
- New pods start in 15 seconds
- Dapr sidecars attach automatically
- Load balancer distributes traffic
- Response times remain <3s (p95)
- When load drops, HPA scales down to 3 pods

Story 5: Cost Optimization via Dashboard
As an admin, I open the cost dashboard:
- See: $1,200 spent this month (↑15% vs. last month)
- Breakdown: Synthesis Agent = 70% of cost
- Drill down: 30% of synthesis calls for "simple" questions
- Decision: Route simple questions to GPT-3.5
- Update Orchestrator logic with confidence threshold
- Next week: Cost down to $900 (↓25%)
- PHR created: "ADR-005: Cost-based model routing"

Story 6: ADR Tracking
As a developer, before choosing Dapr over Istio:
- Create ADR-002: "Use Dapr for Service Mesh"
- Context: Need state management + service invocation
- Decision: Dapr (built-in actors, simpler)
- Consequences: ✅ Easier state, ⚠️ Newer ecosystem
- Alternatives: Istio (more mature, no actors)
- Review in architecture meeting
- Approved, committed to repo

Story 7: PHR Evolution
As an AI engineer, optimizing Synthesis prompt:
- Current: PHR-003 v1.0 (92% accuracy, 2.1s latency)
- Hypothesis: Shorter prompt = faster, same accuracy
- Create: PHR-003 v2.0 (reduced from 150 to 80 words)
- A/B test: 50/50 split over 1000 queries
- Results: v2.0 = 93% accuracy, 1.7s latency (↑ better!)
- Update: PHR-003 v2.0 becomes production default
- Document: Changes, metrics, test results in PHR

CONSTRAINTS:
- Performance:
  * 500 concurrent users minimum
  * <2 minutes for 50-page PDF processing (p95)
  * <3 seconds for query response (p95)
  * 99.9% uptime (max 43 minutes downtime/month)
- Scalability:
  * 10,000 documents per tenant
  * 100 tenants on single cluster
  * Horizontal scaling for all stateless services
- Compliance:
  * GDPR: Data portability, right to deletion
  * SOC 2 Type II ready
  * Data encryption at rest and in transit
  * Audit logs retained for 1 year
- Resource Limits:
  * Max 8GB RAM per service
  * Max 2 CPU cores per service  
  * Total cluster: 32GB RAM, 16 CPU cores

ACCEPTANCE CRITERIA:
- [ ] User uploads 50-page PDF, completes in <2min
- [ ] User asks question, receives streaming answer with citations in <3s
- [ ] System handles 500 concurrent queries without degradation
- [ ] Failed agent calls retry automatically with exponential backoff
- [ ] Admin sees real-time metrics dashboard (agents, costs, performance)
- [ ] All agent decisions logged with trace IDs
- [ ] System recovers from Redis/Qdrant outages within 30s
- [ ] Kubernetes HPA scales pods based on CPU/memory load
- [ ] ADRs created for all major architecture decisions
- [ ] PHRs track all agent prompts with version history
```

**Output:** `.specify/specs/001-agentrag-pro/spec.md`

**Validation:**
```bash
cat .specify/specs/001-agentrag-pro/spec.md
```

---

### Step 4: Clarify & Validate (4 minutes)

```
/clarify
```

**Example Q&A:**

**Q:** Qdrant deployment: Kubernetes StatefulSet or managed cloud?  
**A:** Kubernetes StatefulSet for control and cost. Use persistent volumes (AWS EBS, GKE persistent disk). Backup to S3 nightly. Managed Qdrant Cloud for prod if team lacks K8s expertise.

**Q:** Ray cluster sizing: How many workers?  
**A:** Start with 3 worker nodes (2 CPU, 4GB RAM each). Auto-scale to 10 based on task queue depth. Use KubeRay operator for management.

**Q:** MCP tools: Custom implementation or library?  
**A:** Use MCP Python SDK. Define tools as functions, register with OpenAI Agents SDK. Tools: vector_search, keyword_search, rerank.

**Q:** Streaming implementation: WebSocket or SSE?  
**A:** Server-Sent Events (SSE) for simplicity. Easier client implementation, automatic reconnection, works with HTTP/2. WebSocket overkill for one-way streaming.

**Q:** Dapr state store: Redis Sentinel or Cluster?  
**A:** Redis Cluster for horizontal scaling. Sentinel for HA if single instance. For production, use Redis Cluster (3 masters, 3 replicas).

**Q:** OpenTelemetry exporter: Jaeger, Zipkin, or Tempo?  
**A:** Jaeger for initial deployment (mature, good UI). Consider Tempo for cost-effective long-term storage (S3-backed).

**After answering:**
```
Read the review and acceptance checklist, and check off each item in the checklist if the feature spec meets the criteria. Leave it empty if it does not.
```

---

### Step 5: Design Production Architecture (10 minutes)

```
/plan Production multi-agent architecture:

INFRASTRUCTURE COMPONENTS:

1. Kubernetes Cluster (AWS EKS, GKE, or Minikube for dev)
   
   Namespaces:
   - agentrag-dev
   - agentrag-staging
   - agentrag-prod
   - dapr-system (Dapr control plane)
   - monitoring (Prometheus, Grafana, Jaeger)
   
   Resource Quotas per Namespace:
   - CPU: 16 cores
   - Memory: 32GB
   - Persistent Volumes: 100GB

2. Dapr 1.12+ (Service Mesh & Actors)
   
   Components:
   - State Store: Redis Cluster
     ```yaml
     apiVersion: dapr.io/v1alpha1
     kind: Component
     metadata:
       name: statestore
     spec:
       type: state.redis
       metadata:
       - name: redisHost
         value: redis-cluster:6379
       - name: actorStateStore
         value: "true"
     ```
   
   - Pub/Sub: Redis Streams
     ```yaml
     apiVersion: dapr.io/v1alpha1
     kind: Component
     metadata:
       name: pubsub
     spec:
       type: pubsub.redis
       metadata:
       - name: redisHost
         value: redis-cluster:6379
     ```
   
   - Service Invocation: mTLS enabled
   - Observability: OpenTelemetry automatic

3. Ray 2.9+ (Distributed Compute)
   
   Deployment: KubeRay Operator
   ```yaml
   apiVersion: ray.io/v1
   kind: RayCluster
   metadata:
     name: ray-cluster
   spec:
     headGroupSpec:
       rayStartParams:
         dashboard-host: '0.0.0.0'
       template:
         spec:
           containers:
           - name: ray-head
             image: rayproject/ray:2.9.0
             resources:
               limits:
                 cpu: "2"
                 memory: "4Gi"
     
     workerGroupSpecs:
     - replicas: 3
       minReplicas: 3
       maxReplicas: 10
       rayStartParams: {}
       template:
         spec:
           containers:
           - name: ray-worker
             image: rayproject/ray:2.9.0
             resources:
               limits:
                 cpu: "2"
                 memory: "4Gi"
   ```

4. Qdrant 1.7+ (Vector Database)
   
   Deployment: StatefulSet with Persistent Volumes
   ```yaml
   apiVersion: apps/v1
   kind: StatefulSet
   metadata:
     name: qdrant
   spec:
     serviceName: qdrant
     replicas: 3
     selector:
       matchLabels:
         app: qdrant
     template:
       metadata:
         labels:
           app: qdrant
       spec:
         containers:
         - name: qdrant
           image: qdrant/qdrant:v1.7.0
           ports:
           - containerPort: 6333
           volumeMounts:
           - name: qdrant-storage
             mountPath: /qdrant/storage
     volumeClaimTemplates:
     - metadata:
         name: qdrant-storage
       spec:
         accessModes: ["ReadWriteOnce"]
         resources:
           requests:
             storage: 50Gi
   ```

5. Redis Cluster (State & Caching)
   
   Deployment: Redis Operator or Helm chart
   ```yaml
   # Using Bitnami Redis Cluster Helm chart
   helm install redis bitnami/redis-cluster \
     --set cluster.nodes=6 \
     --set cluster.replicas=1 \
     --set persistence.size=10Gi
   ```

BACKEND ARCHITECTURE:

Project Structure:
```
backend/
├── services/
│   ├── api/                    # FastAPI gateway
│   │   ├── main.py
│   │   ├── routers/
│   │   │   ├── documents.py
│   │   │   ├── queries.py
│   │   │   └── admin.py
│   │   ├── middleware/
│   │   │   ├── auth.py
│   │   │   ├── tracing.py
│   │   │   └── rate_limit.py
│   │   ├── dependencies.py
│   │   └── config.py
│   ├── ingestion/              # Ingestion Agent (Dapr Actor)
│   │   ├── actor.py
│   │   ├── ray_tasks.py
│   │   └── main.py
│   ├── orchestrator/           # Orchestrator Agent (Dapr Workflow)
│   │   ├── workflow.py
│   │   ├── activities.py
│   │   └── main.py
│   ├── retrieval/              # Retrieval Agent (OpenAI + MCP)
│   │   ├── agent.py
│   │   ├── mcp_tools.py
│   │   └── main.py
│   ├── synthesis/              # Synthesis Agent (OpenAI + Streaming)
│   │   ├── agent.py
│   │   ├── prompts.py          # PHR-managed prompts
│   │   └── main.py
│   ├── citation/               # Citation Agent (OpenAI)
│   │   ├── agent.py
│   │   └── main.py
│   └── monitoring/             # Monitoring Agent (Celery)
│       ├── tasks.py
│       └── main.py
├── shared/
│   ├── models/                 # SQLModel database models
│   ├── schemas/                # Pydantic request/response schemas
│   ├── clients/                # Qdrant, Redis, S3 clients
│   └── utils/
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── helm/                       # Helm charts
│   ├── Chart.yaml
│   ├── values.yaml
│   └── templates/
├── docker/
│   └── Dockerfile.service      # Multi-stage Dockerfile
├── .specify/                   # Spec-kit-plus artifacts
│   ├── memory/
│   ├── specs/
│   │   └── 001-agentrag-pro/
│   │       ├── architecture/
│   │       │   ├── adr-001-dapr.md
│   │       │   ├── adr-002-ray.md
│   │       │   └── adr-003-qdrant.md
│   │       └── prompts/
│   │           ├── phr-001-retrieval.md
│   │           ├── phr-002-synthesis.md
│   │           └── phr-003-citation.md
│   └── scripts/
├── requirements.txt
└── README.md
```

1. API Gateway (FastAPI)

Implementation:
```python
# services/api/main.py
from fastapi import FastAPI, Depends
from fastapi.middleware.cors import CORSMiddleware
from opentelemetry.instrumentation.fastapi import FastAPIInstrumentor
import structlog

logger = structlog.get_logger()

app = FastAPI(
    title="AgentRAG Pro API",
    version="1.0.0"
)

# Middleware
app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://app.agentrag.com"],
    allow_methods=["*"],
    allow_headers=["*"],
)

# OpenTelemetry instrumentation
FastAPIInstrumentor.instrument_app(app)

# Routers
app.include_router(documents.router, prefix="/v1/documents", tags=["documents"])
app.include_router(queries.router, prefix="/v1/queries", tags=["queries"])
app.include_router(admin.router, prefix="/v1/admin", tags=["admin"])

@app.get("/health/live")
async def liveness():
    return {"status": "alive"}

@app.get("/health/ready")
async def readiness(db: Database = Depends(get_db)):
    # Check dependencies
    await db.execute("SELECT 1")
    return {"status": "ready"}
```

Kubernetes Deployment:
```yaml
# helm/templates/api-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "api"
        dapr.io/app-port: "8000"
        dapr.io/enable-api-logging: "true"
    spec:
      containers:
      - name: api
        image: agentrag/api:latest
        ports:
        - containerPort: 8000
        env:
        - name: OPENAI_API_KEY
          valueFrom:
            secretKeyRef:
              name: openai-secret
              key: api-key
        resources:
          requests:
            memory: "512Mi"
            cpu: "500m"
          limits:
            memory: "1Gi"
            cpu: "1000m"
        livenessProbe:
          httpGet:
            path: /health/live
            port: 8000
          initialDelaySeconds: 10
          periodSeconds: 30
        readinessProbe:
          httpGet:
            path: /health/ready
            port: 8000
          initialDelaySeconds: 5
          periodSeconds: 10
---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
```

2. Ingestion Agent (Dapr Actor)

Implementation:
```python
# services/ingestion/actor.py
from dapr.actor import Actor, Remindable
from dapr.clients import DaprClient
import ray
import structlog

logger = structlog.get_logger()

class IngestionActor(Actor, Remindable):
    def __init__(self, ctx, actor_id):
        super().__init__(ctx, actor_id)
        self.state_key = f"ingestion_{actor_id}"
    
    async def process_document(self, doc_id: str, tenant_id: str):
        trace_id = f"ing_{doc_id}"
        logger.info("ingestion.started", doc_id=doc_id, trace_id=trace_id)
        
        try:
            # Update state: processing
            await self.state_manager.set_state(
                self.state_key,
                {"status": "processing", "progress": 0}
            )
            await self.state_manager.save_state()
            
            # Step 1: Extract PDF text (Ray)
            pdf_path = await self._get_pdf_path(doc_id)
            text_future = extract_pdf_text.remote(pdf_path)
            text = ray.get(text_future)
            logger.info("ingestion.extracted", doc_id=doc_id, pages=len(text))
            
            await self._update_progress(20)
            
            # Step 2: Semantic chunking (Ray)
            chunk_futures = [
                semantic_chunk.remote(page_text, chunk_size=512, overlap=50)
                for page_text in text
            ]
            all_chunks = ray.get(chunk_futures)
            chunks = [c for page in all_chunks for c in page]  # Flatten
            logger.info("ingestion.chunked", doc_id=doc_id, chunks=len(chunks))
            
            await self._update_progress(50)
            
            # Step 3: Generate embeddings (Ray, batched)
            embedding_futures = [
                generate_embeddings.remote(batch)
                for batch in self._batch(chunks, size=32)
            ]
            all_embeddings = ray.get(embedding_futures)
            embeddings = [e for batch in all_embeddings for e in batch]
            logger.info("ingestion.embedded", doc_id=doc_id, vectors=len(embeddings))
            
            await self._update_progress(80)
            
            # Step 4: Store in Qdrant
            from qdrant_client import QdrantClient
            qdrant = QdrantClient(url="http://qdrant:6333")
            
            points = [
                {
                    "id": f"{doc_id}_{i}",
                    "vector": emb,
                    "payload": {
                        "doc_id": doc_id,
                        "chunk_index": i,
                        "text": chunks[i],
                        "page": i // 10,  # Approximate page
                    }
                }
                for i, emb in enumerate(embeddings)
            ]
            
            qdrant.upsert(
                collection_name=f"tenant_{tenant_id}",
                points=points
            )
            logger.info("ingestion.stored", doc_id=doc_id, points=len(points))
            
            # Update state: completed
            await self.state_manager.set_state(
                self.state_key,
                {"status": "completed", "chunks": len(chunks), "progress": 100}
            )
            await self.state_manager.save_state()
            
            # Publish completion event
            async with DaprClient() as client:
                await client.publish_event(
                    pubsub_name="pubsub",
                    topic_name="document.completed",
                    data={"doc_id": doc_id, "chunks": len(chunks)}
                )
            
            logger.info("ingestion.completed", doc_id=doc_id, trace_id=trace_id)
            
        except Exception as e:
            logger.error("ingestion.failed", doc_id=doc_id, error=str(e))
            await self.state_manager.set_state(
                self.state_key,
                {"status": "failed", "error": str(e)}
            )
            await self.state_manager.save_state()
            raise
    
    async def _update_progress(self, progress: int):
        state = await self.state_manager.try_get_state(self.state_key)
        if state:
            state.data["progress"] = progress
            await self.state_manager.set_state(self.state_key, state.data)
            await self.state_manager.save_state()

# Ray tasks
@ray.remote
def extract_pdf_text(pdf_path: str) -> List[str]:
    import PyPDF2
    with open(pdf_path, 'rb') as f:
        reader = PyPDF2.PdfReader(f)
        return [page.extract_text() for page in reader.pages]

@ray.remote
def semantic_chunk(text: str, chunk_size: int, overlap: int) -> List[str]:
    # Semantic chunking logic
    # For simplicity, just split by tokens
    tokens = text.split()
    chunks = []
    for i in range(0, len(tokens), chunk_size - overlap):
        chunk = " ".join(tokens[i:i + chunk_size])
        chunks.append(chunk)
    return chunks

@ray.remote
def generate_embeddings(texts: List[str]) -> List[List[float]]:
    from openai import OpenAI
    client = OpenAI()
    response = client.embeddings.create(
        model="text-embedding-3-large",
        input=texts
    )
    return [e.embedding for e in response.data]
```

3. Retrieval Agent (OpenAI + MCP)

Implementation:
```python
# services/retrieval/agent.py
from openai import Agent
from mcp import Tool, MCPClient
import structlog

logger = structlog.get_logger()

# Define MCP tools
async def vector_search(query: str, top_k: int = 5, filters: dict = None):
    from qdrant_client import QdrantClient
    from openai import OpenAI
    
    # Get query embedding
    openai_client = OpenAI()
    emb_response = openai_client.embeddings.create(
        model="text-embedding-3-large",
        input=query
    )
    query_vector = emb_response.data[0].embedding
    
    # Search Qdrant
    qdrant = QdrantClient(url="http://qdrant:6333")
    results = qdrant.search(
        collection_name=filters.get("collection", "default"),
        query_vector=query_vector,
        limit=top_k,
        query_filter=filters.get("metadata")
    )
    
    return [
        {
            "text": r.payload["text"],
            "score": r.score,
            "doc_id": r.payload["doc_id"],
            "page": r.payload.get("page", 0)
        }
        for r in results
    ]

vector_search_tool = Tool(
    name="vector_search",
    description="Search documents using semantic similarity",
    function=vector_search,
    parameters={
        "query": {"type": "string", "description": "Search query"},
        "top_k": {"type": "integer", "default": 5},
        "filters": {"type": "object", "default": {}}
    }
)

# Create retrieval agent
retrieval_agent = Agent(
    name="retrieval",
    model="gpt-4-turbo",
    tools=[vector_search_tool],
    instructions="""You are a retrieval agent. Your job is to find the most relevant 
    document chunks for the user's query. Use the vector_search tool to search semantically.
    Return the top 5 most relevant chunks."""
)

async def retrieve_for_query(query: str, tenant_id: str, trace_id: str):
    logger.info("retrieval.started", query=query[:50], trace_id=trace_id)
    
    result = await retrieval_agent.run(
        messages=[{
            "role": "user",
            "content": f"Find relevant information for: {query}"
        }],
        context={"collection": f"tenant_{tenant_id}"}
    )
    
    chunks = result.content  # Parsed from tool calls
    logger.info("retrieval.completed", chunks=len(chunks), trace_id=trace_id)
    
    return chunks
```

(Continuing in next message due to length...)
```

**Research:**
```
Research latest versions and best practices:
1. OpenAI Agents SDK streaming with async generators
2. Dapr actor state consistency guarantees
3. KubeRay operator configuration for auto-scaling
4. Qdrant collection sharding for multi-tenancy
5. OpenTelemetry trace context propagation in Dapr

Update research.md with findings.
```

**Audit:**
```
Audit implementation plan:
1. Check for over-engineering (e.g., do we need Ray for simple tasks?)
2. Verify error handling at every agent boundary
3. Ensure observability (logs, traces, metrics) everywhere
4. Validate resource limits are realistic
5. Confirm ADR/PHR integration points

Update plan.md with improvements.
```

---

### Step 6: Generate Task Breakdown (3 minutes)

```
/tasks
```

**Output:** `.specify/specs/001-agentrag-pro/tasks.md`

**Expected Structure:**
```markdown
## Phase 1: Infrastructure Setup

1. [Sequential] Set up Kubernetes namespace and RBAC
2. [Sequential] Deploy Redis Cluster with Helm
3. [P] Install Dapr with state store configuration
4. [P] Deploy Ray cluster with KubeRay operator
5. [P] Deploy Qdrant StatefulSet with PVCs
6. [Sequential] Configure Ingress and TLS

## Phase 2: Shared Components

7. [Sequential] Create Pydantic schemas in shared/schemas/
8. [Sequential] Create SQLModel models in shared/models/
9. [P] Implement Qdrant client wrapper
10. [P] Implement Redis client wrapper
11. [P] Write unit tests for all schemas and models

## Phase 3: Ingestion Agent

12. [Sequential] Implement IngestionActor with Dapr
13. [P] Create Ray task for PDF extraction
14. [P] Create Ray task for semantic chunking
15. [P] Create Ray task for embedding generation
16. [Sequential] Integrate with Qdrant upsert
17. [P] Write integration tests for ingestion workflow

... (continues for 80+ tasks)
```

---

### Step 7: Validate Cross-Artifact Consistency (2 minutes)

```
/analyze
```

**What It Checks:**
- All user stories in spec have corresponding tasks
- All agents mentioned in plan have implementation tasks
- ADR placeholders exist for key decisions
- PHR placeholders exist for all agent prompts
- No circular dependencies in tasks
- Resource estimates are within constraints

**Expected Output:**
```
✅ All 7 user stories covered in tasks
✅ All 6 agents have implementation tasks
⚠️ ADR-003 (Qdrant vs Pinecone) not yet created
⚠️ PHR-002 (Synthesis prompt) missing performance baseline
✅ Task dependencies validated
✅ Resource limits within constraints
```

---

### Step 8: Implement Everything (15-20 minutes)

```
/implement
```

**What Happens:**
- Agent reads tasks.md
- Executes 80+ tasks in order
- Creates all files and code
- Runs tests as defined
- Deploys to Kubernetes
- Validates deployment

**Monitor Progress:**
```
✓ Task 1/87: Created namespace agentrag-dev
✓ Task 2/87: Deployed Redis Cluster
✓ Task 3/87: Installed Dapr components
✓ Task 4/87: Deployed Ray cluster
✓ Task 5/87: Deployed Qdrant StatefulSet
✓ Task 6/87: Configured Ingress
✓ Task 7/87: Created Pydantic schemas
...
✓ Task 45/87: Implemented Ingestion Actor
✓ Task 46/87: Created Ray tasks
✓ Task 47/87: Integration test passed
...
✓ Task 87/87: Deployment complete!
```

---

### Step 9: Verify Deployment (5 minutes)

```bash
# Check all pods are running
kubectl get pods -n agentrag-dev

# Expected output:
# NAME                           READY   STATUS    RESTARTS   AGE
# api-6d7f4b5c9-abc12            2/2     Running   0          2m
# api-6d7f4b5c9-def34            2/2     Running   0          2m
# api-6d7f4b5c9-ghi56            2/2     Running   0          2m
# ingestion-5c8d9e6f-jkl78       2/2     Running   0          2m
# orchestrator-4b7c8d9e-mno90    2/2     Running   0          2m
# retrieval-3a6b7c8d-pqr12       2/2     Running   0          2m
# synthesis-2z5a6b7c-stu34       2/2     Running   0          2m
# citation-1y4z5a6b-vwx56        2/2     Running   0          2m
# redis-cluster-0                1/1     Running   0          3m
# redis-cluster-1                1/1     Running   0          3m
# qdrant-0                       1/1     Running   0          3m
# ray-head-abc12                 1/1     Running   0          3m
# ray-worker-def34               1/1     Running   0          3m

# Check Dapr sidecars
kubectl get pods -n agentrag-dev -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[*].name}{"\n"}{end}'

# Port-forward API
kubectl port-forward svc/api 8000:8000 -n agentrag-dev

# Test API
curl http://localhost:8000/health/ready
# {"status":"ready"}

# Port-forward Ray dashboard
kubectl port-forward svc/ray-head 8265:8265 -n agentrag-dev
# Open http://localhost:8265

# Check logs
kubectl logs -f deployment/api -c api -n agentrag-dev
```

---

### Step 10: Test Multi-Agent Workflow (5 minutes)

```bash
# Upload a test document
curl -X POST http://localhost:8000/v1/documents \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "filename": "test-manual.pdf",
    "presigned_url": "https://s3.amazonaws.com/bucket/test.pdf"
  }'

# Response:
# {
#   "doc_id": "doc_abc123",
#   "status": "pending",
#   "actor_id": "ingestion_doc_abc123"
# }

# Check processing status
curl http://localhost:8000/v1/documents/doc_abc123
# {
#   "doc_id": "doc_abc123",
#   "status": "processing",
#   "progress": 45,
#   "message": "Generating embeddings: Batch 2/5..."
# }

# Wait for completion (~30-60 seconds)
# Poll until status = "completed"

# Submit a query
curl -X POST http://localhost:8000/v1/queries \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "query": "What are the main safety warnings?"
  }'

# Response (streaming SSE):
# data: {"event":"agent_update","agent":"retrieval","message":"Searching..."}
# data: {"event":"agent_update","agent":"retrieval","message":"Found 5 chunks"}
# data: {"event":"agent_update","agent":"synthesis","message":"Generating answer..."}
# data: {"event":"token","content":"The"}
# data: {"event":"token","content":" main"}
# data: {"event":"token","content":" safety"}
# ...
# data: {"event":"citations","citations":[{"doc":"test-manual.pdf","page":23}]}
# data: [DONE]
```

---

### Step 11: Create ADRs & PHRs (5 minutes)

**ADR Example:**
```bash
# Create ADR for Dapr decision
cat > .specify/specs/001-agentrag-pro/architecture/adr-001-dapr.md << 'EOF'
# ADR-001: Use Dapr for Service Mesh and Actors

## Status
Accepted

## Context
Need service-to-service communication, state management, and actor model for stateful agents in a multi-agent RAG system.

## Decision
Use Dapr instead of Istio, custom gRPC, or direct Redis integration.

## Consequences

### Positive
- Built-in actor model (perfect for Ingestion Agent)
- Automatic state persistence with pluggable backends
- mTLS between services out-of-box
- Pub/sub for event-driven architecture
- OpenTelemetry integration for observability
- Language-agnostic (Python services, future Go/TypeScript)
- Workflows for Orchestrator Agent

### Negative
- Additional sidecar per pod (~50MB memory overhead)
- Dependency on Dapr project maintenance
- Learning curve for team
- Debugging more complex (app + sidecar)

## Alternatives Considered

1. **Istio**
   - Pros: More mature, larger ecosystem
   - Cons: No built-in actors, more complex, higher resource usage
   - Rejected: Actor model critical for our use case

2. **Custom gRPC + etcd**
   - Pros: Full control, minimal dependencies
   - Cons: Lots of custom code, no built-in patterns
   - Rejected: Too much undifferentiated heavy lifting

3. **Direct Redis**
   - Pros: Simple, familiar
   - Cons: No actors, manual state management, no service mesh
   - Rejected: Missing key abstractions we need

## Implementation Notes
- Use Dapr 1.12+ for latest workflow features
- Deploy with Helm in dapr-system namespace
- Configure Redis as state store and pub/sub
- Enable API logging for debugging
- Set resource limits on sidecars: 256Mi memory, 200m CPU

## Monitoring & Success Metrics
- Sidecar overhead: <10% of total resource usage
- State persistence latency: <50ms (p95)
- Zero message loss in pub/sub
- Actor activation time: <100ms

## Review Date
2024-03-01 (3 months after implementation)
EOF
```

**PHR Example:**
```bash
# Create PHR for Synthesis Agent
cat > .specify/specs/001-agentrag-pro/prompts/phr-002-synthesis.md << 'EOF'
# PHR-002: Synthesis Agent System Prompt

## Metadata
- **Agent**: synthesis
- **Model**: gpt-4-turbo
- **Version**: 1.0
- **Date**: 2024-12-03
- **Author**: Irfan (AI Engineer)

## Prompt

```
You are a synthesis agent in a multi-agent RAG system. Your role is to generate comprehensive, accurate answers to user questions based ONLY on the provided document chunks.

Context Chunks (Retrieved by Retrieval Agent):
{chunks}

User Question: {query}

Instructions:
1. Read all context chunks carefully
2. Synthesize information across multiple chunks
3. Answer in 2-3 clear paragraphs
4. Reference specific chunks using [0], [1], [2] notation
5. If the context is insufficient, state: "Based on the available information, I can provide a partial answer..."
6. Do NOT use any external knowledge beyond the provided chunks
7. Maintain a professional, informative tone
8. If chunks contradict, acknowledge both perspectives

Format:
- Start with direct answer
- Provide supporting details in subsequent paragraphs
- End with chunk references

Answer:
```

## Performance Metrics (Production Baseline)
- **Accuracy**: 92% (human eval, n=100)
- **Grounding Rate**: 94% (answers use only provided context)
- **Average Latency**: 2.1s (p50), 3.2s (p95)
- **Average Tokens**: 380 (prompt: 220, completion: 160)
- **User Satisfaction**: 4.4/5 (from feedback surveys)

## Model Configuration
```python
{
    "model": "gpt-4-turbo",
    "temperature": 0.2,
    "max_tokens": 500,
    "top_p": 0.95,
    "frequency_penalty": 0.0,
    "presence_penalty": 0.0,
    "stream": True
}
```

## Known Issues
- Sometimes includes [X] references to non-existent chunk indices (2% of responses)
- Occasionally too verbose for simple questions (10% of responses)
- Struggles with highly technical jargon (5% accuracy drop)

## Changes from Previous Version
N/A - This is the initial version

## A/B Testing History
Not yet tested (baseline version)

## Next Optimization Ideas
1. Test temperature=0.1 for more deterministic outputs
2. Add explicit constraint: "Use exactly 2 paragraphs"
3. Experiment with Chain-of-Thought: "First, identify key points in each chunk..."
4. Test with Claude Sonnet for cost comparison

## Review Schedule
- Weekly: Monitor metrics dashboard
- Monthly: Review accuracy and user satisfaction
- Quarterly: Major prompt redesign if needed
EOF
```

---

## 🎓 What You've Built

### Production-Ready Multi-Agent System
- ✅ 6 specialized agents with clear responsibilities
- ✅ Distributed compute with Ray for scalability
- ✅ Stateful agents with Dapr actors
- ✅ OpenAI Agents SDK with MCP tools
- ✅ Kubernetes deployment with auto-scaling
- ✅ Real-time streaming responses
- ✅ Comprehensive observability
- ✅ ADR and PHR tracking

### Key Achievements
1. **Spec-driven Vibe-coding**: Fast iteration with structure
2. **Production infrastructure**: Kubernetes, Dapr, Ray
3. **Multi-agent coordination**: A2A messaging, workflows
4. **Traceability**: ADRs and PHRs as first-class artifacts
5. **Enterprise-ready**: Multi-tenancy, monitoring, scaling

---

## 🚀 Next Steps

### Extend Your System
1. **Add more document types**: DOCX, TXT, Markdown
2. **Implement user feedback loop**: Thumbs up/down on answers
3. **Add more agents**: Summary Agent, Translation Agent
4. **Optimize costs**: Model routing based on query complexity
5. **Scale globally**: Multi-region Kubernetes clusters

### Learn More Patterns
```bash
# Try different agent frameworks
sp init langgraph-variant --ai claude
# Specify with LangGraph state machines

sp init crewai-variant --ai claude
# Specify with CrewAI collaborative agents

sp init n8n-integration --ai claude
# Specify with n8n workflow orchestration
```

### Production Checklist
- [ ] Set up monitoring alerts (PagerDuty/Slack)
- [ ] Configure backup strategy (Qdrant, PostgreSQL)
- [ ] Implement disaster recovery plan
- [ ] Load test with 500 concurrent users
- [ ] Security audit (penetration testing)
- [ ] Cost optimization review
- [ ] Documentation for ops team
- [ ] Runbook for common issues

---

## 📚 Resources

- [spec-kit-plus Documentation](https://github.com/panaversity/spec-kit-plus)
- [OpenAI Agents SDK Guide](https://platform.openai.com/docs/agents)
- [Dapr Documentation](https://docs.dapr.io/)
- [Ray Documentation](https://docs.ray.io/)
- [Qdrant Documentation](https://qdrant.tech/documentation/)
- [Kubernetes Best Practices](https://kubernetes.io/docs/concepts/configuration/overview/)

---

**Congratulations!** 🎉 You've built a production-grade multi-agent RAG system using Spec-driven Vibe-coding. You've experienced how spec-kit-plus combines rapid AI-assisted development with the structure needed for enterprise systems.

**Key Takeaway:** *Fast iteration + Production structure = spec-kit-plus* 🚀
