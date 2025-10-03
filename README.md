# Agent Chat UI

A chat interface for LangGraph agents built with Next.js.

## Quick Start

1. Install dependencies: `pnpm install`
2. Copy environment: `cp .env.example .env.local`
3. Set `LANGGRAPH_API_URL=http://localhost:3000` in `.env.local`
4. Start dev server: `pnpm dev`
5. Open [http://localhost:3001](http://localhost:3001)

## Environment Variables

- `LANGGRAPH_API_URL`: Your LangGraph backend URL (default: `http://localhost:2024`)
- `NEXT_PUBLIC_API_URL`: Frontend API endpoint (default: `/api`)
- `NEXT_PUBLIC_ASSISTANT_ID`: Default assistant ID (default: `agent`)

## Scripts

- `pnpm dev` - Start development server
- `pnpm build` - Build for production
- `pnpm lint` - Run linting
