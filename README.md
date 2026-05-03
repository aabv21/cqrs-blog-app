# CQRS Blog Application

A modern blog platform implementing the **Command Query Responsibility Segregation (CQRS)** pattern with a microservices architecture — separating read and write operations into distinct services for better scalability and maintainability.

---

## Tech Stack

![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat-square&logo=nodedotjs&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Express.js](https://img.shields.io/badge/Express.js-000000?style=flat-square&logo=express&logoColor=white)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat-square&logo=mysql&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=flat-square&logo=mongodb&logoColor=white)
![RabbitMQ](https://img.shields.io/badge/RabbitMQ-FF6600?style=flat-square&logo=rabbitmq&logoColor=white)
![React](https://img.shields.io/badge/React-20232A?style=flat-square&logo=react&logoColor=61DAFB)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)
![Shadcn/UI](https://img.shields.io/badge/shadcn%2Fui-000000?style=flat-square&logo=shadcnui&logoColor=white)

---

## Architecture Overview

```
                        ┌─────────────────┐
                        │   React UI      │
                        │  (Port 5173)    │
                        └────────┬────────┘
                                 │
                        ┌────────▼────────┐
                        │   API Gateway   │  ← Single entry point
                        │   (Port 3000)   │
                        └────────┬────────┘
                                 │
              ┌──────────────────┼──────────────────┐
              │ Write ops        │                  │ Read ops
              │ POST/PUT/DELETE  │                  │ GET
    ┌─────────▼─────────┐       │       ┌───────────▼──────────┐
    │  Commands Service │       │       │   Queries Service    │
    │   (Port 3001)     │       │       │    (Port 3002)       │
    │                   │       │       │                      │
    │  MySQL (writes)   │──────RabbitMQ─▶  MongoDB (reads)    │
    └───────────────────┘  events bus   └──────────────────────┘
```

### Services

**API Gateway** `Port 3000`
- Single entry point and reverse proxy for all client requests
- Routes writes → Commands Service, reads → Queries Service
- Handles CORS and request logging

**Commands Service** `Port 3001`
- Handles all write operations (create/delete posts and comments)
- MySQL for transactional data storage with strict validation
- Publishes domain events to RabbitMQ on every state change

**Queries Service** `Port 3002`
- Handles all read operations with pagination and filtering
- MongoDB optimized for fast reads
- Subscribes to RabbitMQ events to stay in sync with Commands

**UI Service** `Port 5173`
- React + TypeScript frontend with Shadcn/UI components
- Event-driven real-time updates
- Responsive and mobile-friendly

---

## Key Features

- ✍️ **CQRS pattern** — strict read/write model separation
- 📨 **Event-driven sync** — RabbitMQ keeps read/write models consistent asynchronously
- 🗄️ **Polyglot persistence** — MySQL for writes, MongoDB for reads
- 🔀 **API Gateway** — single entry point with intelligent request routing
- ✅ **Validation** — business rules enforced at the Commands layer
- 🎨 **Modern UI** — React + Shadcn/UI + Tailwind CSS

---

## API Endpoints

### Posts
| Method | Endpoint | Service | Description |
|---|---|---|---|
| `GET` | `/api/posts` | Queries | Get all posts |
| `GET` | `/api/posts/:id` | Queries | Get post with comments |
| `POST` | `/api/posts` | Commands | Create a new post |
| `DELETE` | `/api/posts/:id` | Commands | Delete a post |

### Comments
| Method | Endpoint | Service | Description |
|---|---|---|---|
| `POST` | `/api/posts/:id/comments` | Commands | Add a comment |
| `DELETE` | `/api/posts/:postId/comments/:commentId` | Commands | Delete a comment |

---

## Getting Started

**Prerequisites:** Node.js, MySQL, MongoDB, RabbitMQ

```bash
# Clone the repository
git clone https://github.com/aabv21/cqrs-blog-app.git
cd cqrs-blog-app

# Install dependencies for each service
cd api-gateway && npm install
cd ../commands && npm install
cd ../queries && npm install
cd ../ui && npm install
```

**Environment variables** — create `.env` in each service:

```env
# Commands Service
MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_USER=root
MYSQL_PASSWORD=your_password
MYSQL_DB=cqrs_commands
RABBITMQ_URL=amqp://localhost

# Queries Service
MONGODB_URL=mongodb://localhost:27017/cqrs_queries
RABBITMQ_URL=amqp://localhost
```

**Start all services** (each in a separate terminal):

```bash
cd api-gateway && npm start   # http://localhost:3000
cd commands && npm start      # http://localhost:3001
cd queries && npm start       # http://localhost:3002
cd ui && npm start            # http://localhost:5173
```

Open [http://localhost:5173](http://localhost:5173) to view the app.

---

## Related Projects

- [photo-post](https://github.com/aabv21/photo-post) — Microservices with Kafka & Redis
- [microservices-js-node](https://github.com/aabv21/microservices-js-node) — Node.js microservices with Kubernetes

---

<div align="center">
  <sub>Built by <a href="https://github.com/aabv21">Andrés Buelvas</a> · Full Stack Engineer</sub>
</div>
