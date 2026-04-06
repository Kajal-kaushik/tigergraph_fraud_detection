# 🕸️ FraudNet AI
### Graph-based Fraud Ring Detection & Risk Scoring Platform

> **Hackathon Project** · TigerGraph + Savanna (GraphRAG) · React + Node.js

---

## 📐 Architecture

```
                    ┌─────────────────────────────────────┐
                    │         FraudNet AI Platform         │
                    └─────────────────────────────────────┘
                                      │
          ┌───────────────────────────┼───────────────────────────┐
          │                           │                           │
   ┌──────▼──────┐           ┌────────▼────────┐         ┌───────▼──────┐
   │  React UI   │           │  Express API    │         │  TigerGraph  │
   │  (Port 3000)│◄─────────►│  (Port 5000)   │◄───────►│  (Port 9000) │
   │             │           │                 │         │   FraudNet   │
   │ Dashboard   │           │ /api/user/:id   │         │   Graph DB   │
   │ Investigate │           │ /api/rings      │         │              │
   │ Fraud Rings │           │ /api/suspicious │         │ 5 GSQL Queries│
   │ AI Chatbot  │           │ /api/chat       │         │ Graph Schema │
   │ Simulate    │           │ /api/simulate   │         └──────────────┘
   └─────────────┘           └────────┬────────┘
                                      │
                         ┌────────────▼────────────┐
                         │   TigerGraph Savanna     │
                         │   (GraphRAG Chatbot)     │
                         │   + Anthropic Claude API │
                         └─────────────────────────┘
```

---

## 🔥 Features

| Feature | Description |
|---------|-------------|
| **Graph Schema** | 6 vertex types, 6 edge types modelling user fraud networks |
| **Risk Scoring** | GSQL query computing 0–100 risk score with evidence |
| **Fraud Rings** | Connected-components detection via graph traversal |
| **Shortest Path** | BFS to nearest known fraudster |
| **Suspicious Entities** | Top-k devices, IPs, phones, accounts by shared users |
| **AI Chatbot** | Savanna GraphRAG chatbot with live graph evidence |
| **Transaction Simulator** | Inject fake transactions, trigger risk alerts |
| **Dark Dashboard** | Cyberpunk-themed professional UI with Cytoscape graphs |

---

## 📁 Project Structure

```
FraudNetAI/
├── backend/
│   ├── server.js            # Express app entry point
│   ├── tigergraph.js        # TigerGraph REST++ client
│   ├── .env.example         # Environment variables template
│   ├── package.json
│   └── routes/
│       ├── user.js          # /api/user/:id/network|risk|shortest-path
│       ├── rings.js         # /api/rings
│       ├── suspicious.js    # /api/suspicious
│       ├── chat.js          # /api/chat (Savanna + Claude fallback)
│       └── simulate.js      # /api/simulate
│
├── frontend/
│   ├── public/index.html
│   ├── tailwind.config.js
│   ├── package.json
│   └── src/
│       ├── App.js           # Router + Sidebar
│       ├── index.js
│       ├── index.css        # Tailwind + custom cyber styles
│       ├── lib/api.js       # Axios API client
│       ├── components/
│       │   ├── UI.jsx       # StatCard, RiskBadge, Toast, etc.
│       │   └── GraphView.jsx # Cytoscape.js wrapper
│       └── pages/
│           ├── Dashboard.jsx    # Stats, charts, suspicious entities
│           ├── Investigation.jsx # User risk + graph + path
│           ├── FraudRings.jsx   # Ring list + drill-down graph
│           ├── Chatbot.jsx      # AI fraud analyst chat
│           └── Simulate.jsx     # Transaction simulator + alerts
│
├── tigergraph/
│   ├── schema.gsql          # Graph schema definition
│   ├── queries.gsql         # All 5 GSQL queries
│   └── loading_job.gsql     # CSV data loading job
│
└── dataset/
    ├── generate_dataset.py  # Dataset generator
    ├── users.csv            # 200 users (15 fraudsters in 5 rings)
    ├── phones.csv           # 120 phone numbers
    ├── devices.csv          # 100 devices
    ├── ips.csv              # 80 IP addresses
    ├── accounts.csv         # 130 bank accounts
    ├── transactions.csv     # 300 transactions
    ├── uses_phone.csv
    ├── uses_device.csv
    ├── uses_ip.csv
    ├── owns_account.csv
    ├── sends.csv
    └── receives.csv
```

---

## ⚡ Quick Start

### 1. TigerGraph Setup

**Option A: TigerGraph Cloud (Recommended for hackathon)**
```bash
# 1. Sign up at https://tgcloud.io
# 2. Create a new instance (free tier available)
# 3. Note your host URL, graph name, username, password
```

**Option B: Local Docker**
```bash
docker pull tigergraph/tigergraph:latest
docker run -d -p 9000:9000 -p 14240:14240 --name tigergraph \
  tigergraph/tigergraph:latest
```

**Load Schema & Queries**
```bash
# Via GraphStudio (browser UI at :14240) paste contents of:
#   tigergraph/schema.gsql
#   tigergraph/queries.gsql

# Or via GSQL CLI:
gsql tigergraph/schema.gsql
gsql tigergraph/queries.gsql
```

**Load Dataset**
```bash
# 1. Generate CSVs (if not already generated)
cd dataset && python3 generate_dataset.py

# 2. Upload CSVs to TigerGraph and run loading job
gsql tigergraph/loading_job.gsql
```

---

### 2. Backend Setup

```bash
cd backend
npm install

# Copy and configure env
cp .env.example .env
# Edit .env with your TigerGraph credentials:
#   TG_HOST=https://your-instance.tgcloud.io
#   TG_PORT=9000
#   TG_GRAPH=FraudNet
#   TG_USER=tigergraph
#   TG_PASS=your-password
#   SAVANNA_KEY=your-savanna-key  (optional)
#   ANTHROPIC_API_KEY=sk-ant-...  (for chatbot fallback)

npm run dev   # Development with hot reload
# npm start   # Production
```

---

### 3. Frontend Setup

```bash
cd frontend
npm install

# Optional: set backend URL if not using default localhost:5000
echo "REACT_APP_API_URL=http://localhost:5000/api" > .env

npm start    # Opens http://localhost:3000
```

---

## 🌐 API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/user/:id/network?hops=2` | User's N-hop network graph |
| `GET` | `/api/user/:id/risk` | Risk score + explanation |
| `GET` | `/api/user/:id/shortest-path` | BFS to nearest fraudster |
| `GET` | `/api/rings` | All detected fraud rings |
| `GET` | `/api/suspicious` | Top suspicious entities |
| `POST` | `/api/chat` | AI chatbot (Savanna/Claude) |
| `POST` | `/api/simulate` | Simulate transaction + risk check |
| `GET` | `/health` | Service health check |

### Example Requests

```bash
# Get user risk score
curl http://localhost:5000/api/user/U001/risk

# Get fraud rings
curl http://localhost:5000/api/rings

# Chat with AI analyst
curl -X POST http://localhost:5000/api/chat \
  -H "Content-Type: application/json" \
  -d '{"message":"Why is user U001 risky?"}'

# Simulate transaction
curl -X POST http://localhost:5000/api/simulate \
  -H "Content-Type: application/json" \
  -d '{"sender_id":"U001","receiver_id":"U050","amount":15000}'
```

---

## 🤖 GSQL Queries

| Query | Description |
|-------|-------------|
| `getUserNetwork(user_id, hops)` | N-hop subgraph around user |
| `getRiskScore(user_id)` | 0-100 score + explanation list |
| `detectFraudRings()` | Connected components → rings |
| `topSuspiciousEntities(top_k)` | Top devices/IPs/phones/accounts |
| `shortestFraudPath(user_id)` | BFS to nearest fraudster |

### Risk Scoring Rules
```
+20  Shares device with >2 users
+20  Shares bank account with >2 users
+15  Shares phone with >2 users
+10  Shares IP with >2 users
+25  Within 2 hops of known fraudster
+30  User is known fraudster
────────────────────────────────
MAX  100
```

---

## 🚀 Deployment

### Deploy Frontend (Vercel)
```bash
cd frontend
npm run build
npx vercel --prod
```

### Deploy Backend (Render)
1. Push to GitHub
2. Connect repo on render.com
3. Set environment variables
4. Deploy as Web Service (`npm start`)

---

## 🔮 Future Improvements

- [ ] Real-time graph streaming with TigerGraph CDC
- [ ] ML-based risk scoring with GNN embeddings
- [ ] Multi-graph temporal fraud analysis
- [ ] Automated fraud ring alerting via email/Slack
- [ ] PDF report export with graph screenshots
- [ ] Role-based access control for analysts
- [ ] GraphQL API layer
- [ ] TigerGraph Insights dashboards integration

---

## 📸 Screenshots

| Dashboard | Investigation | Fraud Rings |
|-----------|--------------|-------------|
| `[stats, charts, top entities]` | `[risk score, cytoscape graph]` | `[ring list, drill-down graph]` |

| AI Chatbot | Transaction Simulator |
|------------|----------------------|
| `[ChatGPT-style UI with graph evidence]` | `[form + alert popup + history panel]` |

---

## 🏆 Hackathon Notes

**What makes this stand out:**
1. **Native graph operations** — Every feature uses graph traversal, not SQL joins
2. **GraphRAG** — AI answers are grounded in live graph evidence
3. **End-to-end** — Schema → Data → GSQL → API → UI fully connected
4. **Explainability** — Every risk score comes with a reason list from the graph
5. **Visual** — Cytoscape.js renders the actual fraud network in the browser

---

*Built with ❤️ using TigerGraph · Savanna · React · Node.js*
