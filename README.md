# 🤖 Polymarket MCP Server: Trade Prediction Markets with AI Agents
Connect Polymarket to your AI. The official-like MCP implementation for real-time prediction market analysis, automated odds tracking, and AI-powered trading insights.

# 📊 Dashboard
<img width="1248" height="832" alt="polymarket_mcp_dashboard_v2" src="https://github.com/user-attachments/assets/c3433282-fc5a-430a-b4b0-f86b5ce62ae5" />
<img width="1536" height="1024" alt="polymarket_mcp_banner" src="https://github.com/user-attachments/assets/e909dad5-e75b-40ed-aca2-6e89b6c536cd" />



# 🚀 Getting Started


```bash
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import {
  CallToolRequestSchema,
  ListToolsRequestSchema,
} from "@modelcontextprotocol/sdk/types.js";
import axios from "axios";


const server = new Server(
  {
    name: "polymarket-mcp-server",
    version: "1.0.0",
  },
  {
    capabilities: {
      tools: {},
    },
  }
 );

/**
 * Список доступных инструментов для ИИ
 */
server.setRequestHandler(ListToolsRequestSchema, async () => {
  return {
    tools: [
      {
        name: "search_markets",
        description: "Search for active prediction markets on Polymarket by keyword",
        inputSchema: {
          type: "object",
          properties: {
            query: { type: "string", description: "Keyword to search (e.g., 'Bitcoin', 'Election')" },
          },
          required: ["query"],
        },
      },
      {
        name: "get_market_odds",
        description: "Get real-time odds and order book data for a specific market ID",
        inputSchema: {
          type: "object",
          properties: {
            conditionId: { type: "string", description: "The unique condition ID of the market" },
          },
          required: ["conditionId"],
        },
      },
    ],
  };
});

/**
 * Логика выполнения инструментов
 */
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  const { name, arguments: args } = request.params;

  try {
    if (name === "search_markets") {
      const query = args?.query as string;
      const response = await axios.get(`${POLYMARKET_API_BASE}/markets?active=true`);
      const markets = response.data
        .filter((m: any) => m.question.toLowerCase().includes(query.toLowerCase()))
        .slice(0, 5);

      return {
        content: [{ type: "text", text: JSON.stringify(markets, null, 2) }],
      };
    }

    if (name === "get_market_odds") {
      const conditionId = args?.conditionId as string;
      const response = await axios.get(`${POLYMARKET_API_BASE}/book?token_id=${conditionId}`);
      
      return {
        content: [{ type: "text", text: JSON.stringify(response.data, null, 2) }],
      };
    }

    throw new Error(`Tool not found: ${name}`);
  } catch (error: any) {
    return {
      content: [{ type: "text", text: `Error: ${error.message}` }],
      isError: true,
    };
  }
});

/**
 * Запуск сервера через стандартный ввод/вывод (Stdio)
 */
async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
  console.error("Polymarket MCP Server running on stdio");
}

main().catch((error) => {
  console.error("Fatal error in main():", error);
  process.exit(1);
});
```

---
# ✨ Features
* get_market_data: Fetch live odds for any Polymarket URL.
* search_markets: Find trending markets by keywords.
* execute_trade: (Optional/Advanced) Place orders via API.

# 🧱 Architecture
```bash
polymarket-mcp-server-ai-agents/
│
├── server/
│   ├── agents/
│   ├── strategies/
│   ├── api/
│   ├── data/
│   └── core/
│
├── scripts/
├── tests/
├── docs/
│
├── .env.example
├── README.md
├── package.json / requirements.txt
└── docker-compose.yml
```

  * Agents – AI decision-makers
  * Strategies – trading logic
  * Data Layer – market + external signals
  * API Layer – Polymarket integration
  * Core Engine – execution + risk control
# Tech Stack

**Backend:**

* Python
* FastAPI
* Web3.py

**AI Layer:**

* OpenAI / LLMs
* Custom probability models

**Frontend:**

* React / Next.js
* TailwindCSS
* Recharts

**Data Layer:**

* WebSockets (real-time markets)
* PostgreSQL / Redis

Modern trading systems often use **event-driven architectures + real-time streams + AI scoring engines** ([Prolymarket][4])

# Use Cases
Automated prediction market trading
AI-powered crypto speculation
Political and event forecasting
Arbitrage strategies
Sentiment-based trading

## 🧪 Roadmap

- [ ] 🧱 Basic MCP server  
      Initial implementation of the MCP (Model Context Protocol) server core, including request handling and modular architecture.

- [ ] 🔗 Polymarket API integration  
      Connect to Polymarket APIs to fetch real-time market data, prices, and execute trades programmatically.

- [ ] 🤖 AI agent framework  
      Develop a flexible framework for creating and managing AI trading agents with pluggable strategies.

- [ ] 🧠 Strategy marketplace  
      Enable users to create, share, and deploy custom trading strategies in a modular marketplace.

- [ ] 📈 Reinforcement learning agents  
      Implement RL-based agents capable of learning and improving trading strategies over time.

- [ ] 📊 Dashboard UI  
      Build a web-based interface for monitoring agents, strategies, trades, and performance analytics.

# 🐳 6. Docker

### Dockerfile

```dockerfile
FROM python:3.11

WORKDIR /app
COPY . .

RUN pip install -r requirements.txt

CMD ["python", "main.py"]
```

      
 ![License](https://img.shields.io/badge/license-MIT-blue)
![Build](https://img.shields.io/github/actions/workflow/status/...)
![Stars](https://img.shields.io/github/stars/...)

Keywords: polymarket bot, AI trading agent, prediction market automation, crypto AI trading, decentralized trading bots, web3 AI tools

