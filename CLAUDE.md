# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a full-stack AI agent application built with Next.js (frontend) and FastAPI (LangGraph backend). It demonstrates integration between CopilotKit and LangGraph for building conversational AI agents with real-time UI updates, shared state management, and tool capabilities.

## Architecture

### Frontend (Next.js + React + TypeScript)
- **Framework**: Next.js 16 with App Router
- **Styling**: TailwindCSS 4
- **UI Components**: Custom components with CopilotKit integration
- **State Management**: CopilotKit's useCoAgent hook for shared state between frontend and agent
- **Agent Integration**: CopilotKit runtime with LangGraph HTTP agent

### Backend (Python + FastAPI + LangGraph)
- **Framework**: FastAPI with uvicorn server
- **Agent Framework**: LangGraph with LangChain integration
- **AI Model**: OpenAI GPT-4o via LangChain
- **Tools**: Custom LangChain tools with frontend-backend routing logic
- **State Management**: LangGraph StateGraph with MemorySaver checkpointer

### Key Integration Points
1. **CopilotKit Runtime** (`src/app/api/copilotkit/route.ts`): Connects frontend to LangGraph agent via HTTP
2. **FastAPI Endpoint** (`agent/main.py`): Exposes LangGraph agent through AG-UI integration
3. **State Synchronization**: Agent state (`proverbs` array) synchronized between frontend and backend
4. **Tool Routing**: Distinguishes between frontend tools (UI actions) and backend tools (agent capabilities)

## Development Commands

### Install Dependencies
```bash
# Install Node.js dependencies
pnpm install

# Install Python dependencies (runs automatically via postinstall)
pnpm install:agent
# or manually:
cd agent && uv sync
```

### Development Server
```bash
# Start both frontend and backend concurrently
pnpm dev

# Start only frontend
pnpm dev:ui

# Start only backend agent
pnpm dev:agent

# Start with debug logging
pnpm dev:debug
```

### Production
```bash
# Build for production
pnpm build

# Start production server
pnpm start
```

### Code Quality
```bash
# Run linting
pnpm lint
```

## Environment Setup

1. **OpenAI API Key**: Required for the LangGraph agent
   ```bash
   echo 'OPENAI_API_KEY=your-openai-api-key-here' > agent/.env
   ```

2. **Agent URL**: By default, the frontend connects to `http://localhost:8123`. Override with `AGENT_URL` environment variable.

## Project Structure

```
├── src/app/
│   ├── page.tsx              # Main UI with CopilotKit integration
│   ├── layout.tsx            # Root layout
│   └── api/copilotkit/       # CopilotKit runtime endpoint
├── agent/
│   ├── main.py               # FastAPI server entrypoint
│   ├── src/
│   │   ├── agent.py          # LangGraph workflow definition
│   │   └── util.py           # Tool routing utilities
│   └── pyproject.toml        # Python dependencies
├── package.json              # Node.js dependencies and scripts
└── next.config.ts           # Next.js configuration
```

## Key Concepts

### Agent State Management
The application uses a shared state pattern where:
- Frontend maintains state via `useCoAgent` hook
- Backend agent can read and modify this state
- State includes a `proverbs` array that both sides can manipulate

### Tool Categories
1. **Frontend Tools**: `setThemeColor`, `addProverb` - modify UI directly
2. **Backend Tools**: `get_weather` - agent-side data processing
3. **Generative UI**: `get_weather` renders custom React components based on agent output

### LangGraph Workflow
- Uses ReAct pattern with chat and tool nodes
- MemorySaver for conversation persistence
- Intelligent routing between frontend and backend tools
- StateGraph with AgentState extending CopilotKitState

## Development Notes

- The agent server runs on port 8123 by default (configurable via PORT env var)
- Frontend development server runs on standard Next.js port (3000)
- The application uses UV for Python package management
- TypeScript paths are configured with `@/*` alias pointing to `src/*`
- CopilotKit CSS variables are used for dynamic theming

## Testing

No test framework is currently configured. When adding tests:
- Consider Jest/Testing Library for frontend React components
- Add pytest for backend Python functionality
- Test integration points between frontend and agent state