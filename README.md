# Fault-Tolerant Distributed Storage System

A fault-tolerant distributed storage system ensuring data replication and high availability.

This project implements a Coordinator + Storage Nodes architecture where files are chunked, replicated across nodes, and recovered even if a node fails.

### Features
- File chunking and replication
- Fault tolerance (node failure recovery)
- Coordinator-based management
- Simple web UI (in /public)

### Tech Stack
- Node.js
- Express.js

### How to Run

1. Install dependencies:
   npm install

2. Start all services with single command:
   npm run start-all

3. Open browser at http://localhost:4000

### Project Structure
- coordinator.js - Main coordinator server
- storageNode.js - Storage node logic
- public/ - Frontend UI
- data/ - Auto-generated chunk storage (ignored in git)