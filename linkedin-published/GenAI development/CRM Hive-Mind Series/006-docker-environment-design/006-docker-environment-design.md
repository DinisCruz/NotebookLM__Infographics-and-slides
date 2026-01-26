# Docker Environment Design: Safe & Ephemeral AI Development

**Date:** January 26, 2026
**Document Type:** Infrastructure & Architecture Analysis
**Purpose:** Document the Docker environment design for Claude-Flow experimentation and validate containerized AI orchestration for production deployments

---

## Introduction

This document describes the Docker environment that was purpose-built for experimenting with Claude-Flow in a **safe, isolated, and ephemeral** manner. Beyond experimentation, this setup serves as a **proof-of-concept** for deploying Claude and Claude-Flow in production containerized environments such as:

- Kubernetes (K8s) clusters
- Cloud compute instances (AWS EC2, GCP Compute, Azure VMs)
- Container orchestration platforms (ECS, Cloud Run, etc.)
- CI/CD pipelines for AI-assisted development

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│                    WHY CONTAINERIZE AI DEVELOPMENT?                         │
│                                                                             │
│     ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐    │
│     │   ISOLATION     │     │ REPRODUCIBILITY │     │   EPHEMERALITY  │    │
│     │                 │     │                 │     │                 │    │
│     │ AI agents can't │     │ Same results    │     │ Spin up, use,   │    │
│     │ affect your     │     │ every time,     │     │ tear down—no    │    │
│     │ host system     │     │ any machine     │     │ residue left    │    │
│     └─────────────────┘     └─────────────────┘     └─────────────────┘    │
│                                                                             │
│     "What happens in the container, stays in the container."               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Design Philosophy

### Problem Statement

When experimenting with AI agent orchestration systems like Claude-Flow, several concerns arise:

| Concern | Risk |
|---------|------|
| **System Modification** | AI agents might modify system files, install packages, or change configurations |
| **Resource Consumption** | Parallel agents could consume excessive CPU/memory |
| **State Pollution** | Experiments leave behind files, databases, caches |
| **Reproducibility** | "It works on my machine" syndrome |
| **Security** | Running AI with system access poses risks |

### Solution: Containerized Sandbox

The Docker environment addresses all these concerns:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         CONTAINERIZED SANDBOX                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  HOST MACHINE (Protected)                                                   │
│  ════════════════════════                                                   │
│  │                                                                          │
│  │  Only these folders are shared:                                          │
│  │  ├── ./workspace/  ←──── Your project files (you control what's here)   │
│  │  └── ./data/       ←──── Persistent state (optional, deletable)         │
│  │                                                                          │
│  │  Everything else is ISOLATED                                             │
│  │                                                                          │
│  └──────────────────────────────────────────────────────────────────────    │
│                                                                             │
│  DOCKER CONTAINER (Sandboxed)                                               │
│  ════════════════════════════                                               │
│  │                                                                          │
│  │  ┌─────────────────────────────────────────────────────────────────┐    │
│  │  │  Claude-Flow + Claude Code + Node.js                            │    │
│  │  │                                                                 │    │
│  │  │  • Can only see /home/claude/workspace                          │    │
│  │  │  • Runs as non-root user 'claude'                               │    │
│  │  │  • No access to host network (except mapped ports)              │    │
│  │  │  • No access to host filesystem                                 │    │
│  │  │  • Resource limits can be applied                               │    │
│  │  └─────────────────────────────────────────────────────────────────┘    │
│  │                                                                          │
│  └──────────────────────────────────────────────────────────────────────    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## The Implementation

### Dockerfile Analysis

```dockerfile
FROM node:20-slim

LABEL maintainer="Dinis Cruz"
LABEL description="Claude Flow 2.x execution environment (non-root, runnable)"
LABEL version="1.0"

# System deps - minimal footprint
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \          # Version control
    curl \         # HTTP requests
    bash \         # Shell
    ca-certificates \  # HTTPS
    procps \       # Process management (ps, top)
    tmux \         # Terminal multiplexer
    python3 \      # Python runtime
    make \         # Build tools
    g++ \          # C++ compiler (for native modules)
    && rm -rf /var/lib/apt/lists/*

# SECURITY: Create non-root user
RUN useradd -m -s /bin/bash claude

# Switch to non-root user
USER claude
WORKDIR /home/claude/workspace

# Install Claude Code and Claude Flow
RUN npm install @anthropic-ai/claude-code
RUN npm install claude-flow@alpha

# Environment
ENV NODE_ENV=production
ENV PATH="/home/claude/.npm-global/bin:${PATH}"

# Expose UI ports
EXPOSE 3000

# Entry point: interactive shell
CMD ["/bin/bash"]
```

**Key Design Decisions:**

| Decision | Rationale |
|----------|-----------|
| `node:20-slim` base | Minimal image size (~200MB), LTS stability |
| Non-root user `claude` | Security: limits what the container can do |
| `--no-install-recommends` | Minimal attack surface, smaller image |
| `rm -rf /var/lib/apt/lists/*` | Reduce image size, no package cache |
| `npm install` without `-g` | User-local packages, no root needed |
| `CMD ["/bin/bash"]` | Interactive by default, flexible entry |

### docker-compose.yml Analysis

```yaml
services:
  claude-flow:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: claude-flow

    # Interactive mode - essential for Claude Code
    stdin_open: true
    tty: true

    # Port mappings
    ports:
      - "3000:3000"   # MCP Server / Web UI
      - "3008:3008"   # MCP WebSocket

    # Volume mounts - CRITICAL for non-root user
    volumes:
      # Project workspace (your code)
      - ./workspace:/home/claude/workspace

      # Persistent state (optional)
      - ./data/.swarm:/home/claude/.swarm
      - ./data/.claude-flow:/home/claude/.claude-flow

    # Environment configuration
    environment:
      NODE_ENV: production
      CLAUDE_FLOW_MAX_AGENTS: 12
      CLAUDE_FLOW_MEMORY_SIZE: 2GB

    # No auto-restart during development
    restart: "no"

    # Isolated network
    networks:
      - claude-flow-network

networks:
  claude-flow-network:
    driver: bridge
```

**Key Design Decisions:**

| Decision | Rationale |
|----------|-----------|
| `stdin_open: true` + `tty: true` | Required for interactive Claude Code sessions |
| Volume paths to `/home/claude/` | Match non-root user home directory |
| Separate `./data/` volume | State persists even if workspace is replaced |
| `restart: "no"` | Prevent infinite restart loops during debugging |
| Bridge network | Isolated network namespace |
| `CLAUDE_FLOW_MAX_AGENTS: 12` | Reasonable limit for parallel agents |

---

## Volume Mount Strategy

The volume mount design enables both **ephemerality** and **persistence** as needed:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         VOLUME MOUNT STRATEGY                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  HOST                              CONTAINER                                │
│  ════                              ═════════                                │
│                                                                             │
│  ./workspace/ ──────────────────▶ /home/claude/workspace/                   │
│  │                                │                                         │
│  │  Your project files            │  Where Claude-Flow operates             │
│  │  • Source code                 │  • Reads your code                      │
│  │  • Documentation               │  • Writes generated files               │
│  │  • Tests                       │  • Runs builds/tests                    │
│  │                                │                                         │
│  │  EPHEMERAL: Delete workspace   │                                         │
│  │  to start fresh                │                                         │
│  │                                │                                         │
│  ├────────────────────────────────┼─────────────────────────────────────    │
│                                                                             │
│  ./data/.swarm/ ────────────────▶ /home/claude/.swarm/                      │
│  │                                │                                         │
│  │  Swarm memory database         │  Agent coordination state               │
│  │  • SQLite files                │  • Shared context                       │
│  │  • Pattern storage             │  • Learned behaviors                    │
│  │                                │                                         │
│  │  PERSISTENT: Survives          │                                         │
│  │  container restarts            │                                         │
│  │                                │                                         │
│  ├────────────────────────────────┼─────────────────────────────────────    │
│                                                                             │
│  ./data/.claude-flow/ ──────────▶ /home/claude/.claude-flow/                │
│  │                                │                                         │
│  │  Claude-Flow configuration     │  Session state, config                  │
│  │  • Session history             │  • Resume capability                    │
│  │  • User preferences            │  • Hive-mind state                      │
│  │                                │                                         │
│  │  PERSISTENT: Enables           │                                         │
│  │  session resume                │                                         │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Ephemeral vs Persistent Trade-offs

| Scenario | Action | Result |
|----------|--------|--------|
| **Fresh start** | `rm -rf ./workspace/* ./data/*` | Complete reset |
| **New project, keep learnings** | `rm -rf ./workspace/*` | Memory preserved |
| **Keep code, reset AI state** | `rm -rf ./data/*` | Clean AI slate |
| **Full persistence** | Keep everything | Resume exactly where you left off |

---

## Security Considerations

### Non-Root User

The container runs as user `claude` (UID 1000), not root:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         SECURITY MODEL                                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  TRADITIONAL (Root)                  THIS DESIGN (Non-Root)                 │
│  ══════════════════                  ══════════════════════                 │
│                                                                             │
│  Container runs as root              Container runs as 'claude'             │
│       │                                   │                                 │
│       ▼                                   ▼                                 │
│  ┌─────────────────┐               ┌─────────────────┐                     │
│  │ Can modify any  │               │ Can only modify │                     │
│  │ file in container│               │ /home/claude/   │                     │
│  │                 │               │                 │                     │
│  │ If container    │               │ If container    │                     │
│  │ escape occurs,  │               │ escape occurs,  │                     │
│  │ attacker has    │               │ attacker has    │                     │
│  │ ROOT on host    │               │ limited user    │                     │
│  └─────────────────┘               └─────────────────┘                     │
│                                                                             │
│  Risk: HIGH                         Risk: LOW                               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Network Isolation

The bridge network provides isolation:

- Container cannot access host network directly
- Only explicitly mapped ports (3000, 3008) are exposed
- Inter-container communication only within the network
- No access to other Docker networks

### Resource Limits (Optional Enhancement)

For production, add resource limits:

```yaml
services:
  claude-flow:
    # ... existing config ...
    deploy:
      resources:
        limits:
          cpus: '4'
          memory: 8G
        reservations:
          cpus: '2'
          memory: 4G
```

---

## Validation for Production Environments

This Docker setup validates that Claude-Flow can run effectively in:

### Kubernetes (K8s)

```yaml
# Example K8s deployment (derived from this Docker setup)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: claude-flow
spec:
  replicas: 1
  selector:
    matchLabels:
      app: claude-flow
  template:
    metadata:
      labels:
        app: claude-flow
    spec:
      securityContext:
        runAsUser: 1000        # Non-root (claude user)
        runAsGroup: 1000
        fsGroup: 1000
      containers:
      - name: claude-flow
        image: claude-flow:latest
        stdin: true
        tty: true
        ports:
        - containerPort: 3000
        - containerPort: 3008
        env:
        - name: CLAUDE_FLOW_MAX_AGENTS
          value: "12"
        - name: ANTHROPIC_API_KEY
          valueFrom:
            secretKeyRef:
              name: claude-secrets
              key: api-key
        volumeMounts:
        - name: workspace
          mountPath: /home/claude/workspace
        - name: swarm-data
          mountPath: /home/claude/.swarm
        resources:
          limits:
            cpu: "4"
            memory: "8Gi"
          requests:
            cpu: "2"
            memory: "4Gi"
      volumes:
      - name: workspace
        persistentVolumeClaim:
          claimName: claude-workspace-pvc
      - name: swarm-data
        persistentVolumeClaim:
          claimName: claude-swarm-pvc
```

### Cloud Compute Instances

The Docker setup can be deployed to:

| Platform | Approach |
|----------|----------|
| **AWS EC2** | Install Docker, `docker-compose up -d` |
| **GCP Compute** | Container-Optimized OS, deploy directly |
| **Azure VMs** | Docker extension, compose deployment |
| **DigitalOcean** | Docker droplet, one-click deploy |

### CI/CD Pipeline Integration

```yaml
# Example GitHub Actions workflow
name: AI-Assisted Development

on:
  workflow_dispatch:
    inputs:
      task:
        description: 'Task for Claude-Flow'
        required: true

jobs:
  claude-flow:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build Claude-Flow container
        run: docker build -t claude-flow .

      - name: Run Claude-Flow task
        run: |
          docker run --rm \
            -e ANTHROPIC_API_KEY=${{ secrets.ANTHROPIC_API_KEY }} \
            -v ${{ github.workspace }}:/home/claude/workspace \
            claude-flow \
            npx claude-flow@alpha hive-mind spawn "${{ inputs.task }}" --claude --non-interactive

      - name: Commit generated code
        run: |
          git config user.name "Claude-Flow Bot"
          git add .
          git commit -m "AI-generated: ${{ inputs.task }}" || true
          git push
```

---

## Benefits Demonstrated

The CRM project validated several key benefits:

### 1. Safe Experimentation

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│  8 AI agents spawned and working in parallel                                │
│  19 files created                                                           │
│  Multiple npm packages installed                                            │
│  Server processes started                                                   │
│                                                                             │
│  Host machine impact: ZERO                                                  │
│  (Everything contained in Docker)                                           │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2. Reproducibility

Anyone can recreate the exact same environment:

```bash
git clone <repo>
docker-compose up -d
docker-compose exec claude-flow bash
npx claude-flow@alpha hive-mind spawn "Implement a CRM" --claude
# Same results every time
```

### 3. Ephemerality

After the experiment:

```bash
# Option 1: Keep code, discard container
docker-compose down

# Option 2: Full reset
docker-compose down -v
rm -rf ./workspace/* ./data/*

# Option 3: Keep everything for later
docker-compose stop
# Resume anytime with: docker-compose start
```

### 4. Resource Management

The container approach allows:

- CPU/memory limits per container
- Easy monitoring with `docker stats`
- Kill runaway processes without affecting host
- Scale horizontally in K8s

---

## Lessons Learned

### What Worked Well

| Aspect | Observation |
|--------|-------------|
| **Non-root user** | No permission issues with mounted volumes |
| **Interactive mode** | `stdin_open + tty` essential for Claude Code |
| **Volume strategy** | Clean separation of code and state |
| **Minimal image** | Fast builds, small attack surface |

### Considerations for Production

| Consideration | Recommendation |
|---------------|----------------|
| **API Key Management** | Use secrets management (K8s secrets, Vault) |
| **Logging** | Add structured logging, ship to aggregator |
| **Monitoring** | Prometheus metrics for agent health |
| **Scaling** | StatefulSet for persistent identity in K8s |
| **Backup** | Regular backup of `./data/` volumes |

---

## Conclusion

This Docker environment demonstrates that **Claude-Flow can be safely containerized** for both experimentation and production use. The design provides:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                                                                             │
│                    CONTAINERIZATION BENEFITS                                │
│                                                                             │
│     ┌─────────────────────────────────────────────────────────────────┐    │
│     │                                                                 │    │
│     │  ✓ ISOLATION      AI agents sandboxed from host system          │    │
│     │                                                                 │    │
│     │  ✓ SECURITY       Non-root user, minimal packages               │    │
│     │                                                                 │    │
│     │  ✓ REPRODUCIBILITY Same environment everywhere                   │    │
│     │                                                                 │    │
│     │  ✓ EPHEMERALITY   Easy to create, easy to destroy               │    │
│     │                                                                 │    │
│     │  ✓ PORTABILITY    Works on any Docker host                      │    │
│     │                                                                 │    │
│     │  ✓ SCALABILITY    Ready for K8s, cloud deployments              │    │
│     │                                                                 │    │
│     └─────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│     This setup proves that AI agent orchestration can be deployed           │
│     safely in enterprise environments with proper isolation and             │
│     resource management.                                                    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

The CRM project—built entirely within this container—serves as proof that:

1. **Complex AI orchestration works in containers** (8 agents, parallel execution)
2. **Non-root execution is viable** (no privilege escalation needed)
3. **Volume mounts enable persistence** (session resume, memory retention)
4. **The setup translates to production** (K8s manifests, CI/CD pipelines)

For teams evaluating Claude-Flow for production use, this Docker environment provides a **low-risk, high-confidence** path to adoption.

---

## Quick Reference

### Start Fresh Environment

```bash
docker-compose up -d
docker-compose exec claude-flow bash
```

### Full Reset

```bash
docker-compose down -v
rm -rf ./workspace/* ./data/*
docker-compose up -d --build
```

### Export to Kubernetes

```bash
# Convert docker-compose to K8s manifests
kompose convert -f docker-compose.yml
```

---

*Document generated as part of the Claude-Flow development debrief series.*

*This Docker environment was designed by Dinis Cruz for safe AI experimentation.*
