# 🚀 Order Execution Engine (Eterna Backend Task 2)

An order execution engine built with **Node.js** and **TypeScript** that processes **Limit Orders** with intelligent DEX routing (Raydium vs. Meteora) and real-time WebSocket status updates.

> **Deployment URL:** https://order-execution-engine-sach.vercel.app/ 
> **Production:** https://order-execution-engine-production-51ba.up.railway.app/api/orders

---

## 📋 Project Overview

This backend system simulates a high-frequency trading execution flow on the Solana network. It handles the full lifecycle of an order—from reception and intelligent routing based on the best price to transaction simulation and real-time client notification via WebSockets.

### Why Limit Orders?
I chose **Limit Orders** to demonstrate handling state and conditional execution. Unlike market orders which execute immediately, limit orders require the engine to check price conditions against the order's constraints. This adds a layer of logic that showcases better architectural control over the "pending" and "routing" states.

### Extensibility
To support *Market* or *Sniper* orders, we would simply abstract the `OrderProcessor` class. A *Market* order would bypass the price condition check (executing immediately), while a *Sniper* order would add a block-monitoring service to trigger execution only when liquidity is added to a pool.

---

## 🛠 Features

* **REST API:** Secure endpoint for submitting orders.
* **Intelligent DEX Routing:** Simulates fetching quotes from **Raydium** and **Meteora**, comparing prices, and selecting the optimal route.
* **WebSocket Updates:** Real-time feedback loop pushing status changes (`pending` → `routing` → `confirmed`) to the client.
* **Queue Processing:** Uses an internal queue to manage high loads and ensure orders are processed sequentially and reliably.
* **Mock Execution:** Simulates realistic network latency (2-3s) and price variations (2-5%) for robust testing without devnet funds.

---

---

## 📡 API Documentation

### 1. Submit Order (HTTP)
**Endpoint:** `POST /api/orders/execute`  
**Description:** Validates order details and pushes the order to the processing queue. Returns an `orderId` immediately for WebSocket subscription.

**Request Body:**
json
{
  "pair": "SOL/USDC",
  "amount": 10,
  "type": "limit",
  "limitPrice": 145.50
}

{
  "status": "queued",
  "orderId": "ord_123456789",
  "message": "Order validated and queued for execution."
}

// Message 1
{ "orderId": "...", "status": "pending", "timestamp": "..." }

// Message 2
{ "orderId": "...", "status": "routing", "info": "Checking Raydium vs Meteora..." }

// Message 3
{ "orderId": "...", "status": "confirmed", "txHash": "sol_tx_99...", "price": 145.55 }


## ⚙️ Architecture & Design Decisions

The system is designed as a non-blocking, event-driven architecture that handles high concurrency through an internal queue system.

### 1. Order Execution Flow
1.  **Submission:** `POST /api/orders/execute` validates the payload and pushes the order to an in-memory queue.
2.  **WebSocket Upgrade:** The client connects to the WS server using the returned `orderId` to listen for updates.
3.  **Processing (Queue Consumer):** The engine picks up orders sequentially.
    * **Routing:** The engine mocks API calls to Raydium and Meteora.
    * **Decision:** It compares the mock prices. If Raydium offers 105 SOL/USDC and Meteora offers 103, the engine routes to Raydium.
    * **Execution:** A transaction is "built" and "submitted" (simulated delay).
4.  **Completion:** The final status is pushed to the client, and the loop ends.

### 2. Transaction Settlement
* **Execution:** The system "builds" the transaction for the chosen DEX.
* **Settlement:** It simulates the blockchain confirmation process, handling potential failures or network latency.
* **Final Output:** Returns the final execution price and a simulated `txHash`.

### 3. Status Lifecycle (WebSocket)
* 🟡 **PENDING**: Order received and queued.
* 🔍 **ROUTING**: Fetching and comparing DEX quotes.
* 🏗️ **BUILDING**: Creating transaction payload.
* 🚀 **SUBMITTED**: Transaction sent to the network (mock delay).
* ✅ **CONFIRMED**: Success! Includes `txHash` and execution price.
* ❌ **FAILED**: If any step fails (includes error).

---

## 🧪 Testing & Quality Assurance

This project prioritizes reliability through rigorous testing.

### ✅ Postman/Insomnia Collection **plus** ≥10 Unit/Integration Tests

I have included a comprehensive testing suite in the `tests/` directory covering:
1.  **Routing Logic:** Tests ensuring the engine correctly picks the higher price between Raydium and Meteora.
2.  **Queue Behaviour:** Tests verifying that multiple orders are processed sequentially and no orders are dropped.
3.  **WebSocket Lifecycle:** Integration tests simulating a client connecting, receiving the correct sequence of status updates (`pending` → `confirmed`), and disconnecting.
<img width="1418" height="967" alt="image" src="https://github.com/user-attachments/assets/97629d9a-0fbc-4e8f-a1af-3f07d2da6aa8" />

**To run the tests:**
```bash
npm test
