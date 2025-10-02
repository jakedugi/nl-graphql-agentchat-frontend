# Agent Chat UI - Frontend Setup

## ✅ Frontend Status: Running

- **URL:** http://localhost:3001
- **Status:** ✅ Configured and running correctly

## ⚠️ Backend Status: Required

- **Expected URL:** http://localhost:3000
- **Status:** ❌ Not running - **YOU NEED TO SET THIS UP!**

## The Error You're Seeing

```
TypeError: newHistory.at is not a function
```

**Why?** The frontend is trying to connect to a backend at `http://localhost:3000`, but there's no server running there. The frontend expects to receive an array of messages but gets nothing.

## Quick Fix

You need a LangGraph backend server. See **[BACKEND_SETUP_NEEDED.md](./BACKEND_SETUP_NEEDED.md)** for detailed instructions.

### Quick Start - Option 1: Minimal Backend

```bash
# In a new terminal, create a simple backend
cd /Users/jakedugan/Projects
mkdir nl-graphql-backend && cd nl-graphql-backend
npm init -y
npm install express cors
```

Create `index.js`:
```javascript
const express = require('express');
const cors = require('cors');
const app = express();

app.use(cors());
app.use(express.json());

app.post('/threads', (req, res) => {
  res.json({ thread_id: `thread_${Date.now()}`, created_at: new Date().toISOString() });
});

app.get('/threads/:id', (req, res) => {
  res.json({ thread_id: req.params.id, values: { messages: [] } });
});

app.post('/threads/:id/runs/stream', (req, res) => {
  res.setHeader('Content-Type', 'text/event-stream');
  res.write(`data: ${JSON.stringify({ type: 'message', content: 'Hello!' })}\n\n`);
  res.end();
});

app.get('/assistants/:id', (req, res) => {
  res.json({ assistant_id: req.params.id, name: 'Football Agent' });
});

app.listen(3000, () => console.log('Backend on http://localhost:3000'));
```

Run it:
```bash
node index.js
```

### Option 2: Use Your Existing Backend

If you have a LangGraph backend project, update `.env.local`:

```bash
LANGGRAPH_API_URL=http://your-backend-url
```

## Current Configuration

**`.env.local`:**
```bash
NEXT_PUBLIC_API_URL=http://localhost:3001/api
NEXT_PUBLIC_ASSISTANT_ID=football-agent
LANGGRAPH_API_URL=http://localhost:3000
```

**How it works:**
1. Frontend (port 3001) sends requests to its own `/api/*` endpoints
2. Those endpoints proxy/forward to `LANGGRAPH_API_URL` (port 3000)
3. Backend processes and responds
4. Response flows back through proxy to frontend

## Verification Steps

Once backend is running:

1. ✅ Test backend: `curl http://localhost:3000/assistants/football-agent`
2. ✅ Open frontend: http://localhost:3001
3. ✅ Send a message in the chat
4. ✅ Check for no errors in console

## Project Structure

```
/Users/jakedugan/Projects/
├── nl-graphql-agentchat-frontend/    # THIS PROJECT (Frontend)
│   ├── src/
│   │   ├── app/
│   │   │   ├── api/[..._path]/       # Proxy to backend
│   │   │   └── page.tsx              # Chat UI
│   │   └── components/               # UI components
│   ├── .env.local                    # Configuration
│   └── package.json
│
└── nl-graphql-backend/               # YOU NEED TO CREATE THIS
    ├── index.js                      # Backend server
    └── package.json
```

## Files in This Project

| File | Purpose |
|------|---------|
| `src/app/page.tsx` | Main chat interface |
| `src/app/api/[..._path]/route.ts` | Proxy to backend |
| `src/providers/Stream.tsx` | Handles streaming responses |
| `src/components/thread/` | Chat components |
| `.env.local` | Configuration |

## Development

```bash
# Start frontend (already running)
npm run dev

# In another terminal - start backend
cd ../nl-graphql-backend
npm start
```

## Resources

- 📖 [Backend Setup Guide](./BACKEND_SETUP_NEEDED.md) - **READ THIS FIRST!**
- 📖 [Setup Complete](./SETUP_COMPLETE.md) - Frontend setup details
- 🔗 [Agent Chat UI Docs](https://github.com/langchain-ai/agent-chat-ui)
- 🔗 [LangGraph Docs](https://langchain-ai.github.io/langgraph/)

---

## Summary

✅ **Frontend:** Fully configured and running on port 3001  
❌ **Backend:** Missing - you need to create/run a LangGraph server on port 3000

**Next step:** Follow the [Backend Setup Guide](./BACKEND_SETUP_NEEDED.md)
