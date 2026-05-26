# PyScape-Basic 🎓

**The foundational single-agent adaptive learning system for Python & AI.**

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/yourusername/pyscape-basic)](https://github.com/yourusername/pyscape-basic)
[![Contributions Welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg)](CONTRIBUTING.md)

## What is PyScape-Basic?

PyScape-Basic is a **clean, foundational implementation** of the PyScape adaptive learning ecosystem. It's designed to teach Python, AI, Machine Learning, and Data Science through personalized learning paths, real-time coding challenges, interactive visualizations, and guided projects.

### This is the **base version** because it:
✅ Has clear, modular architecture  
✅ Teaches core concepts of adaptive learning systems  
✅ Serves as the foundation for research variants and multi-agent extensions  
✅ Prioritizes educational clarity over complex orchestration  

### Looking for something else?
- **Multi-agent orchestration?** → See [pyscape-multi-agent](https://github.com/zxnb01/pyscape-multi-agent)
- **Research benchmarks?** → See [pyscape-research](https://github.com/zxnb01/pyscape-research)

---

## 🚀 Core Features

### 1. **Adaptive Learning Platform**
- 📚 Personalized learning roadmaps based on interests
- 🎮 Gamified progression (XP, levels, achievements)
- 🎯 Topic-driven skill tree for Python + AI domains

### 2. **Real-Time Code Duels** ⚔️
- 👥 Live 1v1 competitive coding battles
- 🎯 Skill-based matchmaking by difficulty and language
- 🔗 WebSocket-powered real-time updates
- 🏆 Leaderboard rankings and statistics

### 3. **Algorithm Visualizers**
- 🔄 Sorting (Bubble, Quick, Merge, Heap)
- 🗺️ Pathfinding (A*, Dijkstra, BFS, DFS)
- 📊 K-Means Clustering
- 🧠 Neural Network Architecture
- ⬇️ Gradient Descent Optimization
- 🌳 Data Structures (trees, graphs, heaps)

### 4. **ML Sandbox**
- 🐍 Python with NumPy, Pandas, Matplotlib
- ☕ JavaScript (browser-based)
- ☕ Java with full library support
- ⚙️ C/C++ compilation and execution
- 💾 Interactive code execution with stdin/stdout

### 5. **Guided Project Labs**
- 🎓 Step-by-step project walkthroughs
- 📈 Performance metrics and visualization
- 🎓 Project completion tracking

### 6. **Portfolio Generation**
- 📄 Markdown and PDF export
- 🏅 Showcase completed projects
- 📊 Display skills and achievements
- 📤 Shareable portfolio links

---

## 🏗️ Architecture

**Frontend:** React + Tailwind CSS + Framer Motion  
**Backend:** Node.js Express + WebSockets  
**Database:** Supabase (PostgreSQL + Real-time)  
**Code Execution:** Judge0 API  
**Authentication:** Supabase Auth (JWT)  

### System Architecture

```
┌─────────────────────┐       ┌──────────────────────┐
│  Frontend (React)   │       │ Backend (Express)    │
│                     │◄─────►│                      │
│ Pages (Feature-org) │  HTTP │ REST API Routes      │
│ Components          │       │ Services             │
│ Context/Hooks       │       │ Models               │
└─────────────────────┘       └──────────────────────┘
         │                            │
         └────────────────┬───────────┘
                          │
                   ┌──────▼──────┐
                   │  Supabase   │
                   │ PostgreSQL  │
                   │   Real-time │
                   └─────────────┘
```

See [ARCHITECTURE.md](docs/ARCHITECTURE.md) for full system design.

---

## ⚡ Quick Start

### Prerequisites
- Node.js v16+
- npm or yarn
- Supabase account (free tier works)

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/pyscape-basic.git
cd pyscape-basic
```

2. **Create environment file**
```bash
cp .env.example .env
# Fill in your Supabase and Judge0 credentials
```

3. **Install dependencies and start**

**Terminal 1 - Frontend:**
```bash
cd frontend
npm install
npm start
```

**Terminal 2 - Backend REST API:**
```bash
cd backend
npm install
npm run dev
```

**Terminal 3 - Backend WebSocket:**
```bash
cd backend
npm run dev:duel
```

Visit `http://localhost:3000` → Sign up → Select topics → Start learning! 🚀

---

## 📚 Documentation

- **[DEVELOPMENT.md](docs/DEVELOPMENT.md)** — Setup, running, and debugging
- **[ARCHITECTURE.md](docs/ARCHITECTURE.md)** — System design and data flow
- **[API.md](docs/API.md)** — REST and WebSocket API reference
- **[DATABASE.md](docs/DATABASE.md)** — Schema and migrations
- **[FEATURES/](docs/FEATURES/)** — Feature-specific documentation
- **[CONTRIBUTING.md](CONTRIBUTING.md)** — How to contribute

---

## 🗂️ Project Structure

```
pyscape-basic/
├── frontend/              # React frontend application
│   └── src/
│       ├── pages/        # Feature-based pages
│       ├── components/   # Reusable UI components
│       ├── features/     # Feature-specific logic
│       ├── services/     # API and Supabase clients
│       └── utils/        # Helper functions
├── backend/               # Express.js API
│   └── src/
│       ├── routes/       # API endpoints and WebSocket handlers
│       ├── services/     # Business logic
│       ├── models/       # Database models
│       └── config/       # Configuration
├── database/              # Database schema and migrations
├── docs/                  # Comprehensive documentation
├── .github/               # GitHub templates and workflows
└── scripts/               # Utility scripts
```

See [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) for full structure details.

---

## 🎮 How It Works

### User Journey: Onboarding → Learning → Dueling → Portfolio

```
1. Sign Up (Auth.js)
   └─> Supabase Auth created

2. Select Topics (TopicSelection.js)
   └─> Roadmap generated based on interests

3. Start Learning (Dashboard.js → Learn.js)
   └─> Complete lessons (reading, quiz, coding)
   └─> Earn XP and unlock levels

4. Practice with Code Duels (CodeDuel.js)
   └─> Real-time 1v1 battles
   └─> Climb leaderboard
   └─> Earn competitive XP

5. Build Portfolio (ProjectLabs.js → Portfolio.js)
   └─> Complete guided projects
   └─> Generate and export portfolio

6. Share (Portfolio.js)
   └─> Download as PDF
   └─> Share link with recruiters/peers
```

---

## 🤖 Real-Time Features

### Code Duels (WebSocket)
- **Matchmaking** — Find opponent with similar skill level
- **Live Progress** — See opponent's test pass count
- **Real-time Chat** — Communicate during duel
- **Instant Results** — Code execution via Judge0 API
- **Leaderboard** — Global rankings updated live

### Algorithm Visualizers
- Step-by-step visualization of algorithms
- Play/pause/speed controls
- Compare different approaches
- Educational explanations

---

## 🔧 Configuration

See [DEVELOPMENT.md](docs/DEVELOPMENT.md) for detailed setup.

**Key environment variables:**
```env
# Supabase
REACT_APP_SUPABASE_URL=
REACT_APP_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=

# Judge0 API (for code execution)
JUDGE0_API_HOST=
JUDGE0_API_KEY=

# Backend
BACKEND_PORT=5000
WS_PORT=8080
NODE_ENV=development
```

---

## 📊 Learning Path Example

**User selects:** Python, Machine Learning, Data Science

**Generated roadmap:**
```
Level 1: Python Basics
├─ Intro to Python (reading)
├─ Variables & Types (quiz)
└─ Control Flow (coding problem)

Level 2: Data Science Fundamentals
├─ NumPy Essentials (reading)
├─ Pandas Tutorial (coding lab)
└─ Data Visualization (interactive)

Level 3: Machine Learning
├─ Supervised Learning (reading)
├─ Build Classifier (project lab)
└─ Evaluate Model (guided)

Level 4: Applied AI
├─ Neural Networks (reading)
├─ Train & Deploy (project)
└─ Capstone Project (showcase)
```

Users progress through lessons, earn XP, and unlock projects!

---

## 🎯 Design Principles

1. **Clarity** — Code and documentation are clear and easy to follow
2. **Modularity** — Features are independent and reusable
3. **Scalability** — Architecture supports growth and research variants
4. **Maintainability** — Clean separation of concerns
5. **Educational** — Suitable for learning and teaching system design

---

## 🚀 Deployment

### Deploy Frontend (Vercel)
```bash
cd frontend
npm run build
# Deploy build/ folder to Vercel
```

### Deploy Backend (Heroku, Railway, etc.)
```bash
cd backend
# Push to hosting platform
# Set environment variables in dashboard
```

### Database (Supabase)
- Already cloud-hosted
- Automatic backups
- Real-time subscriptions included

See [docs/DEVELOPMENT.md](docs/DEVELOPMENT.md) for detailed deployment steps.

---

## 🤝 Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for:
- Code style guide
- Commit message format
- Pull request process
- Bug report template
- Feature request template

**Ways to contribute:**
- 💻 Code improvements
- 📚 Documentation
- 🐛 Bug reports
- 💡 Feature suggestions
- 🎨 UI/UX feedback

---

## 📈 Roadmap

**Phase 2:** Advanced matchmaking, expanded content, engagement features  
**Phase 3:** Community features, instructor tools, public portfolios  
**Phase 4:** AI tutor, advanced analytics, adaptive difficulty  
**Phase 5:** Mobile app, IDE extensions, research ecosystem  

See [ROADMAP.md](ROADMAP.md) for detailed plans.

---

## 🔒 Security

- ✅ Supabase Auth with JWT tokens
- ✅ Row-Level Security (RLS) on all tables
- ✅ Code execution in isolated containers (Judge0)
- ✅ No API keys exposed in frontend
- ✅ CORS protection on backend

**Reporting security issues:** Please email security@pyscape.dev (or contact maintainers privately)

---

## 📝 License

MIT License — See [LICENSE](LICENSE) for details.

---

## 🙌 Acknowledgments

Built with:
- [React](https://react.dev)
- [Express.js](https://expressjs.com)
- [Supabase](https://supabase.io)
- [Tailwind CSS](https://tailwindcss.com)
- [Framer Motion](https://www.framer.com/motion)
- [Judge0 API](https://judge0.com)
- [D3.js](https://d3js.org)

---

## 📞 Support

- **Documentation:** See [docs/](docs/)
- **Issues:** [GitHub Issues](https://github.com/yourusername/pyscape-basic/issues)
- **Discussions:** [GitHub Discussions](https://github.com/yourusername/pyscape-basic/discussions)
- **Email:** connect @zxnb01

---

## 🌟 Show Your Support

If this project helps you, please:
- ⭐ Star the repository
- 🔗 Share with others
- 📝 Leave feedback
- 🚀 Contribute!

---

**Start your learning journey today!** 🚀

[Get Started](docs/DEVELOPMENT.md) | [Features](docs/FEATURES/) | [API Docs](docs/API.md) | [Architecture](docs/ARCHITECTURE.md)
