# Concepts — How FowyldAI Works

Understand the architecture and design principles behind FowyldAI.

## Crown Engine

The Crown Engine is the core inference server. It receives requests, routes them to the appropriate model, and returns results. All processing happens within your infrastructure — nothing leaves your network.

## Sovereign Brain

The Sovereign Brain is FowyldAI's intelligent routing layer. It analyzes incoming requests and determines which model, pipeline, or agent should handle each task.

## Council Protocol

For complex tasks that require coordination between multiple AI agents, the Council Protocol manages multi-agent workflows — task decomposition, delegation, and result aggregation.

## Intelligence Bus

The communication backbone connecting all FowyldAI components. Handles message passing, event streaming, and observability.

## Memory Architecture

FowyldAI supports persistent memory across sessions — institutional memory, timeline stores, and context windows that survive restarts.

---

*For detailed architecture diagrams, see the Architecture Deep-Dive section (coming soon).*
