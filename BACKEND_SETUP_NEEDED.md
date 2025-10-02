# ⚠️ Backend Server Required!

## The Problem

Your frontend is running correctly on **http://localhost:3001**, but you're getting errors because there's **no backend server** running on **http://localhost:3000**.

The error `newHistory.at is not a function` occurs because the frontend expects to receive thread history as an array from the backend API, but it's getting nothing or an error response.

## Architecture Explanation

```
┌─────────────────────────┐         ┌──────────────────────────┐
│   Agent Chat UI         │         │  LangGraph Backend       │
│   (Port 3001)           │────────▶│  (Port 3000)             │
│                         │  Proxy  │                          │
│   Frontend sends to:    │         │  Must implement:         │
│   /api/* internally     │         │  - POST /threads         │
│                         │         │  - GET /threads/:id      │
│   Proxy forwards to:    │         │  - POST /threads/:id/... │
│   LANGGRAPH_API_URL     │         │  - GET /assistants/:id   │
└─────────────────────────┘         └──────────────────────────┘
```

## Current Configuration

**Updated `.env.local`:**
```bash
NEXT_PUBLIC_API_URL=http://localhost:3001/api  # Frontend calls its own API
NEXT_PUBLIC_ASSISTANT_ID=football-agent

LANGGRAPH_API_URL=http://localhost:3000        # Proxy forwards to backend
LANGSMITH_API_KEY=                              # Optional: for auth
```

## Solutions

### Option 1: Create a Local LangGraph Backend

You need to create a separate backend project that implements the LangGraph API. Here's a minimal example:

**1. Create a new directory:**
```bash
cd /Users/jakedugan/Projects
mkdir nl-graphql-backend
cd nl-graphql-backend
npm init -y
```

**2. Install dependencies:**
```bash
npm install @langchain/langgraph @langchain/core express cors dotenv
npm install -D typescript @types/node @types/express
```

**3. Create a basic LangGraph server:**

```typescript
// src/index.ts
import express from 'express';
import cors from 'cors';

const app = express();
app.use(cors());
app.use(express.json());

// POST /threads - Create new thread
app.post('/threads', (req, res) => {
  const threadId = `thread_${Date.now()}`;
  res.json({ thread_id: threadId, created_at: new Date().toISOString() });
});

// GET /threads/:thread_id - Get thread state
app.get('/threads/:thread_id', (req, res) => {
  res.json({
    thread_id: req.params.thread_id,
    values: { messages: [] },
    next: [],
    checkpoint: { thread_id: req.params.thread_id }
  });
});

// POST /threads/:thread_id/runs/stream - Stream responses
app.post('/threads/:thread_id/runs/stream', (req, res) => {
  res.setHeader('Content-Type', 'text/event-stream');
  res.setHeader('Cache-Control', 'no-cache');
  res.setHeader('Connection', 'keep-alive');
  
  // Send a simple response
  res.write(`data: ${JSON.stringify({ type: 'message', content: 'Hello from backend!' })}\n\n`);
  res.end();
});

// GET /assistants/:assistant_id
app.get('/assistants/:assistant_id', (req, res) => {
  res.json({
    assistant_id: req.params.assistant_id,
    graph_id: 'football-agent',
    name: 'Football Agent',
    config: {}
  });
});

const PORT = 3000;
app.listen(PORT, () => {
  console.log(`🚀 Backend running on http://localhost:${PORT}`);
});
```

**4. Run the backend:**
```bash
npx tsx src/index.ts
```

### Option 2: Use LangGraph Cloud/Hosted Backend

If you have a LangGraph deployment (e.g., on LangSmith), update `.env.local`:

```bash
LANGGRAPH_API_URL=https://your-deployment.langchain.com
LANGSMITH_API_KEY=your_api_key_here
```

### Option 3: Use LangGraph Studio Locally

1. Install LangGraph Studio
2. Run your graph locally
3. Update `LANGGRAPH_API_URL` to point to the studio server (usually `http://localhost:8123`)

## Quick Test

After setting up a backend, test the connection:

```bash
# Test if backend is running
curl http://localhost:3000/assistants/football-agent

# Should return something like:
# {"assistant_id":"football-agent","graph_id":"football-agent","name":"Football Agent","config":{}}
```

## Next Steps

1. ✅ **Frontend is configured** (running on port 3001)
2. ⚠️ **Backend is needed** (should run on port 3000)
3. 🎯 **Choose an option above** to set up your backend
4. 🎯 **Test the connection** once backend is running

## Verification Checklist

Once your backend is running, verify:

- [ ] Backend responds at `http://localhost:3000/assistants/football-agent`
- [ ] Frontend loads without errors at `http://localhost:3001`
- [ ] You can send a message in the chat UI
- [ ] Messages are being proxied correctly (check Network tab in DevTools)

## Troubleshooting

### Still getting `newHistory.at is not a function`?

This means the backend is not returning the correct data structure. The backend must return:
- Thread history as an **array** of messages
- Each message should have proper structure with `id`, `type`, `content`

### Connection refused errors?

- Make sure backend is running on port 3000
- Check firewall settings
- Verify `LANGGRAPH_API_URL` in `.env.local`

### CORS errors?

Add CORS headers to your backend:
```typescript
app.use(cors({
  origin: 'http://localhost:3001',
  credentials: true
}));
```

---

**Summary:** Your frontend is set up correctly, but you need to create or connect to a LangGraph backend server on port 3000! 🚀

