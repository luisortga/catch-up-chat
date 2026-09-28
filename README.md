<div align="center">

# Catch Up Chat

### Real-Time Chat Application with Node.js, Express, Socket.IO & Turso

<p>
  <img src="https://img.shields.io/badge/Node.js-24+-339933?style=for-the-badge&logo=node.js&logoColor=white" alt="Node.js">
  <img src="https://img.shields.io/badge/Express-5.x-000000?style=for-the-badge&logo=express&logoColor=white" alt="Express">
  <img src="https://img.shields.io/badge/Socket.IO-4.x-010101?style=for-the-badge&logo=socket.io&logoColor=white" alt="Socket.IO">
  <img src="https://img.shields.io/badge/Turso-libSQL-4FF8D2?style=for-the-badge&logo=turso&logoColor=black" alt="Turso">
  <img src="https://img.shields.io/badge/pnpm-11.x-F69220?style=for-the-badge&logo=pnpm&logoColor=white" alt="pnpm">
  <img src="https://img.shields.io/badge/JavaScript-ESM-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript">
</p>

<p>
  <img src="https://skillicons.dev/icons?i=nodejs,express,js,html,css" alt="Technology Stack">
</p>

<p>
  A lightweight real-time chat application built to explore<br>
  WebSockets, event-driven communication, persistent messages and Node.js backend development.
</p>

<p>
  <a href="https://catch-up-chat.seenode.app/">Live Demo</a>
  ·
  <a href="https://github.com/luisortga/catch-up-chat">Repository</a>
</p>

</div>

---

## Overview

**Catch Up Chat** is a real-time messaging application built with **Node.js**, **Express** and **Socket.IO**, with persistent message storage using **Turso/libSQL**.

The project was created as a practical exploration of real-time communication between clients and a backend server.

Instead of relying exclusively on traditional HTTP request/response cycles, the application uses **Socket.IO events** to send and receive messages instantly between connected clients.

The backend also persists messages in a SQL database, allowing new clients to recover previous messages when they connect.

---

## Application Preview

<div align="center">

<img src="https://i.ibb.co/DP9sRg38/Indice.png" width="350" alt="Catch Up Chat interface">

</div>

---

## Architecture

The application follows a simple event-driven architecture:

```text
┌─────────────────────┐
│      Browser        │
│                     │
│ HTML + CSS + JS     │
└──────────┬──────────┘
           │
           │ Socket.IO
           │
           ▼
┌─────────────────────┐
│     Node.js         │
│                     │
│     Express         │
│     Socket.IO       │
└──────────┬──────────┘
           │
           │ @libsql/client
           │
           ▼
┌─────────────────────┐
│   Turso / libSQL    │
│                     │
│      messages       │
└─────────────────────┘
```

### Message Flow

```text
User A
  │
  │ "Hello!"
  ▼
Socket.IO Server
  │
  ├──────────────► Turso
  │                 │
  │                 └── Store message
  │
  └──────────────► Connected Clients
                      │
                      ├── User A
                      ├── User B
                      └── User C
```

---

## Features

* Real-time messaging with Socket.IO
* Persistent message storage
* Automatic database table creation
* Message recovery for newly connected clients
* Socket.IO connection state recovery
* Automatic scrolling to the latest message
* Random username generation
* Username persistence using browser `localStorage`
* Static frontend served directly by Express
* Environment variable configuration
* Development mode with Node.js watch
* Lightweight architecture without a frontend framework

---

## Technology Stack

### Backend

<p>
  <img src="https://skillicons.dev/icons?i=nodejs,express,js" alt="Backend Technologies">
</p>

| Technology | Purpose                               |
| ---------- | ------------------------------------- |
| Node.js    | JavaScript runtime                    |
| Express    | HTTP server and static file serving   |
| Socket.IO  | Real-time bidirectional communication |
| Morgan     | HTTP request logging                  |
| dotenv     | Environment variable management       |

### Database

<p>
  <img src="https://skillicons.dev/icons?i=sqlite" alt="Database">
</p>

| Technology       | Purpose                                    |
| ---------------- | ------------------------------------------ |
| Turso            | Hosted SQLite-compatible database platform |
| libSQL           | SQLite-compatible database engine          |
| `@libsql/client` | Node.js client for database communication  |

### Frontend

<p>
  <img src="https://skillicons.dev/icons?i=html,css,js" alt="Frontend Technologies">
</p>

The frontend intentionally uses a lightweight stack:

* HTML5
* CSS3
* Vanilla JavaScript
* Socket.IO client

No frontend framework is required for this project.

### Development Tools

<p>
  <img src="https://skillicons.dev/icons?i=git,github,vscode" alt="Development Tools">
</p>

* Git
* GitHub
* VS Code
* pnpm
* ESLint

---

## Real-Time Communication

The main concept explored in this project is **event-driven communication**.

When a user sends a message, the browser emits a Socket.IO event:

```javascript
socket.emit('chat message', input.value)
```

The server listens for that event:

```javascript
socket.on('chat message', async (msg) => {
  // Store message
  // Broadcast message
})
```

After storing the message, the server broadcasts it to connected clients:

```javascript
io.emit('chat message', msg, result.lastInsertRowid.toString())
```

This creates a real-time communication flow without requiring the client to continuously poll the server.

---

## Database Persistence

Messages are stored in a `messages` table:

```sql
CREATE TABLE IF NOT EXISTS messages (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  content TEXT
);
```

The database is initialized automatically when the application starts.

### Message Persistence Flow

```text
Client
   │
   │ chat message
   ▼
Socket.IO
   │
   ▼
Turso / libSQL
   │
   │ INSERT
   ▼
messages
   │
   ▼
Socket.IO
   │
   │ broadcast
   ▼
Connected Clients
```

---

## Connection State Recovery

The Socket.IO server enables connection state recovery:

```javascript
const io = new Server(server, {
  connectionStateRecovery: {},
})
```

When a client reconnects, the application checks whether the connection state was recovered.

If it was not, the server retrieves messages newer than the client's last known offset:

```sql
SELECT id, content
FROM messages
WHERE id > ?
```

This allows the application to recover messages that may have been missed during a temporary disconnection.

---

## Client Identity

The frontend generates a lightweight username for each browser:

```javascript
const randomUsername = `user_${Math.random().toString(36).slice(2, 8)}
```

The username is stored using `localStorage`:

```javascript
localStorage.setItem('username', randomUsername)
```

This allows the same browser to retain its generated username between sessions.

---

## Frontend

The frontend is intentionally simple and framework-free.

```text
client/
├── index.html
└── style.css
```

The HTML page contains the chat interface, while JavaScript handles:

* Socket.IO connection
* Message events
* Form submission
* Username generation
* Local storage
* Automatic scrolling

The styling provides:

* Responsive chat container
* Message bubbles
* Custom scrollbar
* Focus states
* Hover effects
* Light theme
* Soft dark-mode support based on system preference
* Pistachio-inspired visual identity

---

## Project Structure

```text
catch-up-chat/
│
├── client/
│   ├── index.html
│   └── style.css
│
├── .gitignore
├── app.js
├── eslint.config.js
├── package.json
├── pnpm-lock.yaml
└── README.md
```

---

## Environment Variables

Create a `.env` file in the project root:

```env
PORT=3000

TURSO_DATABASE_URL=libsql://your-database-url.turso.io
DB_TOKEN=your-database-token
```

### Variables

| Variable             | Description                           |
| -------------------- | ------------------------------------- |
| `PORT`               | Port used by the Node.js server       |
| `TURSO_DATABASE_URL` | Turso/libSQL database URL             |
| `DB_TOKEN`           | Authentication token for the database |

> Never commit `.env` files or database credentials to GitHub.

---

## Installation

### Prerequisites

Make sure you have installed:

* Node.js
* pnpm
* A Turso database
* Git

### Clone the Repository

```bash
git clone https://github.com/luisortga/catch-up-chat.git

cd catch-up-chat
```

### Install Dependencies

```bash
pnpm install
```

### Configure Environment Variables

Create:

```text
.env
```

Then add your database configuration:

```env
PORT=3000
TURSO_DATABASE_URL=your_turso_database_url
DB_TOKEN=your_turso_database_token
```

### Start Development Server

```bash
pnpm dev
```

The application will be available at:

```text
http://localhost:3000
```

---

## Available Scripts

| Command        | Description                                          |
| -------------- | ---------------------------------------------------- |
| `pnpm install` | Install project dependencies                         |
| `pnpm dev`     | Start the development server with Node.js watch mode |
| `pnpm test`    | Placeholder test script                              |

The development command uses:

```bash
node --watch app.js
```

which automatically restarts the server when source files change.

---

## Deployment

The application is deployed and publicly accessible at:

**Live Application**

https://catch-up-chat.seenode.app/

The server listens on:

```javascript
const port = process.env.PORT ?? 3000
```

and binds to:

```text
0.0.0.0
```

This allows the application to work correctly in containerized or cloud-hosted environments.

---

## Backend Responsibilities

The Node.js server is responsible for:

```text
                    Node.js Server
                          │
          ┌───────────────┼───────────────┐
          │               │               │
          ▼               ▼               ▼
      Express         Socket.IO        Turso
          │               │               │
     Static Files     Real-Time       Persistence
          │           Messaging           │
          └───────────────┴───────────────┘
                          │
                          ▼
                       Client
```

This separation makes it possible to clearly understand the different responsibilities of each component.

---

## Concepts Practiced

This project was built to practice several backend and web development concepts:

### Node.js

* ES Modules
* Environment variables
* HTTP servers
* File serving
* Asynchronous programming

### Express

* Application setup
* Middleware
* Static file serving
* HTTP server integration

### Socket.IO

* WebSocket-based communication
* Events
* Broadcasting
* Client/server communication
* Connection handling
* Connection recovery

### Databases

* SQL
* SQLite/libSQL
* INSERT operations
* SELECT queries
* Auto-incrementing IDs
* Persistent application data

### Web Development

* HTML
* CSS
* JavaScript
* DOM manipulation
* `localStorage`
* Real-time UI updates

---

## Learning Architecture

The project can be summarized as:

```text
                   Web Application
                         │
          ┌──────────────┴──────────────┐
          │                             │
      Frontend                       Backend
          │                             │
    HTML / CSS / JS              Node.js / Express
          │                             │
          │                         Socket.IO
          │                             │
          └──────────────┬──────────────┘
                         │
                    Real-Time Events
                         │
                         ▼
                  Turso / libSQL
                         │
                         ▼
                    Persistent Data
```

---

## Why This Project?

This project focuses on understanding an important distinction in web development:

```text
Traditional HTTP

Client ───── Request ─────► Server
Client ◄──── Response ───── Server


Real-Time Communication

Client ◄──────► Socket.IO Server
   ▲                   │
   │                   │
   └──── Events ───────┘
```

Instead of treating every interaction as an independent HTTP request, Socket.IO allows the server and clients to maintain an ongoing communication channel.

This makes the project a practical introduction to **real-time backend systems and event-driven programming**.

---

## Future Improvements

Possible future improvements include:

* User authentication
* Persistent usernames
* Multiple chat rooms
* Private messaging
* Message timestamps
* Message deletion
* Message editing
* Online/offline user status
* Improved message ownership detection
* Input validation
* Automated tests
* Better error handling
* Rate limiting
* Production security hardening
* Containerization with Docker

---

## Author

Developed by **Luis Ortega**.

I'm primarily focused on **Backend Development**, while continuously learning **DevOps** and **Frontend Development**.

This project is part of my ongoing practice with:

```text
Backend
  │
  ├── Node.js
  ├── Express
  ├── REST APIs
  ├── WebSockets
  └── Databases
       │
       ▼
DevOps
  │
  ├── Linux
  ├── Docker
  ├── Deployment
  └── Cloud
       │
       ▼
Frontend
  │
  ├── JavaScript
  ├── HTML
  └── CSS
```

---

## Repository

<p align="center">

<a href="https://github.com/luisortga/catch-up-chat">
<img src="https://img.shields.io/badge/GitHub-catch--up--chat-181717?style=for-the-badge&logo=github&logoColor=white" alt="GitHub Repository">
</a>

<a href="https://catch-up-chat.seenode.app/">
<img src="https://img.shields.io/badge/Live-Demo-8FB935?style=for-the-badge&logo=googlechrome&logoColor=white" alt="Live Demo">
</a>

</p>

---

<div align="center">

### Node.js · Express · Socket.IO · Turso · JavaScript

**Building real-time systems one event at a time.**

</div>
