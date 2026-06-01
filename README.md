# DreamCloud V16 — Advanced Cognitive Memory Subsystem
## Version: 16 (Typed Graph Edges + Belief Systems + Advanced Activation Decay + Retrospective Importance Backpropagation) Date: 1 June 2026

# Abstract
DreamCloud implements a hybrid neuro-symbolic persistent memory system for autonomous cognitive agents. It combines:
	•	Dense vector embeddings for semantic similarity retrieval
	•	Typed directed/undirected knowledge graph for relational and causal reasoning
	•	Dynamic activation landscapes with multiple decay regimes
	•	Autonomous DreamCycle for consolidation and concept synthesis
	•	Belief system with NLI-based contradiction detection and reinforcement
	•	Retrospective importance backpropagation across graph edges
This emulates mammalian memory consolidation while remaining efficient on standard hardware.

# Core Architecture
High-Level Components
graph TD
    A[DreamCloudEngine] --> B[Embedder]
    A --> C[FaissIndex]
    A --> D[MemoryPipeline]
    A --> E[MemoryGraph]
    A --> F[BeliefSystem]
    A --> G[DreamCycle]
    A --> H[ActivationDecayManager]
    A --> I[Telemetry Suite]
    A --> J[API Layer]
## Key Modules:
	•	Embedder: Sentence-Transformers all-MiniLM-L6-v2 (384 dims)
	•	Vector Store: FAISS (IVF/HNSW)
	•	Graph: NetworkX with typed edges + causal overlay
	•	BeliefSystem: NLI cross-encoder for 3-class classification
## Memory Schema (`core/memory/schema.py`)
Each memory follows this structure:
class Memory(BaseModel):
    id: UUID
    content: str
    type: MemoryType  # episodic, semantic, procedural...
    embedding: Optional[np.ndarray]
    importance: float = Field(..., ge=0.0, le=5.0)
    reliability: float = Field(..., ge=0.0, le=1.0)
    activation: float
    timestamp: datetime
    last_activated: datetime
    reinforcement_count: int = 0
    contradiction_count: int = 0
    entailment_count: int = 0
    metadata: Dict[str, Any]
    schema_version: int = 8

# Activation Decay Mechanisms
Mathematical Foundations
	1	Global Sweep Decay: [ a_{t+1} = a_t \times \gamma \quad (\gamma = 0.985) ]
	2	Lazy On-Access Decay: [ a(t) = a_0 \exp(-\lambda \Delta t) \quad (\lambda = 0.0015) ]
	3	Hybrid (Recommended): Combines lazy computation with periodic sweeps and anomaly detection (runaway reinforcement >25, activation >8, monopolization >65%).
Configuration: Highly tunable in config/memory_config.py.

# Typed Graph & Backpropagation (V15/V16)
Supported Edge Types (enforced by schema):
	•	semantic, causal, temporal, derived_from, supports, contradicts, goal_dependency
Backpropagation Weights:
BACKPROP_EDGE_WEIGHTS = {
    "causal": 1.00,
    "goal_dependency": 0.90,
    "derived_from": 0.85,
    "temporal": 0.70,
    "supports": 0.65,
    "semantic": 0.50,
    "contradicts": 0.20
}
Algorithm (depth-limited with decay factor 0.6 per hop).

# Belief System (V16)
	•	Detection Pipeline: Heuristic pre-filter → NLI Cross-Encoder (contradiction/entailment/neutral)
	•	Resolution:
	◦	Contradiction → penalize weaker memory + contradicts edge
	◦	Entailment → reinforce both + supports edge
	◦	Neutral → peaceful coexistence
	•	Queue for external arbitration.

# DreamCycle
Periodic background process (~60s interval):
	•	Embedding + graph connectivity clustering
	•	Higher-order concept synthesis
	•	Weak memory consolidation
	•	Low-importance pruning

# Installation & Usage
git clone https://github.com/Timoune/DreamCloud.git
cd DreamCloud
pip install -r requirements.txt
python main.py
API Server:
uvicorn api.server:app --reload --port 8000
Core Endpoints:
	•	POST /store — ingest new memory
	•	POST /retrieve — hybrid semantic + graph query
	•	GET /health — system status

# Configuration
Edit files in config/:
	•	memory_config.py — cognitive parameters
	•	model_config.py — embedding & NLI models
Scientist Tuning Recommendations:
	•	Increase LAZY_DECAY_LAMBDA for short-term focus
	•	Lower CONCEPT_SIMILARITY_THRESHOLD for creative synthesis
	•	Enable full telemetry for empirical studies

# Testing & Validation
	•	Unit tests in tests/
	•	Interactive mode for qualitative assessment
	•	Telemetry metrics: entropy, activation histograms, graph density

# License: See LICENSE file (proprietary, personal/non-commercial use).