# Embeddable RAG FAQ-Based AI Chatbot Widget - JavaScript, Cloudflare Workers, Vectorize, BGE Model, Llama 3 FullStack Project

A production-ready, embeddable AI chatbot widget powered by Cloudflare Workers, featuring RAG (Retrieval Augmented Generation), real-time streaming responses, and zero-dependency client-side implementation.

- **Live-Demo:** [https://ai-chatbot-widget.arnobt78.workers.dev/](https://ai-chatbot-widget.arnobt78.workers.dev/)

- **Production-Live:** [https://www.arnobmahmud.com/](https://www.arnobmahmud.com/)

![Screenshot 2026-01-25 at 15 10 10](https://github.com/user-attachments/assets/62c0b8eb-0d6c-416f-b6df-cc223b816a6e)

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Technology Stack](#-technology-stack)
- [Project Structure](#-project-structure)
- [How It Works](#-how-it-works)
- [Installation & Setup](#-installation--setup)
- [Environment Variables](#-environment-variables)
- [Deployment](#-deployment)
- [Usage](#-usage)
- [API Endpoints](#-api-endpoints)
- [Components & Architecture](#-components--architecture)
- [Reusing Components](#-reusing-components)
- [Code Examples](#-code-examples)
- [Keywords](#-keywords)
- [Conclusion](#-conclusion)

---

## 🎯 Overview

This project is a fully functional, embeddable AI chatbot widget that can be integrated into any website. It leverages Cloudflare's edge computing infrastructure to provide fast, scalable AI-powered conversations with RAG capabilities for accurate, context-aware responses.

### Key Highlights

- **Zero Dependencies**: Client-side widget is pure vanilla JavaScript
- **Edge Computing**: Runs on Cloudflare Workers for global low-latency
- **RAG Implementation**: Semantic search using Vectorize for accurate FAQ retrieval
- **Real-time Streaming**: Server-Sent Events (SSE) for progressive response rendering
- **Session Management**: Persistent conversations using Cloudflare KV
- **Mobile Responsive**: Optimized for all screen sizes with keyboard handling
- **Dark/Light Mode**: Automatic theme detection with manual toggle

---

## ✨ Features

### Backend Features

- **RAG (Retrieval Augmented Generation)**: Semantic search through FAQ database
- **Streaming AI Responses**: Real-time token streaming using Workers AI (Llama 3)
- **Session Persistence**: 30-day conversation history stored in Cloudflare KV
- **CORS Support**: Cross-origin requests enabled for embedding
- **Aggressive Caching**: Static assets cached for 1 year for optimal performance
- **Health Check Endpoint**: Monitoring and status verification

### Frontend Features

- **Embeddable Widget**: Single script tag integration
- **Inline Styles**: No external CSS dependencies required
- **Dark/Light Mode**: System preference detection with manual toggle
- **Mobile Optimized**: Responsive design with keyboard-aware positioning
- **Progressive Rendering**: Messages appear as they stream
- **Typing Indicator**: Visual feedback during AI processing
- **Menu System**: Theme toggle and chat clearing
- **Accessibility**: Proper ARIA labels and keyboard navigation

---

## 🛠 Technology Stack

### Backend

- **Cloudflare Workers**: Serverless edge computing platform
- **Workers AI**: AI model inference (Llama 3, BGE embeddings)
- **Vectorize**: Vector database for semantic search
- **Cloudflare KV**: Key-value storage for sessions
- **Server-Sent Events (SSE)**: Real-time streaming protocol

### Frontend

- **Vanilla JavaScript**: Zero dependencies, pure ES6+
- **Inline CSS**: Portable styling without external frameworks
- **Tailwind CSS**: Used only for demo page (optional)
- **DOM API**: Native browser APIs for maximum compatibility

### Development Tools

- **Wrangler CLI**: Cloudflare Workers development and deployment
- **Node.js**: Runtime environment
- **npm**: Package management

---

## 📁 Project Structure

```bash
cloudflare-chatbot-widget/
├── src/
│   ├── index.js          # Main Worker entry point (API routes, RAG, streaming)
│   └── input.css         # Tailwind CSS source (for demo page)
├── public/
│   ├── index.html        # Demo page showcasing the widget
│   ├── widget.js         # Embeddable widget script (vanilla JS)
│   └── styles.css        # Compiled CSS (Tailwind + widget styles)
├── wrangler.jsonc        # Cloudflare Workers configuration
├── tailwind.config.js    # Tailwind CSS configuration
├── package.json          # Dependencies and scripts
├── .gitignore           # Git ignore rules
└── README.md            # This file
```

### File Descriptions

- **`src/index.js`**: Main Worker code handling all API routes, RAG logic, streaming, and asset serving
- **`public/widget.js`**: Self-contained embeddable widget script (577 lines, fully commented)
- **`public/index.html`**: Demo page demonstrating widget integration
- **`public/styles.css`**: Compiled CSS including Tailwind utilities and widget-specific styles
- **`wrangler.jsonc`**: Cloudflare bindings configuration (KV, Vectorize, AI, ASSETS)

---

## 🔄 How It Works

### Architecture Flow

```bash
User Input → Widget (widget.js)
    ↓
POST /api/chat → Cloudflare Worker (src/index.js)
    ↓
1. Extract/Generate Session ID
2. Retrieve FAQ Context (RAG)
   - Generate embedding vector
   - Query Vectorize index
   - Get top 3 relevant FAQs
3. Build AI Message Array
   - System prompt + FAQ context
   - Last 10 conversation messages
4. Stream AI Response
   - Workers AI (Llama 3)
   - Transform stream
   - Parse SSE format
5. Save to KV Storage
    ↓
SSE Stream → Widget
    ↓
Progressive Rendering → User sees response in real-time
```

### RAG (Retrieval Augmented Generation) Process

1. **Question Embedding**: User's question is converted to a 768-dimensional vector using BGE model
2. **Semantic Search**: Vectorize index is queried for similar vectors (cosine similarity)
3. **Context Retrieval**: Top 3 most relevant FAQ entries are retrieved
4. **Context Injection**: FAQs are formatted and injected into the AI system prompt
5. **AI Generation**: Llama 3 generates response using the FAQ context + conversation history

### Session Management

- **Session Creation**: New sessions get a UUID-based session ID
- **Cookie Storage**: Session ID stored in HttpOnly cookie (30-day expiration)
- **KV Storage**: Full conversation history stored in Cloudflare KV
- **Session Retrieval**: Existing sessions load conversation history on widget initialization

---

## 🚀 Installation & Setup

### Prerequisites

- Node.js 18+ installed
- Cloudflare account with Workers plan
- Wrangler CLI installed globally (or use npx)

### Step 1: Clone Repository

```bash
git clone <repository-url>
cd cloudflare-chatbot-widget
```

### Step 2: Install Dependencies

```bash
npm install
```

This installs:

- `wrangler`: Cloudflare Workers CLI
- `tailwindcss`: CSS framework (for demo page)
- `autoprefixer` & `postcss`: CSS processing

### Step 3: Configure Cloudflare Resources

#### Create KV Namespace

```bash
npx wrangler kv namespace create CHAT_SESSIONS
```

Copy the namespace ID and update `wrangler.jsonc`:

```jsonc
"kv_namespaces": [
  {
    "binding": "CHAT_SESSIONS",
    "id": "YOUR_NAMESPACE_ID_HERE"
  }
]
```

#### Create Vectorize Index

```bash
npx wrangler vectorize create faq-vectors \
  --dimensions=768 \
  --metric=cosine
```

Update `wrangler.jsonc`:

```jsonc
"vectorize": [
  {
    "binding": "VECTORIZE",
    "index_name": "faq-vectors"
  }
]
```

#### Enable Workers AI

Workers AI is automatically available in your Worker. No additional setup needed.

### Step 4: Build CSS

```bash
npm run build:css
```

This compiles Tailwind CSS and appends widget-specific styles.

### Step 5: Deploy Worker

```bash
npm run deploy
```

Or manually:

```bash
npx wrangler deploy
```

### Step 6: Seed FAQ Data

After deployment, populate the Vectorize index:

```bash
curl -X POST https://your-worker.workers.dev/api/seed
```

This generates embeddings for all FAQs and stores them in Vectorize.

---

## 🔐 Environment Variables

### Cloudflare Workers Configuration

This project uses Cloudflare bindings configured in `wrangler.jsonc`. No `.env` file is needed for production.

### Local Development

For local development, create `.dev.vars` (git-ignored):

```bash
# .dev.vars (optional, for local testing)
# Most bindings are configured in wrangler.jsonc
```

### Required Bindings (Configured in wrangler.jsonc)

1. **KV Namespace** (`CHAT_SESSIONS`)
   - Purpose: Store conversation sessions
   - TTL: 30 days
   - Created via: `wrangler kv namespace create`

2. **Vectorize Index** (`VECTORIZE`)
   - Purpose: Store FAQ embeddings for RAG
   - Dimensions: 768 (BGE model output)
   - Metric: cosine similarity
   - Created via: `wrangler vectorize create`

3. **Workers AI** (`AI`)
   - Purpose: Run AI models (Llama 3, BGE embeddings)
   - Automatically available: No setup needed

4. **Assets** (`ASSETS`)
   - Purpose: Serve static files (widget.js, styles.css, index.html)
   - Directory: `./public`
   - Automatically configured

### Configuration File: `wrangler.jsonc`

```jsonc
{
  "name": "ai-chatbot-widget",
  "main": "src/index.js",
  "compatibility_date": "2025-12-23",
  "assets": {
    "directory": "./public",
    "binding": "ASSETS",
  },
  "ai": {
    "binding": "AI",
  },
  "vectorize": [
    {
      "binding": "VECTORIZE",
      "index_name": "faq-vectors",
    },
  ],
  "kv_namespaces": [
    {
      "binding": "CHAT_SESSIONS",
      "id": "YOUR_KV_NAMESPACE_ID",
    },
  ],
}
```

---

## 📦 Deployment

### Development

```bash
npm run dev
```

Starts local development server with hot reload.

### Production

```bash
npm run deploy
```

This:

1. Builds CSS (`npm run build:css`)
2. Deploys Worker to Cloudflare (`wrangler deploy`)

### Post-Deployment Steps

1. **Seed FAQ Data**:

   ```bash
   curl -X POST https://your-worker.workers.dev/api/seed
   ```

2. **Verify Health**:

   ```bash
   curl https://your-worker.workers.dev/api/health
   ```

3. **Test Widget**:
   Visit `https://your-worker.workers.dev` to see the demo page.

---

## 💻 Usage

### Embedding the Widget

#### Basic Integration

Add these lines to your HTML:

```html
<!-- Optional: Configure widget -->
<script>
  window.CHATBOT_BASE_URL = "https://your-worker.workers.dev";
  window.CHATBOT_TITLE = "Support Assistant";
  window.CHATBOT_GREETING = "Hi! 👋 How can I help you today?";
  window.CHATBOT_PLACEHOLDER = "Type your message...";
</script>

<!-- Load widget script -->
<script src="https://your-worker.workers.dev/widget.js"></script>
```

#### Configuration Options

| Variable              | Default                          | Description              |
| --------------------- | -------------------------------- | ------------------------ |
| `CHATBOT_BASE_URL`    | `window.location.origin`         | API endpoint URL         |
| `CHATBOT_TITLE`       | `'Chat Assistant'`               | Widget header title      |
| `CHATBOT_GREETING`    | `'👋 How can I help you today?'` | Initial greeting message |
| `CHATBOT_PLACEHOLDER` | `'Message...'`                   | Input field placeholder  |

### Example: React Integration

```jsx
import { useEffect } from "react";

function App() {
  useEffect(() => {
    // Configure widget
    window.CHATBOT_BASE_URL = "https://your-worker.workers.dev";
    window.CHATBOT_TITLE = "AI Assistant";

    // Load widget script
    const script = document.createElement("script");
    script.src = "https://your-worker.workers.dev/widget.js";
    script.async = true;
    document.body.appendChild(script);

    return () => {
      // Cleanup (optional)
      document.body.removeChild(script);
    };
  }, []);

  return <div>Your app content</div>;
}
```

### Example: Next.js Integration

```tsx
// app/layout.tsx
export default function RootLayout({ children }) {
  return (
    <html>
      <head>
        <script
          dangerouslySetInnerHTML={{
            __html: `
              window.CHATBOT_BASE_URL = '${process.env.NEXT_PUBLIC_CHATBOT_URL}';
              window.CHATBOT_TITLE = 'Support';
            `,
          }}
        />
      </head>
      <body>
        {children}
        <script src={`${process.env.NEXT_PUBLIC_CHATBOT_URL}/widget.js`} />
      </body>
    </html>
  );
}
```

---

## 🔌 API Endpoints

### POST `/api/chat`

Sends a message and receives a streaming AI response.

**Request:**

```json
{
  "message": "Tell me about your services"
}
```

**Response:** Server-Sent Events (SSE) stream

```json
data: {"response": "Hello"}
data: {"response": "! "}
data: {"response": "I can"}
...
data: [DONE]
```

**Headers:**

- `Content-Type: application/json` (request)
- `Content-Type: text/event-stream` (response)
- `Set-Cookie: chatbot_session=...` (new sessions)

---

### GET `/api/history`

Retrieves conversation history for the current session.

**Request:** Cookie-based (no body needed)

**Response:**

```json
{
  "messages": [
    {
      "role": "user",
      "content": "Hello",
      "timestamp": 1234567890
    },
    {
      "role": "assistant",
      "content": "Hi! How can I help?",
      "timestamp": 1234567891
    }
  ]
}
```

**Headers:**

- Cookie: `chatbot_session=<session-id>`

---

### POST `/api/seed`

Populates the Vectorize index with FAQ embeddings.

**Request:** No body required

**Response:**

```json
{
  "success": true,
  "count": 20
}
```

**Note:** Run this once after deployment to populate the knowledge base.

---

### GET `/api/health`

Health check endpoint for monitoring.

**Response:**

```json
{
  "status": "ok"
}
```

---

### Static Assets

- `GET /widget.js` - Embeddable widget script
- `GET /styles.css` - Widget stylesheet
- `GET /index.html` - Demo page
- `GET /` - Serves `index.html`

All static assets are served with aggressive caching headers (1 year).

---

## 🏗 Components & Architecture

### Backend Components

#### 1. RAG Function (`faq()`)

**Location:** `src/index.js`

**Purpose:** Implements Retrieval Augmented Generation

**Process:**

```javascript
async function faq(env, q) {
  // 1. Generate embedding
  const embedding = await env.AI.run("@cf/baai/bge-base-en-v1.5", {
    text: [q],
  });

  // 2. Query Vectorize
  const results = await env.VECTORIZE.query(embedding.data[0], {
    topK: 3,
    returnMetadata: "all",
  });

  // 3. Format context
  return results.matches
    .map((m) => `Q: ${m.metadata.question}\nA: ${m.metadata.answer}`)
    .join("\n\n");
}
```

**Reusability:** Can be extracted to a separate module for use in other Workers.

---

#### 2. Chat Handler (`chat()`)

**Location:** `src/index.js`

**Purpose:** Handles chat requests with streaming

**Key Features:**

- Session management
- RAG context injection
- SSE streaming
- KV persistence

**Reusability:** The streaming pattern can be adapted for other AI models.

---

#### 3. Seed Function (`seed()`)

**Location:** `src/index.js`

**Purpose:** Populates Vectorize index

**Process:**

1. Iterate through FAQ array
2. Generate embeddings in parallel
3. Upsert to Vectorize index

**Reusability:** FAQ array can be externalized to a JSON file or database.

---

### Frontend Components

#### 1. Widget Initialization (`init()`)

**Location:** `public/widget.js`

**Purpose:** Creates DOM elements and sets up widget

**Key Features:**

- Creates floating action button
- Creates chat window
- Applies responsive styles
- Binds event handlers

**Reusability:** The initialization pattern can be adapted for other embeddable widgets.

---

#### 2. Theme System (`theme()`)

**Location:** `public/widget.js`

**Purpose:** Manages dark/light mode

**Implementation:**

- Toggles `dark` class on container
- Updates inline styles for all elements
- Preserves state across sessions

**Reusability:** Theme logic can be extracted to a standalone utility.

---

#### 3. Streaming Handler (`send()`)

**Location:** `public/widget.js`

**Purpose:** Handles SSE streaming and progressive rendering

**Process:**

1. Send POST request
2. Read SSE stream
3. Parse `data: {...}` lines
4. Update DOM incrementally

**Reusability:** SSE handling can be extracted to a reusable utility function.

---

## 🔄 Reusing Components

### Using RAG Function in Another Project

```javascript
// Copy faq() function from src/index.js
// Adapt to your embedding model and vector database

async function customRAG(env, question) {
  const embedding = await env.AI.run("@cf/baai/bge-base-en-v1.5", {
    text: [question],
  });

  const results = await env.VECTORIZE.query(embedding.data[0], {
    topK: 5, // Adjust as needed
    returnMetadata: "all",
  });

  return results.matches.map((m) => m.metadata);
}
```

### Using Widget Script in Another Project

1. **Copy `widget.js`** to your project
2. **Update API URL**:

   ```javascript
   window.CHATBOT_BASE_URL = "https://your-api.com";
   ```

3. **Customize styling** by modifying inline styles in `init()`
4. **Add to HTML**:

   ```html
   <script src="/path/to/widget.js"></script>
   ```

### Using Streaming Pattern

```javascript
// Reusable SSE handler
async function streamResponse(url, data, onChunk) {
  const response = await fetch(url, {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify(data),
  });

  const reader = response.body.getReader();
  const decoder = new TextDecoder();

  while (true) {
    const { done, value } = await reader.read();
    if (done) break;

    const lines = decoder.decode(value).split("\n");
    for (const line of lines) {
      if (line.startsWith("data: ")) {
        const json = JSON.parse(line.slice(6));
        onChunk(json);
      }
    }
  }
}

// Usage
streamResponse("/api/chat", { message: "Hello" }, (chunk) => {
  console.log(chunk.response);
});
```

---

## 💡 Code Examples

### Example 1: Custom FAQ Dataset

Modify the `seed()` function to use your own FAQs:

```javascript
const faqs = [
  ["What is your return policy?", "We offer 30-day returns..."],
  ["How do I track my order?", "You can track your order..."],
  // Add more FAQs
];
```

### Example 2: Custom AI Model

Switch to a different Workers AI model:

```javascript
// In chat() function
const stream = await env.AI.run("@cf/meta/llama-3-70b-instruct", {
  messages: msgs,
  stream: true,
});
```

### Example 3: Custom Session Storage

Use a different storage backend:

```javascript
// Replace KV with your own storage
await yourDatabase.save(sessionId, sessionData);
```

### Example 4: Custom Widget Styling

Modify widget appearance in `widget.js`:

```javascript
// In init() function, update inline styles
btn.style.cssText = `
  position: fixed !important;
  bottom: 2rem !important;
  right: 2rem !important;
  width: 4rem !important;
  height: 4rem !important;
  background-color: #your-color !important;
  // ... more styles
`;
```

---

## 🏷 Keywords

- **RAG (Retrieval Augmented Generation)**
- **Cloudflare Workers**
- **Server-Sent Events (SSE)**
- **Vector Database**
- **Semantic Search**
- **Embeddable Widget**
- **Vanilla JavaScript**
- **Edge Computing**
- **AI Chatbot**
- **Streaming Responses**
- **Session Management**
- **Dark Mode**
- **Mobile Responsive**
- **Zero Dependencies**
- **Workers AI**
- **Vectorize**
- **Cloudflare KV**
- **Embeddings**
- **BGE Model**
- **Llama 3**

---

## 📝 Conclusion

This project demonstrates a production-ready implementation of an AI chatbot widget with RAG capabilities, built entirely on Cloudflare's edge computing platform. It showcases:

- **Modern Architecture**: Serverless, edge-first design
- **Best Practices**: Clean code, comprehensive comments, proper error handling
- **Performance**: Aggressive caching, streaming responses, optimized assets
- **Portability**: Zero-dependency client-side code, inline styles
- **Scalability**: Cloudflare's global network ensures low latency worldwide

The codebase is well-documented and structured for easy understanding and extension. Each component can be reused independently in other projects.

---

## Happy Coding! 🎉

This is an **open-source project** - feel free to use, enhance, and extend this project further!

If you have any questions or want to share your work, reach out via GitHub or my portfolio at [https://www.arnobmahmud.com/](https://www.arnobmahmud.com/).

**Enjoy building and learning!** 🚀

Thank you! 😊

---
