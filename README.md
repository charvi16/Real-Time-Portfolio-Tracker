# Real-Time Portfolio Tracker + Alerting Engine

A full-stack **real-time stock portfolio tracker** that:

- Lets you **add trades** (stock + buy price + optional quantity & alert thresholds)
- Fetches **live prices** from a free stock API
- Streams **real-time updates** to the UI using **WebSockets**
- Computes **profit/loss** in real time
- Runs a **scheduled alerting engine** that sends **email alerts** when thresholds are crossed
- Persists all trades in **MongoDB**
- Is fully **Docker-ready** for easy deployment

---

## ✨ Features

- 📥 Add stock symbol, buy price, quantity, alert-above & alert-below thresholds  
- 📊 Responsive UI table showing:
  - Symbol
  - Buy price
  - Latest price
  - P/L (absolute & %)
  - Alert thresholds
- ⚡ Live price updates via **WebSockets**
- ⏱ Background **cron job** to:
  - Poll API every X seconds / minutes
  - Update prices in DB
  - Trigger email alerts
- 📧 Email alerts using **Nodemailer**
- 🗄 Persistent storage in **MongoDB**
- 🐳 **Dockerized** backend, frontend & MongoDB (via `docker-compose`)

---

## 🏗 System Architecture

### High-Level Flow

1. **React Frontend (Vite)**  
   - Shows portfolio table  
   - Sends `POST /api/trades` to create new trades  
   - Connects to WebSocket to receive live price updates  

2. **Node.js + Express Backend**
   - REST API for CRUD on trades
   - WebSocket server using **Socket.IO**
   - Scheduler using **node-cron** to:
     - Fetch prices from stock API
     - Update DB
     - Send alerts via email
     - Broadcast updates to clients

3. **MongoDB**
   - Stores all trades with current price and last updated time

4. **Email Provider**
   - SMTP server (e.g. Gmail, Mailtrap) configured via `.env`

5. **Docker**
   - `backend`, `frontend`, and `mongo` services orchestrated using `docker-compose`

### Architecture Diagram (Mermaid)

```mermaid
flowchart LR
  subgraph Client
    UI[React Portfolio UI]
  end

  subgraph Server[Node.js Backend]
    API[REST API]
    WS[WebSocket (Socket.IO)]
    CRON[Scheduler (node-cron)]
    ALERT[Alert Service]
  end

  DB[(MongoDB)]
  MAIL[(SMTP / Email Server)]
  PRICEAPI[(Stock Price API)]

  UI -- HTTP --> API
  UI -- WebSocket --> WS

  API -- CRUD Trades --> DB
  CRON -- fetch prices --> PRICEAPI
  CRON -- update --> DB
  CRON -- push updates --> WS
  ALERT -- read trades --> DB
  ALERT -- send alerts --> MAIL
  ALERT -- notify --> WS
