# Vault: Fault-Tolerant Distributed Object Storage

A fault-tolerant distributed storage system that replicates every file across multiple storage nodes, detects failures and data corruption, and repairs itself automatically.

**Live Demo:** https://fault-tolerant-storage-system.onrender.com

> Hosted on a free tier, so the first load may take 30-50 seconds to wake up.

## Features

- **Replication:** every uploaded file is stored on all 3 storage nodes
- **Fault tolerance:** downloads are served from any available node, even if others are down
- **Integrity checking:** a SHA-256 checksum is stored with every file and verified on demand
- **Self-healing:** a background checker runs every 5 seconds and restores missing or corrupted replicas from a healthy copy
- **Health monitoring:** live node status (up/down) with failure and recovery events
- **Recovery log:** dashboard tab showing uploads, failures, corruption and repairs in real time
- **Failure simulation:** built-in endpoint to corrupt a replica and watch the system repair it

## Architecture

```
            Client (Web Dashboard)
                     |
               Coordinator :4000
              /       |        \
        node1 :3001  node2 :3002  node3 :3003
```

- **Coordinator:** handles uploads, downloads, metadata, health checks and auto-repair
- **Storage nodes:** independent Express servers, each with its own data folder, storing files and checksums

## Tech Stack

- Node.js
- Express.js
- Axios
- HTML, CSS, JavaScript (dashboard)

## How to Run Locally

1. Install dependencies:
   ```
   npm install
   ```
2. Start the coordinator and all 3 nodes together:
   ```
   npm run start-all
   ```
3. Open http://localhost:4000

## API Endpoints

**Coordinator**

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/upload` | Upload a file and replicate it to all nodes |
| GET | `/download/:fileId` | Download from any available node |
| GET | `/status` | Cluster health and per-file replica status |
| GET | `/events` | Recovery and activity log |
| POST | `/simulate-corrupt/:nodeId/:fileId` | Corrupt a replica for demo |

**Storage node:** `/health`, `/store/:fileId`, `/retrieve/:fileId`, `/checksum/:fileId`, `/list`, `/delete/:fileId`

## Demo Walkthrough

1. Upload a file. It shows as healthy on node1, node2 and node3.
2. Corrupt a replica using `/simulate-corrupt/node2/<fileId>`.
3. Within 5 seconds the checker detects the checksum mismatch and repairs it.
4. Check the Recovery tab to see the repair event.

## Project Structure

```
coordinator.js    Coordinator server (replication, health check, repair)
storageNode.js    Storage node server
public/           Web dashboard
data/             Auto-generated node storage (git-ignored)
```

## Limitations

- Metadata is stored in memory (a production system would use a database or a consensus layer such as Raft)
- On the hosted demo, all nodes run on a single instance with temporary disk, so data resets on restart
