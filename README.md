# Todo List Application - Full Stack with Kubernetes

A production-ready todo list application with a Next.js frontend, Express.js backend, and PostgreSQL database, designed to be deployed on Kubernetes with multiple replicas for high availability.

## 📋 Table of Contents

- [Features](#features)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Kubernetes Deployment](#kubernetes-deployment)
- [API Documentation](#api-documentation)
- [Database Schema](#database-schema)

## ✨ Features

### Core Features
- ✅ Create, read, update, and delete todos
- ✅ Mark todos as complete/incomplete
- ✅ Set priority levels (Low, Medium, High)
- ✅ Add due dates to tasks
- ✅ Filter todos by status (All, Active, Completed)
- ✅ Search todos by title or description
- ✅ Real-time updates with React Query
- ✅ Responsive design for mobile and desktop
- ✅ Clean, modern UI with Tailwind CSS

### Production Features
- ✅ Kubernetes-ready with multiple replicas
- ✅ Health checks and readiness probes
- ✅ Horizontal pod autoscaling support
- ✅ Rolling updates with zero downtime
- ✅ PostgreSQL with persistent storage
- ✅ Connection pooling for database efficiency
- ✅ Environment-based configuration
- ✅ Docker containerization

## 🏗️ Architecture

```
┌─────────────────┐
│   Ingress       │
│  (todolist.local)│
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
┌───▼──────┐ ┌▼──────────┐
│ Frontend │ │  Backend  │
│ (Next.js)│ │ (Express) │
│ 2 replicas│ │ 3 replicas│
└──────────┘ └─────┬─────┘
                   │
            ┌──────▼─────────┐
            │   PostgreSQL   │
            │  (StatefulSet) │
            └────────────────┘
```

## 🛠️ Tech Stack

### Frontend
- **Framework**: Next.js 14 (App Router)
- **Language**: TypeScript
- **Styling**: Tailwind CSS
- **State Management**: TanStack React Query
- **HTTP Client**: Axios

### Backend
- **Framework**: Express.js
- **Language**: TypeScript
- **Database Client**: node-postgres (pg)
- **Validation**: express-validator
- **Logger**: Morgan

### Database
- **Database**: PostgreSQL 15
- **Features**: Indexed queries, connection pooling

### DevOps
- **Containerization**: Docker (multi-stage builds)
- **Orchestration**: Kubernetes
- **Deployment**: Rolling updates
- **Ingress**: NGINX Ingress Controller

## 📁 Project Structure

```
.
├── devops-bootcamp-todolist-backend-api/
│   ├── src/
│   │   ├── config/
│   │   │   └── database.ts          # PostgreSQL connection pool
│   │   ├── controllers/
│   │   │   └── todoController.ts    # Business logic
│   │   ├── models/
│   │   │   └── todoModel.ts         # Database queries
│   │   ├── routes/
│   │   │   └── todoRoutes.ts        # API routes
│   │   └── server.ts                # Express app setup
│   ├── Dockerfile                   # Backend container image
│   ├── package.json
│   └── tsconfig.json
│
├── devops-bootcamp-todolist-frontend/
│   ├── src/
│   │   ├── app/
│   │   │   ├── layout.tsx           # Root layout with QueryClient
│   │   │   ├── page.tsx             # Main page
│   │   │   └── globals.css          # Global styles
│   │   ├── components/
│   │   │   ├── TodoForm.tsx         # Add todo form
│   │   │   ├── TodoItem.tsx         # Todo item display
│   │   │   └── FilterBar.tsx        # Filter & search
│   │   └── lib/
│   │       ├── api.ts               # API client
│   │       └── types.ts             # TypeScript types
│   ├── Dockerfile                   # Frontend container image
│   ├── package.json
│   ├── next.config.js
│   └── tailwind.config.ts
│
└── k8s/
    ├── namespace.yaml               # Namespace definition
    ├── postgres/
    │   ├── secret.yaml              # Database credentials
    │   ├── statefulset.yaml         # PostgreSQL deployment
    │   └── service.yaml             # Database service
    ├── backend/
    │   ├── configmap.yaml           # Backend config
    │   ├── deployment.yaml          # Backend deployment (3 replicas)
    │   └── service.yaml             # Backend service
    ├── frontend/
    │   ├── configmap.yaml           # Frontend config
    │   ├── deployment.yaml          # Frontend deployment (2 replicas)
    │   └── service.yaml             # Frontend service
    └── ingress.yaml                 # Ingress configuration
```

## 🚀 Getting Started

### Prerequisites
- Node.js 20+
- PostgreSQL 15+
- Docker (for containerization)
- Kubernetes cluster (for K8s deployment)

### Local Development

#### 1. Setup Database

```bash
# Create database
createdb todolist

# Or using psql
psql -U postgres
CREATE DATABASE todolist;
```

#### 2. Backend Setup

```bash
cd devops-bootcamp-todolist-backend-api

# Install dependencies
npm install

# Create .env file
cp .env.example .env

# Edit .env with your database credentials
# DATABASE_HOST=localhost
# DATABASE_PORT=5432
# DATABASE_NAME=todolist
# DATABASE_USER=postgres
# DATABASE_PASSWORD=postgres
# PORT=5000
# NODE_ENV=development

# Run development server
npm run dev
```

Backend will be available at `http://localhost:5000`

#### 3. Frontend Setup

```bash
cd devops-bootcamp-todolist-frontend

# Install dependencies
npm install

# Create .env.local file
echo "NEXT_PUBLIC_API_URL=http://localhost:5000/api" > .env.local

# Run development server
npm run dev
```

Frontend will be available at `http://localhost:3000`

## ☸️ Kubernetes Deployment

### Prerequisites
- Kubernetes cluster (local or cloud)
- kubectl configured
- Docker for building images

### Step 1: Build Docker Images

```bash
# Build backend image
cd devops-bootcamp-todolist-backend-api
docker build -t todo-backend:latest .

# Build frontend image
cd ../devops-bootcamp-todolist-frontend
docker build -t todo-frontend:latest .
```

### Step 2: Deploy to Kubernetes

```bash
# Create namespace
kubectl apply -f k8s/namespace.yaml

# Deploy PostgreSQL
kubectl apply -f k8s/postgres/

# Deploy Backend
kubectl apply -f k8s/backend/

# Deploy Frontend
kubectl apply -f k8s/frontend/

# Deploy Ingress
kubectl apply -f k8s/ingress.yaml
```

### Step 3: Verify Deployment

```bash
# Check all pods are running
kubectl get pods -n todolist

# Check services
kubectl get svc -n todolist

# Check ingress
kubectl get ingress -n todolist
```

### Step 4: Access the Application

Add to your `/etc/hosts`:
```
127.0.0.1 todolist.local
```

Access the application at `http://todolist.local`

### Scaling

```bash
# Scale backend
kubectl scale deployment backend -n todolist --replicas=5

# Scale frontend
kubectl scale deployment frontend -n todolist --replicas=3
```

## 📚 API Documentation

### Endpoints

#### Get All Todos
```http
GET /api/todos
Query Parameters:
  - completed: boolean (optional)
  - priority: low|medium|high (optional)
  - search: string (optional)
```

#### Get Single Todo
```http
GET /api/todos/:id
```

#### Create Todo
```http
POST /api/todos
Body:
{
  "title": "string (required)",
  "description": "string (optional)",
  "priority": "low|medium|high (optional, default: medium)",
  "due_date": "ISO 8601 date string (optional)"
}
```

#### Update Todo
```http
PUT /api/todos/:id
Body:
{
  "title": "string (optional)",
  "description": "string (optional)",
  "completed": "boolean (optional)",
  "priority": "low|medium|high (optional)",
  "due_date": "ISO 8601 date string (optional)"
}
```

#### Toggle Todo Completion
```http
PATCH /api/todos/:id/toggle
```

#### Delete Todo
```http
DELETE /api/todos/:id
```

### Health Checks

```http
GET /health        # Liveness probe
GET /ready         # Readiness probe (checks DB connection)
```

## 🗄️ Database Schema

```sql
CREATE TABLE todos (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    completed BOOLEAN DEFAULT false,
    priority VARCHAR(20) CHECK (priority IN ('low', 'medium', 'high')) DEFAULT 'medium',
    due_date TIMESTAMP,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes for performance
CREATE INDEX idx_todos_completed ON todos(completed);
CREATE INDEX idx_todos_due_date ON todos(due_date);
CREATE INDEX idx_todos_priority ON todos(priority);
```

## 🔧 Configuration

### Backend Environment Variables
- `DATABASE_HOST`: PostgreSQL host
- `DATABASE_PORT`: PostgreSQL port (default: 5432)
- `DATABASE_NAME`: Database name
- `DATABASE_USER`: Database user
- `DATABASE_PASSWORD`: Database password
- `PORT`: Backend port (default: 5000)
- `NODE_ENV`: Environment (development/production)

### Frontend Environment Variables
- `NEXT_PUBLIC_API_URL`: Backend API URL

## 📝 Features in Detail

### Priority Levels
- **High**: Red color indicator, urgent tasks
- **Medium**: Yellow color indicator, normal priority
- **Low**: Green color indicator, low priority

### Filtering
- **All**: Show all todos
- **Active**: Show only incomplete todos
- **Completed**: Show only completed todos

### Search
Real-time search across todo titles and descriptions

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request

## 📄 License

This project is licensed under the ISC License.

## 👨‍💻 Author

Created for DevOps Bootcamp

## 🙏 Acknowledgments

- Next.js team for the amazing framework
- Express.js community
- PostgreSQL contributors
- Kubernetes community
