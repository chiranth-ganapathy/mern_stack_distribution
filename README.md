# 📋 Admin–Agent Task Distribution System

A production-ready MERN stack application where admins can manage agents, upload CSV/Excel contact lists, and automatically distribute tasks equally among agents.

---

## 🏗️ Project Structure

```
task-distribution/
├── backend/
│   ├── config/
│   │   └── db.js                 # MongoDB connection
│   ├── controllers/
│   │   ├── authController.js     # Login logic
│   │   ├── agentController.js    # Agent CRUD
│   │   └── taskController.js     # File upload & distribution
│   ├── middleware/
│   │   ├── auth.js               # JWT verification middleware
│   │   └── upload.js             # Multer file upload config
│   ├── models/
│   │   ├── Admin.js              # Admin schema
│   │   ├── Agent.js              # Agent schema
│   │   └── Task.js               # Task schema
│   ├── routes/
│   │   ├── authRoutes.js
│   │   ├── agentRoutes.js
│   │   └── taskRoutes.js
│   ├── utils/
│   │   ├── fileParser.js         # CSV/XLSX parsing + distribution algorithm
│   │   ├── generateToken.js      # JWT token generator
│   │   └── seedAdmin.js          # Admin seeder script
│   ├── .env.example
│   ├── package.json
│   └── server.js                 # Express entry point
│
├── frontend/
│   ├── public/
│   │   └── index.html
│   ├── src/
│   │   ├── components/
│   │   │   ├── Layout.js         # App shell (sidebar + topbar)
│   │   │   ├── ProtectedRoute.js # Auth guard
│   │   │   └── Sidebar.js        # Navigation
│   │   ├── context/
│   │   │   └── AuthContext.js    # Auth state & login/logout
│   │   ├── pages/
│   │   │   ├── LoginPage.js      # Admin + Agent login
│   │   │   ├── Dashboard.js      # Admin dashboard
│   │   │   ├── Agents.js         # Agent management
│   │   │   ├── Upload.js         # File upload page
│   │   │   ├── Tasks.js          # All tasks grouped by agent
│   │   │   └── AgentDashboard.js # Agent task view
│   │   ├── styles/
│   │   │   └── global.css        # Complete UI styles
│   │   ├── utils/
│   │   │   └── api.js            # Axios instance
│   │   └── index.js              # React entry point
│   ├── .env.example
│   └── package.json
│
├── sample_tasks.csv              # Test upload file (13 contacts)
├── package.json                  # Root (concurrently runner)
└── README.md
```

---

## ✅ Features

| Feature | Description |
|---|---|
| 🔑 Admin Login | JWT-based login with bcrypt password hashing |
| 👤 Agent Login | Separate agent portal with own dashboard |
| 👥 Agent Management | Add, list, delete agents with country code |
| 📤 File Upload | CSV / XLSX / XLS parsing with validation |
| ⚡ Auto Distribution | Equal task distribution with remainder handling |
| 📋 Task Tracking | Agents can update task status (pending / in-progress / completed) |
| 🔒 Route Protection | Admin and agent routes fully secured with JWT |

---

## 🚀 Local Setup

### Prerequisites

- Node.js v18+
- MongoDB (local or MongoDB Atlas)
- npm or yarn

---

### 1. Clone / Extract

```bash
cd task-distribution
```

### 2. Backend Setup

```bash
cd backend
cp .env.example .env
# Edit .env with your MongoDB URI and desired credentials
npm install
```

### 3. Seed the Admin User

```bash
npm run seed
# Creates admin@example.com / Admin@123456 (or your .env values)
```

### 4. Start Backend

```bash
npm run dev
# Server runs on http://localhost:5000
```

### 5. Frontend Setup (new terminal)

```bash
cd frontend
cp .env.example .env
npm install
npm start
# App opens at http://localhost:3000
```

---

## ⚙️ Environment Variables

### Backend `.env`

```env
MONGODB_URI=mongodb://localhost:27017/task_distribution
JWT_SECRET=your_super_secret_key_change_this
JWT_EXPIRES_IN=7d
PORT=5000
FRONTEND_URL=http://localhost:3000
ADMIN_EMAIL=admin@example.com
ADMIN_PASSWORD=Admin@123456
ADMIN_NAME=Super Admin
```

### Frontend `.env`

```env
REACT_APP_API_URL=http://localhost:5000/api
```

---

## 📡 API Endpoints

### Auth
| Method | Route | Access | Description |
|---|---|---|---|
| POST | `/api/auth/admin/login` | Public | Admin login |
| POST | `/api/auth/agent/login` | Public | Agent login |
| GET | `/api/auth/me` | Protected | Get current user |

### Agents (Admin only)
| Method | Route | Description |
|---|---|---|
| GET | `/api/agents` | List all agents |
| POST | `/api/agents` | Create agent |
| GET | `/api/agents/:id` | Get single agent |
| DELETE | `/api/agents/:id` | Delete agent |

### Tasks
| Method | Route | Access | Description |
|---|---|---|---|
| POST | `/api/tasks/upload` | Admin | Upload & distribute file |
| GET | `/api/tasks` | Admin | All tasks grouped by agent |
| GET | `/api/tasks/agent/:id` | Admin | Tasks for specific agent |
| GET | `/api/tasks/batches` | Admin | Upload history |
| GET | `/api/tasks/my-tasks` | Agent | Agent's own tasks |
| PATCH | `/api/tasks/:id/status` | Agent | Update task status |

---

## ⚡ Distribution Algorithm

```
Tasks: 13   Agents: 5

Base share  = Math.floor(13 / 5) = 2
Remainder   = 13 % 5             = 3

Agent 1 (idx 0, < remainder): 2 + 1 = 3 tasks
Agent 2 (idx 1, < remainder): 2 + 1 = 3 tasks
Agent 3 (idx 2, < remainder): 2 + 1 = 3 tasks
Agent 4 (idx 3, >= remainder): 2 tasks
Agent 5 (idx 4, >= remainder): 2 tasks

Total: 3+3+3+2+2 = 13 ✅
```

Tasks are assigned in file order — first N tasks to Agent 1, next N to Agent 2, etc.

---

## 🧪 Testing Upload

Use the included `sample_tasks.csv` (13 rows) to test distribution. With 5 agents:
- 3 agents get 3 tasks each
- 2 agents get 2 tasks each

---

## 🔐 Security

- Passwords hashed with bcrypt (12 rounds)
- JWT tokens expire after 7 days
- All sensitive routes protected by middleware
- CORS restricted to frontend origin
- File type validation on both client and server
- File size limited to 10MB

---

## 🏭 Production Deployment

**Backend:** Deploy to Railway, Render, or AWS EC2. Set `NODE_ENV=production`.

**Frontend:** `npm run build` → deploy `build/` to Netlify, Vercel, or S3.

**Database:** Use MongoDB Atlas with IP whitelisting.
