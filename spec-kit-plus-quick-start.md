# Spec-Kit-Plus Quick Start Cheat Sheet

## 🚀 Installation (Choose One Method)

### Method 1: PyPI (Recommended - Easiest)
```bash
# Install from PyPI
pip install specifyplus

# Verify installation
sp check
```

### Method 2: UV Tools (Recommended - Best Management)
```bash
# Install with uv tools
uv tool install specifyplus

# Verify
sp check

# Manage tools
uv tool list
uv tool upgrade specifyplus
uv tool uninstall specifyplus
```

### Method 3: Direct Execution (No Installation)
```bash
# Run without installing
uvx specifyplus init my-project
# or shorter
uvx sp init my-project
```

---

## 📝 Starting a New Project

### Basic Initialization
```bash
# Create new project
sp init my-ai-project

# With specific AI agent (Claude Code recommended)
sp init my-ai-project --ai claude

# Alternative agents
sp init my-ai-project --ai cursor        # Cursor
sp init my-ai-project --ai windsurf      # Windsurf
sp init my-ai-project --ai gemini        # Gemini CLI
sp init my-ai-project --ai qwen          # Qwen
sp init my-ai-project --ai opencode      # opencode
```

### Initialize in Existing Directory
```bash
# In current directory
sp init . --ai claude

# Or use --here flag
sp init --here --ai claude

# Force merge (skip confirmation)
sp init . --force --ai claude
```

### Special Options
```bash
# Skip git initialization
sp init my-project --ai claude --no-git

# Debug mode (troubleshooting)
sp init my-project --ai claude --debug

# Corporate environment (with GitHub token)
sp init my-project --ai claude --github-token ghp_your_token

# PowerShell scripts (Windows)
sp init my-project --ai claude --script ps
```

---

## 🎯 The 7-Step Workflow (In Your AI Agent)

After running `claude` or your chosen agent:

### 1. Constitution (Define Governance)
```
/constitution Create principles for [your project type]:
- Architecture patterns (Kubernetes, Dapr, Ray)
- Agent design standards
- Code quality requirements
- Testing strategy
- Deployment practices
- Security policies
```

**Example for Production AI:**
```
/constitution Create governance for production multi-agent system:
- Cloud-Native: Kubernetes orchestration, Dapr actors
- Agent Standards: OpenAI Agents SDK, MCP tools, A2A messaging
- Code Quality: Python type hints, Pydantic schemas, 85% coverage
- AI/LLM: Streaming responses, token tracking, retry logic
- Deployment: Docker containers, Helm charts, HPA scaling
- Observability: OpenTelemetry, structured logging
```

### 2. Specify (Define Requirements)
```
/specify [Describe WHAT you want to build and WHY]
```

**Example:**
```
/specify Build a production multi-agent RAG system:
- Ingestion Agent: Processes documents with Ray workers
- Orchestrator Agent: Coordinates query workflow with Dapr
- Retrieval Agent: Semantic search with MCP tools
- Synthesis Agent: Generates answers with streaming
- Citation Agent: Extracts and formats sources
- Deploy on Kubernetes with horizontal scaling
- Support 500 concurrent users, <3s response time
```

### 3. Clarify (Fill Gaps)
```
/clarify
```

Agent asks questions. Answer them, then validate:
```
Read the review and acceptance checklist, and check off each item if the feature spec meets the criteria.
```

### 4. Plan (Technical Architecture)
```
/plan [Your tech stack and cloud-native architecture]
```

**Example:**
```
/plan Production architecture:

INFRASTRUCTURE:
- Kubernetes 1.28+ with Helm charts
- Dapr 1.12+ for actors and pub/sub
- Ray 2.9+ with KubeRay for distributed compute
- Qdrant for vector database
- Redis cluster for state and caching

BACKEND:
- FastAPI with async/await
- OpenAI Agents SDK with MCP tools
- Pydantic v2 for all schemas
- SQLModel for database models
- Celery for background tasks

FRONTEND:
- Next.js 14 App Router
- shadcn/ui components
- TanStack Query for data fetching
- SSE for streaming responses

DEPLOYMENT:
- Docker multi-stage builds
- GitHub Actions CI/CD
- Horizontal Pod Autoscaling
- OpenTelemetry for observability
```

Then research and audit:
```
Research latest best practices for:
- OpenAI Agents SDK streaming patterns
- Dapr actor state management
- KubeRay auto-scaling configuration
- MCP tools implementation

Then audit the plan for over-engineering and gaps.
```

### 5. Tasks (Break Down Work)
```
/tasks
```

Generates ordered, executable tasks in `tasks.md`

### 6. Analyze (Validate Consistency)
```
/analyze
```

Cross-checks specs, plan, tasks for consistency and coverage

### 7. Implement (Execute)
```
/implement
```

Agent executes all tasks systematically

---

## 🔧 Common Starting Patterns

### For OpenAI Agents SDK + MCP
```bash
sp init openai-mcp-system --ai claude

# In agent:
/constitution OpenAI Agents SDK with MCP principles:
- Use Assistant API with streaming
- MCP tools for external integrations
- Structured outputs with Pydantic
- A2A messaging between agents
- Deploy with Kubernetes + Dapr

/specify Build customer support multi-agent system using OpenAI Agents SDK...
```

### For Distributed Multi-Agent (Dapr + Ray)
```bash
sp init distributed-agents --ai claude

# In agent:
/constitution Distributed multi-agent principles:
- Dapr actors for stateful agents
- Ray for compute-intensive tasks
- Kubernetes for orchestration
- OpenTelemetry for tracing
- Redis for state management

/specify Build research automation with distributed agents...
```

### For Enterprise RAG System
```bash
sp init enterprise-rag --ai claude

# In agent:
/constitution Enterprise RAG principles:
- Multi-tenant isolation
- Vector database with sharding
- Agent coordination with Dapr workflows
- Kubernetes HPA for scaling
- ADR tracking for architecture
- PHR tracking for prompts

/specify Build enterprise RAG platform with...
```

---

## 🛠️ Maintenance Commands

### Upgrade spec-kit-plus
```bash
pip install -U specifyplus
# or
uv tool upgrade specifyplus
```

### Check System Setup
```bash
sp check
```

### List Installed Tools (UV)
```bash
uv tool list
```

### Uninstall
```bash
pip uninstall specifyplus
# or
uv tool uninstall specifyplus
```

---

## 🐛 Quick Troubleshooting

### Commands Not Showing in Agent
```bash
# Check if config exists
ls -la .specify/

# Re-initialize with force
sp init . --force --ai claude

# Verify setup
sp check
```

### For Non-Git Projects
```bash
# Set feature name before planning
export SPECIFY_FEATURE=001-my-feature

# Then use normal workflow
/plan
/tasks
/implement
```

### Kubernetes Pod Issues
```bash
# Check pod status
kubectl get pods

# View logs
kubectl logs <pod-name>

# Describe pod for events
kubectl describe pod <pod-name>

# Check Dapr sidecar
kubectl logs <pod-name> -c daprd
```

### Dapr Actor Issues
```bash
# Check Dapr components
kubectl get components

# Test actor invocation
dapr invoke --app-id my-agent --method process

# View actor state
kubectl exec -it redis-0 -- redis-cli
> KEYS *
```

### Ray Cluster Issues
```bash
# Check Ray status
ray status

# Check workers
kubectl get pods -l ray.io/node-type=worker

# View Ray dashboard
kubectl port-forward svc/ray-head 8265:8265
# Open http://localhost:8265
```

---

## 📚 All Available Slash Commands

| Command | Purpose |
|---------|---------|
| `/constitution` | Define project governance |
| `/specify` | Create requirements |
| `/clarify` | Structured Q&A (MUST run before /plan) |
| `/plan` | Technical architecture |
| `/tasks` | Generate task breakdown |
| `/analyze` | Cross-artifact validation |
| `/implement` | Execute implementation |

---

## 🎯 Your First 20 Minutes

```bash
# 1. Install (2 min)
pip install specifyplus

# 2. Create project (1 min)
sp init my-first-agent-system --ai claude
cd my-first-agent-system

# 3. Launch agent (1 min)
claude

# 4. Define governance (3 min)
/constitution Create principles for cloud-native multi-agent system with OpenAI Agents SDK

# 5. Specify what to build (4 min)
/specify Build a document Q&A system with:
- Ingestion agent for processing PDFs
- Retrieval agent with semantic search
- Synthesis agent with streaming responses
- Deploy on Kubernetes with Dapr
- Support 100 concurrent users

# 6. Clarify (3 min)
/clarify
[Answer questions]

# 7. Plan (3 min)
/plan Use FastAPI, OpenAI Agents SDK, Dapr actors, Ray for processing, Kubernetes deployment

# 8. Generate tasks (1 min)
/tasks

# 9. Validate (1 min)
/analyze

# 10. Implement (Start)
/implement
```

**You're now doing Spec-driven Vibe-coding!** 🎉

---

## 💡 Pro Tips

1. **Always run /clarify**: It's REQUIRED before /plan (catches ambiguities)
2. **Use /analyze**: Validates consistency before implementation
3. **Track decisions**: Create ADRs for architecture, PHRs for prompts
4. **Research in /plan**: For rapidly changing tech (AI SDKs, K8s patterns)
5. **Start with constitution**: Good governance = consistent implementation
6. **Monitor with OpenTelemetry**: Built-in tracing for multi-agent systems

---

## 🆚 Key Differences from Original spec-kit

| Feature | spec-kit | spec-kit-plus |
|---------|----------|---------------|
| **Install** | `uv tool install ... from git` | `pip install specifyplus` |
| **Command** | `specify` | `sp` or `specifyplus` |
| **Slash** | `/speckit.constitution` | `/constitution` |
| **Focus** | General apps | Production multi-agent AI |
| **Stack** | Framework-agnostic | K8s, Dapr, Ray, OpenAI SDK |
| **Tracking** | Basic | ADR + PHR first-class |
| **Sub-agents** | No | Spec Architect, PHR Curator |
| **Philosophy** | Spec-Driven | Spec-driven Vibe-coding |

---

## 🌟 Production-Ready Features

### ADR Tracking (Architecture Decision Records)
```markdown
# ADR-001: Use Dapr for Service Mesh
Status: Accepted
Context: Need service coordination for multi-agent system
Decision: Use Dapr instead of Istio
Consequences: Simpler state management, built-in actors
```

### PHR Tracking (Prompt History Records)
```markdown
# PHR-001: Synthesis Agent Prompt
Version: 2.0
Performance: 94% accuracy, 1.8s latency
Changes: Added context constraint, reduced temperature
```

### Sub-Agents
- **Spec Architect**: Reviews and improves specifications
- **PHR/ADR Curator**: Manages prompt and architecture records

---

## 🚀 Production Stack Patterns

### OpenAI Agents SDK + MCP
```python
from openai import Agent
from mcp import Tool

agent = Agent(
    name="retrieval",
    tools=[Tool(name="search", function=search_func)],
    model="gpt-4-turbo"
)
```

### Dapr Actors
```python
from dapr.actor import Actor

class IngestionActor(Actor):
    async def process_document(self, doc_id: str):
        # State auto-managed by Dapr
        state = await self.state_manager.get_state("status")
```

### Ray Distributed Compute
```python
import ray

@ray.remote
def process_pdf(pdf_path: str):
    return extracted_text

results = ray.get([process_pdf.remote(p) for p in pdfs])
```

### Kubernetes Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: agent
spec:
  template:
    metadata:
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "agent"
```

---

## 🔗 Resources

- [spec-kit-plus Repo](https://github.com/panaversity/spec-kit-plus)
- [Original spec-kit](https://github.com/github/spec-kit)
- [OpenAI Agents SDK](https://platform.openai.com/docs/agents)
- [Dapr Docs](https://docs.dapr.io/)
- [Ray Docs](https://docs.ray.io/)
- [MCP Protocol](https://modelcontextprotocol.io/)

---

## ✅ Ready to Build?

**Three-step start:**
1. `pip install specifyplus`
2. `sp init my-project --ai claude`
3. Follow the 7-step workflow

**For production multi-agent AI systems, spec-kit-plus is your fastest path from idea to deployment.** 🚀

---

**Key Insight:** *spec-kit-plus = Conversational Speed + Production Structure*
