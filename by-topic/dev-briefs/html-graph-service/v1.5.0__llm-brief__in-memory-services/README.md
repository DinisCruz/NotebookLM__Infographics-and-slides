# In-Memory Service Pattern for FastAPI Microservices (v1.5.0)

[Home](../../../../../README.md) > [By Topic](../../../../README.md) > [Dev Briefs](../../README.md) > [HTML Graph Service](../README.md) > v1.5.0 In-Memory Services

---

## Overview

Implementation brief describing the In-Memory Service Pattern that enables FastAPI microservices to run in three modes: Full Remote (production HTTP), Full In-Memory (testing via TestClient), and Hybrid (mix of remote and in-memory). The pattern achieves this through dependency injection at the request layer, ensuring client packages never require service package dependencies in remote mode while enabling seamless in-memory testing.

## Contents

| File | Description |
|------|-------------|
| `v1.5.0__llm-brief__in-memory-services.md` | Full implementation brief (detailed) |
| `21 Jan - Microservice_In-Memory_Abstraction.pdf` | Extended PDF documentation |
| `21 Jan - Flexible FastAPI Service Pattern Diagram.jpg` | Visual architecture diagram |

## Key Topics

- **Three Execution Modes**: Full Remote, Full In-Memory, Hybrid service composition
- **Request Layer Abstraction**: `*__Requests` class as the single injection point
- **TestClient Integration**: Starlette TestClient for in-process ASGI calls
- **Dependency Injection**: Service packages injectable into client packages
- **Zero-Compromise Testing**: Millisecond tests, no port conflicts, deterministic results

## Execution Modes

| Mode | Use Case | Characteristics |
|------|----------|-----------------|
| Full Remote | Production | HTTP over network, pure client packages |
| Full In-Memory | Testing | TestClient, zero network, instant responses |
| Hybrid | Development | Mix local and remote services as needed |

## Key Classes

| Class | Purpose |
|-------|---------|
| `*__Service__In_Memory` | Creates FastAPI app with in-memory backend |
| `*__Service__Client__Config` | Configuration with mode, base_url, fast_api_app |
| `*__Service__Client__Requests` | THE INJECTION POINT - switches TestClient vs requests.Session |
| `Client__*__Service` | Factory wrapper with `set__fast_api_app().client()` |

## Implementation Phases

| Phase | Focus |
|-------|-------|
| 1 | Cache Service In-Memory (no dependencies) |
| 2 | HTML Graph Client Refactor (request layer abstraction) |
| 3 | HTML Graph Service In-Memory (with injected cache client) |

## Source

- **Version**: v1.5.0
- **Date**: January 2025
- **Scope**: mgraph_ai_service_cache, mgraph_ai_service_cache_client, mgraph_ai_service_html_graph

---

*Generated for NotebookLM content pipeline*
