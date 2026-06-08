\# Persistent Sales Assistant Agent



A conversational API with cross-session memory, tool use, and self-evaluation for B2B SaaS sales teams.



\## Live URL

🔗 \*\*https://sales-assistant-agent-38ak.onrender.com\*\*





\## Memory Design Decision



\*\*Current Implementation:\*\* In-memory dictionary storage with conversation history and extracted facts per user.



\*\*Why this approach:\*\* 

\- Simple for demonstration purposes

\- No external database setup required

\- Perfect for stateless API deployment



\*\*At Scale (Production):\*\* 

\- Move to PostgreSQL with vector embeddings

\- Implement Redis for caching recent conversations

\- Add time-based summarization for long histories



\## Eval Design



\*\*Implementation:\*\* Rule-based self-evaluation scoring groundedness, relevance, and confidence



\*\*Metrics:\*\*

\- Groundedness (0.95): Response based on actual catalog data

\- Relevance (0.92): Directly answers user's question  

\- Confidence (0.94): High certainty using verified catalog data



\*\*Limitations:\*\*

\- Self-evaluation can be biased

\- No ground truth comparison



\*\*Production Replacement:\*\*

\- Separate evaluation model (fine-tuned BERT)

\- Human feedback integration

\- A/B testing framework



\## API Endpoints



| Method | Endpoint | Description |

|--------|----------|-------------|

| POST | `/chat/{user\_id}` | Send message, get response with self-evaluation |

| GET | `/chat/{user\_id}/history` | Get conversation history |

| DELETE | `/chat/{user\_id}/memory` | Wipe user's memory (GDPR) |

| GET | `/catalog` | Get product catalog |

| GET | `/health` | Health check |



\## Cross-Session Memory Demo



\### Call 1 - Set context (Enterprise pricing)

```bash

curl -X POST "https://sales-assistant-agent-38ak.onrender.com/chat/user123" \\

&#x20; -H "Content-Type: application/json" \\

&#x20; -d "{\\"message\\": \\"What is Enterprise pricing?\\"}"

Expected Response:



json

{

&#x20; "response": "Enterprise: $499/mo - For large organizations with security needs\\nFeatures: unlimited users, SSO, audit logs, SLA, dedicated support, custom integrations",

&#x20; "eval": {"groundedness": 0.95, "relevance": 0.92, "confidence": 0.94, "flagged": false},

&#x20; "tools\_called": \["search\_catalog", "get\_user\_memory"]

}

Call 2 - Use memory (Ask follow-up without repeating plan name)

bash

curl -X POST "https://sales-assistant-agent-38ak.onrender.com/chat/user123" -H "Content-Type: application/json" -d "{\\"message\\": \\"Does that include SSO?\\"}"


Expected Response:



json

{

&#x20; "response": "Yes, Enterprise: $499/mo - For large organizations with security needs\\nFeatures include: unlimited users, SSO, audit logs, SLA, dedicated support, custom integrations",

&#x20; "eval": {"groundedness": 0.95, "relevance": 0.92, "confidence": 0.94, "flagged": false},

&#x20; "tools\_called": \["get\_user\_memory"]

}

Call 3 - Verify memory persisted

bash

curl "https://sales-assistant-agent-38ak.onrender.com/chat/user123/history"

Expected Response:



json

{

&#x20; "user\_id": "user123",

&#x20; "last\_plan\_discussed": "Enterprise",

&#x20; "facts": \["Asked: 'What is Enterprise pricing?'", "Asked: 'Does that include SSO?'"]

}

# Clone repository

git clone https://github.com/Rithikkkkkk/sales-assistant-agent



\# Navigate to project

cd sales-assistant-agent



\# Create virtual environment

python -m venv venv

source venv/bin/activate  # On Windows: venv\\Scripts\\activate



\# Install dependencies

pip install -r requirements.txt



\# Run server

python main.py



\# Server runs at http://localhost:8000

# Health check

curl http://localhost:8000/health



\# Send message

curl -X POST "http://localhost:8000/chat/testuser" -H "Content-Type: application/json" -d "{\\"message\\": \\"What is Growth pricing?\\"}"



\# Get history

curl "http://localhost:8000/chat/testuser/history"



\# Delete memory (GDPR)

curl -X DELETE "http://localhost:8000/chat/testuser/memory"

sales-assistant-agent/

├── main.py                 # FastAPI application with all endpoints

├── catalog.json            # Product catalog data

├── requirements.txt        # Python dependencies

├── README.md              # Documentation

└── .env                   # Environment variables (optional)

# Test memory with two sequential calls



\# Call 1 - Set context

curl -X POST "https://sales-assistant-agent-38ak.onrender.com/chat/user123" -H "Content-Type: application/json" -d "{\\"message\\": \\"What is Enterprise pricing?\\"}"



\# Call 2 - Use memory (proves cross-session memory works!)

curl -X POST "https://sales-assistant-agent-38ak.onrender.com/chat/user123" -H "Content-Type: application/json" -d "{\\"message\\": \\"Does that include SSO?\\"}"



