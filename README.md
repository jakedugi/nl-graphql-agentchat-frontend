# Agent Chat UI

A modern, streaming chat interface for LangGraph agents built with Next.js, React, and TypeScript.

## Features

- **Real-time streaming** - Live responses from LangGraph agents
- **File upload support** - Images and PDFs with multimodal processing
- **Thread management** - Organized conversation history
- **Tool call visualization** - Interactive display of agent actions
- **Responsive design** - Works on desktop and mobile devices
- **Dark/Light themes** - Automatic theme switching

## Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   Frontend      │    │   API Proxy      │    │   LangGraph     │
│   (Next.js)     │───▶│   (Next.js)      │───▶│   Backend       │
│   Port 3001     │    │   /api/*         │    │   Port 3000     │
└─────────────────┘    └──────────────────┘    └─────────────────┘
```

## Quick Start

### Prerequisites

- Node.js 18+
- npm or pnpm
- A LangGraph backend server running on port 3000

### Installation

```bash
# Clone the repository
git clone https://github.com/jakedugi/nl-graphql-agentchat-frontend.git
cd nl-graphql-agentchat-frontend

# Install dependencies
pnpm install

# Copy environment configuration
cp .env.example .env.local

# Configure your backend URL
echo "LANGGRAPH_API_URL=http://localhost:3000" >> .env.local
```

### Development

```bash
# Start the development server
pnpm dev
```

Open [http://localhost:3001](http://localhost:3001) in your browser.

## Configuration

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `NEXT_PUBLIC_API_URL` | Frontend API endpoint | `/api` |
| `NEXT_PUBLIC_ASSISTANT_ID` | Default assistant ID | `agent` |
| `LANGGRAPH_API_URL` | LangGraph backend URL | `http://localhost:2024` |
| `LANGSMITH_API_KEY` | Optional: LangSmith API key | - |

### Backend Requirements

The backend must implement these LangGraph API endpoints:

- `POST /threads` - Create new conversation thread
- `GET /threads/:id` - Get thread state
- `POST /threads/:id/runs/stream` - Stream agent responses
- `GET /assistants/:id` - Get assistant information

## Project Structure

```
src/
├── app/                    # Next.js app router
│   ├── api/[..._path]/     # API proxy routes
│   ├── globals.css         # Global styles
│   ├── layout.tsx          # Root layout
│   └── page.tsx            # Main chat page
├── components/             # Reusable UI components
│   ├── thread/             # Chat-specific components
│   └── ui/                 # Base UI components
├── hooks/                  # React hooks
├── lib/                    # Utilities and helpers
└── providers/              # React context providers
```

## Key Components

- **Thread Provider** - Manages conversation state
- **Stream Provider** - Handles real-time streaming
- **File Upload Hook** - Multimodal file processing
- **Message Components** - Human/AI message rendering
- **Tool Call Table** - Interactive tool visualization

## Development

### Available Scripts

```bash
pnpm dev        # Start development server
pnpm build      # Build for production
pnpm start      # Start production server
pnpm lint       # Run ESLint
pnpm format     # Format code with Prettier
```

### Code Quality

- **TypeScript** - Full type safety
- **ESLint** - Code linting
- **Prettier** - Code formatting
- **Tailwind CSS** - Utility-first styling

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Run tests and linting
5. Submit a pull request

## Resources

- [LangGraph Documentation](https://langchain-ai.github.io/langgraph/)
- [Next.js Documentation](https://nextjs.org/docs)
- [LangChain Agent Chat UI](https://github.com/langchain-ai/agent-chat-ui)

## License

Licensed under the MIT License.
