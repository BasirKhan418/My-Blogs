---
title: "Welcome to the Magic: Real-Time Updates with Server-Sent Events (SSE) in Node.js + React.js"
seoTitle: "Real-Time Updates with Server-Sent Events (SSE) in Node.js and React.j"
seoDescription: "Learn how to implement real-time data streaming using Server-Sent Events (SSE) with Node.js and React.js. Build a live dashboard that streams updates instan"
datePublished: Sun Oct 19 2025 16:46:02 GMT+0000 (Coordinated Universal Time)
cuid: cmgxxuiav000202kzgckhevza
slug: welcome-to-the-magic-real-time-updates-with-server-sent-events-sse-in-nodejs-reactjs
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/PSpf_XgOM5w/upload/c8376e04ed345383092d75e3b15ec7e0.jpeg
tags: realtime, redis, javascript, nodejs, react-js, redis-cache, serversentevents

---

In today’s web world, **real-time communication** is everywhere — whether you’re building dashboards, notifications, live metrics, or chat systems.  
When you hear “real-time,” most people think of **WebSockets**.

But there’s another lightweight, reliable alternative that’s often overlooked —  
💡 **Server-Sent Events (SSE)**.

In this blog, we’ll build a **real-time data stream** using **Node.js (Express)** on the backend and **React.js** on the frontend.  
By the end, your browser will receive **live updates** from the server — no manual refreshes, no complex setup. Just pure streaming magic. ✨

---

## 🧠 What Are Server-Sent Events?

Server-Sent Events (SSE) allow the **server to push data** to the client **over a single HTTP connection**.  
The client subscribes to this stream and receives automatic updates — all using standard HTTP and no extra libraries.

Unlike **WebSockets**, SSE is:

* ✅ **Unidirectional** (Server ➜ Client)
    
* ✅ **Simple to implement**
    
* ✅ **HTTP/1.1 friendly**
    
* ✅ **Perfect for dashboards, logs, notifications**
    

---

## ⚙️ How SSE Works (Simple Concept)

1. The **browser** opens a persistent connection to `/events`.
    
2. The **server** keeps the connection open and sends messages in this format:
    
    ```bash
    data: Hello Client!
    \n\n
    ```
    
3. The **browser** listens using the `EventSource` API.
    

So when new data arrives, the client instantly updates — no refresh or request needed.

---

## 🧩 Step 1: Setting Up the Node.js (Express) Server

Let’s start by creating a new Node.js project.

```bash
mkdir sse-demo && cd sse-demo
npm init -y
npm install express cors
```

Now create a file named `server.js` 👇

```bash
import express from "express";
import cors from "cors";

const app = express();
app.use(cors());
const PORT = 4000;

// List of connected clients
const clients = [];

// ---------------------------
// 1️⃣ SSE Endpoint
// ---------------------------
app.get("/events", (req, res) => {
  res.setHeader("Content-Type", "text/event-stream");
  res.setHeader("Cache-Control", "no-cache");
  res.setHeader("Connection", "keep-alive");

  res.write("data: Connected to SSE stream\n\n");

  // Add client connection
  clients.push(res);
  console.log("Client connected, total:", clients.length);

  req.on("close", () => {
    console.log("Client disconnected");
    clients.splice(clients.indexOf(res), 1);
  });
});

// ---------------------------
// 2️⃣ Simulate Sending Data Every 3s
// ---------------------------
setInterval(() => {
  const message = {
    time: new Date().toISOString(),
    random: Math.floor(Math.random() * 100),
  };
  clients.forEach((client) =>
    client.write(`data: ${JSON.stringify(message)}\n\n`)
  );
}, 3000);

app.listen(PORT, () =>
  console.log(`✅ SSE Server running on http://localhost:${PORT}`)
);
```

Now run it:

```bash
node server.js
```

---

## 🖥️ Step 2: Setting Up the React Client

If you don’t already have a React app:

```bash
npx create-react-app sse-client
cd sse-client
npm start
```

---

### Create `SSEStream.jsx`

```bash
import React, { useEffect, useState } from "react";

const SSEStream = () => {
  const [messages, setMessages] = useState([]);

  useEffect(() => {
    const eventSource = new EventSource("http://localhost:4000/events");

    eventSource.onmessage = (event) => {
      try {
        const data = JSON.parse(event.data);
        setMessages((prev) => [...prev, data]);
      } catch (e) {
        console.error("Invalid data:", e);
      }
    };

    eventSource.onerror = (err) => {
      console.error("SSE error:", err);
      eventSource.close();
    };

    return () => {
      eventSource.close();
    };
  }, []);

  return (
    <div style={{ padding: 20, fontFamily: "monospace" }}>
      <h2>📡 Live Server-Sent Events</h2>
      {messages.map((msg, i) => (
        <div key={i}>
          Time: {msg.time} | Random: {msg.random}
        </div>
      ))}
    </div>
  );
};

export default SSEStream;
```

---

### Add It in `App.js`

```bash
import React from "react";
import SSEStream from "./SSEStream";

function App() {
  return (
    <div>
      <h1>🚀 Real-Time Data Stream (SSE + React)</h1>
      <SSEStream />
    </div>
  );
}

export default App;
```

---

## 🧠 Step 3: Make It More Real — Integrating Redis

Now imagine your data is stored in Redis.  
You want to push updates to clients **whenever Redis changes** — without polling.

That’s where **Redis Pub/Sub** shines ✨

### Install Redis Package

```bash
npm install redis
```

### Updated Server (`server.js`)

```bash
import express from "express";
import cors from "cors";
import { createClient } from "redis";

const app = express();
app.use(cors());
const PORT = 4000;

// Redis setup
const redisClient = createClient();
const subscriber = redisClient.duplicate();
await redisClient.connect();
await subscriber.connect();

const clients = [];

app.get("/events", (req, res) => {
  res.setHeader("Content-Type", "text/event-stream");
  res.setHeader("Cache-Control", "no-cache");
  res.setHeader("Connection", "keep-alive");
  clients.push(res);
  req.on("close", () => clients.splice(clients.indexOf(res), 1));
});

await subscriber.subscribe("updates", (message) => {
  console.log("Redis message:", message);
  clients.forEach((res) => res.write(`data: ${message}\n\n`));
});

app.listen(PORT, () => console.log(`✅ SSE Server running on ${PORT}`));
```

### Publisher Example

```bash
// publisher.js
import { createClient } from "redis";
const publisher = createClient();
await publisher.connect();

setInterval(async () => {
  const data = JSON.stringify({
    time: new Date().toISOString(),
    value: Math.floor(Math.random() * 1000),
  });
  await publisher.publish("updates", data);
  console.log("Published:", data);
}, 3000);
```

Now every time your Redis publisher emits a message, **React instantly sees it** 🔥

---

## 🎯 When to Use SSE

✅ Perfect for:

* Live analytics dashboards
    
* Real-time logs or monitoring
    
* Notification systems
    
* Stock prices / crypto price feeds
    
* System health monitoring
    

❌ Not ideal for:

* Two-way chat (use WebSockets)
    
* Heavy concurrent updates
    

---

## 💬 Final Thoughts

Server-Sent Events (SSE) are an elegant, HTTP-friendly way to stream updates **from your server to browsers**.  
They’re simpler than WebSockets, and with Redis Pub/Sub, they scale beautifully in production.

In this tutorial, we:

* Built an **Express server** streaming live data
    
* Connected a **React frontend** using `EventSource`
    
* Enhanced it with **Redis Pub/Sub** for distributed real-time updates
    

Now you can build real-time dashboards, live notifications, or any app that thrives on **continuous data flow** ⚡

---

## 🔗 Bonus: Repo Structure Example

```bash
sse-demo/
│
├── server.js           # Express + Redis + SSE server
├── publisher.js        # Redis publisher
└── sse-client/         # React frontend
    ├── src/
    │   ├── App.js
    │   └── SSEStream.jsx
```