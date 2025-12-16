# 🚀 Order Execution Engine (Eterna Backend Task 2)

An order execution engine built with **Node.js** and **TypeScript** that processes **Limit Orders** with intelligent DEX routing (Raydium vs. Meteora) and real-time WebSocket status updates.

> **Deployment URL:** [Insert your free hosting URL here, e.g., https://eterna-backend-demo.onrender.com]  
> **YouTube Demo:** [Insert your YouTube video link here]

---

## 📋 Project Overview

This backend system simulates a high-frequency trading execution flow on the Solana network (using a Mock Implementation strategy). It handles the full lifecycle of an order: reception, intelligent routing based on best price, transaction simulation, and real-time client notification via WebSockets.

### Why Limit Orders?
I chose **Limit Orders** to demonstrate handling state and conditional execution. Unlike market orders which execute immediately, limit orders require the engine to check price conditions against the order's constraints, adding a layer of logic that showcases better architectural control over the "pending" and "routing" states.

**Extensibility:**
To support *Market* or *Sniper* orders, we would simply abstract the `OrderProcessor` class. A *Market* order would bypass the price condition check (executing immediately at best available price), while a *Sniper* order would add a block-monitoring service to trigger execution only when liquidity is added to a pool.

---

## 🛠 Features

* **REST API:** Secure endpoint for submitting orders.
* **Intelligent DEX Routing:** Simulates fetching quotes from **Raydium** and **Meteora**, comparing prices, and selecting the optimal route (best price/lowest slippage).
* **WebSocket Updates:** Real-time feedback loop pushing status changes (`pending` → `routing` → `confirmed`) to the client.
* **Queue Processing:** Uses an internal queue to manage high loads and ensure orders are processed sequentially and reliably.
* **Mock Execution:** Simulates realistic network latency (2-3s) and price variations (2-5%) for robust testing without devnet funds.

---

## ⚙️ Architecture & Design Decisions

### 1. Order Execution Flow
The system follows a non-blocking, event-driven architecture:
1.  **Submission:** `POST /api/orders/execute` validates the payload and pushes the order to an in-memory queue.
2.  **WebSocket Upgrade:** The client connects to the WS server using the returned `orderId` to listen for updates.
3.  **Processing (Queue Consumer):**
    * **Routing:** The engine mocks API calls to Raydium and Meteora.
    * **Decision:** It compares the mock prices. If Raydium offers 105 SOL/USDC and Meteora offers 103, the engine routes to Raydium.
    * **Execution:** A transaction is "built" and "submitted" (simulated delay).
4.  **Completion:** The final status (`confirmed` or `failed`) is pushed to the client, and the loop ends.

### 2. Status Lifecycle
* 🟡 **PENDING**: Order received and queued.
* 🔍 **ROUTING**: Fetching and comparing DEX quotes.
* 🏗️ **BUILDING**: Constructing the transaction payload.
* 🚀 **SUBMITTED**: Transaction sent to the network (mock delay).
* ✅ **CONFIRMED**: Success! Includes `txHash` and execution price.

---

## 🚀 Setup & Installation

### Prerequisites
* Node.js (v16+)
* npm or yarn

### Steps
1.  **Clone the repository**
    ```bash
    git clone [https://github.com/Mahadev2074/Eterna_Assg_Backend.git](https://github.com/Mahadev2074/Eterna_Assg_Backend.git)
    cd Eterna_Assg_Backend
    ```

2.  **Install dependencies**
    ```bash
    npm install
    ```

3.  **Configure Environment**
    Create a `.env` file in the root directory (optional for mock, but good practice):
    ```env
    PORT=3000
    NODE_ENV=development
    ```

4.  **Run the Server**
    ```bash
    # Development mode
    npm run dev

    # Production build
    npm run build
    npm start
    ```

---

## 🧪 Testing

### 1. Running Automated Tests
The project includes unit and integration tests covering routing logic, queue behavior, and WebSocket lifecycles.

```bash
npm test
