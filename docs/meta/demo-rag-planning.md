# Demo RAG Planning

## Overview

The Field Manual requires a sanitized RAG corpus for public demos, workshops, and module content. This separates the private "brain RAG" (actual lab infrastructure) from a safe "demo RAG" that can be shown publicly without exposing personal or infrastructure details.

## The Two-RAG Model

### RAG #1 - Brain RAG (Private)
- Location: `mlsecops-lab/docs/`
- Corpus: `lab_docs`
- Contains: Real infrastructure, network details, career planning, personal notes
- Use: Personal assistant, internal queries only
- Never exposed publicly

### RAG #2 - Demo RAG (Public-Safe)
- Location: `mlsecops-lab/projects/mlsecops-rag/data/demo_rag/`
- Corpus: `demo_rag`
- Contains: Sanitized, synthetic content that mirrors real scenarios
- Use: Workshops, conference talks, OBS recordings, Field Manual modules
- Safe to show on screen, publish, or let others query

## What Goes in Demo RAG

Sanitized equivalents of real lab content:
- Generic RAG architecture (no real IPs, VLANs, hostnames)
- Fictional company/lab scenarios
- Security attack walkthroughs with synthetic data
- Agent misuse demonstrations
- Retrieval poisoning examples

What is explicitly excluded:
- Real IP addresses or network topology
- Actual hardware inventory
- Personal career/planning documents
- Any "this is literally my home network" detail

## Relationship to Field Manual Modules

Each Field Manual module that involves RAG or agent demos should have corresponding content in `demo_rag`:

| Module | Demo RAG Content |
|--------|------------------|
| Module 1 - Evasion Attack | `evasion-attack-scenario.md` |
| Module 3 - LLM Jailbreak | `jailbreak-demo-setup.md` |
| Module 4 - Retrieval Poisoning | `retrieval-poisoning-walkthrough.md` |
| Module 6 - Agent Tool Misconfig | `agent-misuse-scenario.md` |

Content is created as modules are developed, not in advance.

## Implementation

### corpora.yaml addition

```yaml
demo_rag:
  source_type: demo_doc
  scope: demo
  priority: demo_safe
  domains: [rag, agents, threats]
  path: projects/mlsecops-rag/data/demo_rag
```

### Ingestion

```bash
python docs/ingestion/indexer.py --corpus demo_rag
```

### Query isolation

Demo mode should only query `demo_rag` collection:
- Environment variable: `RAG_CORPUS=demo_rag`
- Or CLI flag: `rag-assistant --corpus demo_rag`

## When to Build This

Build `demo_rag` when:
1. Module 1 content is ready and needs a backing RAG
2. A workshop or talk is scheduled that requires live demo
3. Any public recording will show RAG queries

Do not build prematurely - create demo content alongside the module that needs it.

## Cross-Reference

The mlsecops-lab repo should reference this document when demo corpus work begins. A brief note in `docs/architecture/` or `AGENTS.md` pointing here is sufficient.

---

**Status:** Planning  
**Created:** 2025-11-21  
**Implements when:** First Field Manual module requiring RAG demo
