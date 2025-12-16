# Eterna_Assg_Backend

# 🚀 Order Execution Engine (Backend)

A scalable backend service for handling order submission, queuing, and execution using an asynchronous, event-driven architecture.

🔗 **Live Deployment**: https://order-execution-engine-sach.vercel.app/

🔗 **Production**: https://order-execution-engine-production-51ba.up.railway.app/api/orders

---

## 📌 Overview

This project implements an **Order Execution Engine** that accepts orders via REST APIs, validates them, and processes execution asynchronously using a queue-based system.

It is designed to simulate real-world backend systems used in trading and high-throughput applications, focusing on scalability, reliability, and clean architecture.

---

## 🛠 Tech Stack

- Node.js
- TypeScript
- Express.js
- Redis
- BullMQ
- Vercel (Deployment)


---

## 🔑 The 5 Main Components

### 1️⃣ API Server (Fastify)

**Responsibilities:**
- Accepts incoming `POST /order` HTTP requests
- Validates request payload
- Saves the order in **PostgreSQL** with status `PENDING`
- Pushes a job into the **Redis Queue**
- Immediately responds with an `orderId`

---

### 2️⃣ Message Broker (Redis)

**Responsibilities:**
**Queue (BullMQ):**
- Holds orders waiting to be processed
- Ensures retry, durability, and ordered execution

**Pub/Sub:**
- Broadcasts order status updates such as:
  - `PROCESSING`
  - `COMPLETED`
  - `FAILED`
---

### 3️⃣ Worker Service

**Responsibilities:**
- Pulls orders from Redis Queue one by one
- Executes the core order logic
- Calls the **DexRouter**
- Updates order status in PostgreSQL
- Publishes results via Redis Pub/Sub
---

### 4️⃣ DexRouter (Business Logic)

**Responsibilities:**
- Compares prices across liquidity sources
- Determines the best execution route
- (Example) Chooses between **Raydium** and **Meteora**
---

Commands to run : 1. npx ts-node src/lib/worker.ts
                  2. npx ts-node src/app.ts
                  3. cd client
                     npm run dev
                     
---
Postman collection

{
  "message": "Route GET:/api/orders not found",
  "error": "Not Found",
  "statusCode": 404
}



<img width="1392" height="983" alt="image" src="https://github.com/user-attachments/assets/1fe37ed5-3187-4bb7-8516-e76bc2fa2c0f" />

