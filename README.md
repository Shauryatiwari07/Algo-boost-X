# TokenIQ — Algorithmic Trading System
**Minimum Viable Product (MVP) • Delta Exchange Testnet Hackathon Edition**

TokenIQ is an institutional-grade algorithmic trading application designed to analyze cryptocurrency market data, generate quantitative trading signals, execute orders automatically, and manage risk through the **Delta Exchange Testnet**.

---

## 🏆 Hackathon Judging Alignment

| Criterion | Weight | TokenIQ Implementation |
|---|---|---|
| **Strategy Logic** | **30%** | Configurable Exponential Moving Average (EMA 9 / 21) crossover strategy evaluated strictly on completed candles with zero repainting and independent unit tests. |
| **Risk Management** | **20%** | Comprehensive 8-tier pre-execution validation engine (max order size, exposure limits, open orders count, daily loss limits, duplicate position guard, margin validation, and instant emergency stop). |
| **Technical Code** | **20%** | Clean modular architecture (FastAPI, SQLAlchemy, Pydantic, Pandas), sanitized logging (zero secret leaks), custom exceptions, and a 21-test pytest suite. |
| **Automation** | **15%** | Fully automated end-to-end pipeline: Ingestion ➔ Strategy ➔ Risk ➔ Execution State Machine ➔ Exchange Position Reconciliation. |
| **Demo & UI** | **15%** | Real-time interactive web dashboard with live Chart.js indicator plots, positions, order state lifecycle, trade P&L, and safety controls. |

---

## 🚀 Quickstart

### Prerequisites
- Python 3.11+
- Git

### 1. Installation
Clone the repository and install the dependencies:

```bash
cd backend
pip install -r requirements.txt
```

### 2. Configuration (`.env`)
Copy the template configuration file:

```bash
cp .env.example .env
```

To connect to live Delta Exchange Testnet, configure your API credentials in `.env`:
```ini
USE_LIVE_TESTNET=true
DELTA_API_KEY=your_testnet_api_key
DELTA_API_SECRET=your_testnet_api_secret
```
*(Leave `USE_LIVE_TESTNET=false` to run in self-contained simulation/sandbox mode without requiring live keys).*

### 3. Run the Application
Start the unified FastAPI server and dashboard:

```bash
python run.py
```

Open your browser to:
- **Interactive Trading Dashboard:** [http://localhost:8000/](http://localhost:8000/)
- **Swagger REST API Documentation:** [http://localhost:8000/docs](http://localhost:8000/docs)
- **Health Check Endpoint:** [http://localhost:8000/health](http://localhost:8000/health)

---

## 🧪 Running the Test Suite

Run the full automated test suite (unit tests, risk controls, P&L calculations, and E2E workflow):

```bash
pytest backend/tests -v
```

**Test Breakdown (21 Tests):**
- `test_strategy.py`: Golden cross (BUY), death cross (SELL), HOLD signals, and minimum candle window.
- `test_risk_engine.py`: 8 independent risk controls, duplicate order prevention, emergency stop switch.
- `test_pnl.py`: Realized P&L calculations, fee deductions, stop-loss (-2%) and take-profit (+4%) triggers.
- `test_execution.py`: Order lifecycle state machine, timeout reconciliation with exchange.
- `test_full_workflow.py`: End-to-end automated cycle from market ingestion to position sync.

---

## 📐 Project Structure

```
tokeniq/
├── backend/
│   ├── app/
│   │   ├── main.py                  # FastAPI application entry point & lifespan
│   │   ├── config.py                # Type-safe configuration settings
│   │   ├── database.py              # SQLite & SQLAlchemy engine
│   │   ├── models/                  # Database ORM models (Signal, Order, Trade, Position, Event)
│   │   ├── schemas/                 # Pydantic schemas for API serialization
│   │   ├── exchange/                # Delta Exchange HMAC-SHA256 client & mock sandbox
│   │   ├── services/
│   │   │   ├── market_data.py       # Market Data Service (ingestion, freshness, dedup)
│   │   │   ├── strategy_engine.py   # EMA Crossover Strategy (closed candle evaluation)
│   │   │   ├── risk_engine.py       # Multi-rule Risk Management Engine
│   │   │   ├── execution_engine.py  # Order Execution Engine & state machine
│   │   │   ├── position_manager.py  # Exchange position sync & P&L calculations
│   │   │   └── trading_loop.py      # Background orchestrator loop
│   │   ├── api/v1/                  # FastAPI router endpoints
│   │   ├── core/                    # Sanitized logger, thread-safe state, exceptions
│   │   └── static/                  # Interactive real-time web dashboard (HTML, CSS, JS)
│   ├── tests/                       # Comprehensive unit and integration test suite
│   ├── requirements.txt             # Python package dependencies
│   ├── .env.example                 # Environment configuration template
│   └── run.py                       # Easy launch script
├── docs/
│   ├── architecture.md              # Detailed system architecture specification
│   ├── strategy.md                  # Strategy mathematical definition & rules
│   └── demo_guide.md                # Hackathon evaluator walkthrough guide
└── README.md
```

---

## 🛡️ Safety & Risk Controls

1. **Testnet Isolated:** Designed strictly for testnet environments.
2. **Deterministic Pre-Execution Checks:** Every order must receive a signed `RiskDecision(approved=True)` before reaching the execution layer.
3. **No Blind Retries:** If an order submission experiences an ambiguous network timeout, it transitions to `RECONCILING` and polls the exchange rather than blindly retrying.
4. **Sanitized Logs:** All sensitive API keys, HMAC signatures, and auth headers are scrubbed before logging.
5. **Hardware/Operator Emergency Stop:** Instantly blocks new order generation via web UI or `POST /api/v1/emergency-stop`.

---

## 📄 License
MIT License. Built for the TokenIQ Algorithmic Trading Hackathon.
