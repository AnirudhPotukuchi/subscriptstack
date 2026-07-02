# SubscriptStack: College-Exclusive Social Network

[![Vue 3](https://img.shields.io/badge/Frontend-Vue%203-4fc08d?style=flat-square&logo=vue.js)](https://vuejs.org/)
[![Vite](https://img.shields.io/badge/Bundler-Vite-646cff?style=flat-square&logo=vite)](https://vitejs.dev/)
[![Tailwind CSS v4](https://img.shields.io/badge/Styling-Tailwind%20CSS%20v4-38bdf8?style=flat-square&logo=tailwind-css)](https://tailwindcss.com/)
[![Express](https://img.shields.io/badge/Backend-Express.js-000000?style=flat-square&logo=express)](https://expressjs.com/)
[![Firebase Admin](https://img.shields.io/badge/Database-Firebase/Firestore-FFCA28?style=flat-square&logo=firebase)](https://firebase.google.com/)
[![TypeScript](https://img.shields.io/badge/Language-TypeScript-3178c6?style=flat-square&logo=typescript)](https://www.typescriptlang.org/)

Subscript is a high-performance, developer-first, and data-dense social network exclusively designed for college communities. Built using a modern monorepo architecture, the platform features a Vue 3 frontend optimized with Tailwind CSS v4, and a Node.js/Express backend that utilizes Firebase (Auth & Firestore) alongside a seamless local JSON database fallback for zero-configuration development.

---

## 🏗️ Architecture & Project Structure

The project is structured as a monorepo containing distinct `client/` and `server/` directories:

```
subscript/
├── client/                     # Frontend Vue 3 application
│   ├── src/
│   │   ├── assets/             # Global styles and static assets
│   │   ├── components/         # Shared, global UI elements (Buttons, Inputs, Cards)
│   │   ├── composables/        # Shared composables (useAuth, useTheme, etc.)
│   │   ├── layouts/            # Page layouts (DefaultLayout, AuthLayout, etc.)
│   │   ├── modules/            # Domain-driven features (Modular pattern)
│   │   │   ├── auth/           # Login, Register, Verification
│   │   │   ├── feed/           # Main post feed
│   │   │   ├── interviews/     # Interview experience sharing
│   │   │   ├── resources/      # Notes & roadmap resource sharing
│   │   │   ├── discussions/    # Forum channels
│   │   │   └── admin/          # Moderation queue & analytics
│   │   ├── router/             # Vue Router configurations and navigation guards
│   │   └── stores/             # Global Pinia stores (auth, theme, notifications)
│   ├── tailwind.config.js      # Tailwind configurations
│   ├── vite.config.ts          # Vite configuration
│   └── package.json
│
├── server/                     # Backend Node.js / Express application
│   ├── src/
│   │   ├── config/             # Firebase configuration & initialization
│   │   ├── constants/          # Application-wide status codes & secret constants
│   │   ├── controllers/        # Domain-specific route handlers
│   │   ├── middlewares/        # Authentication, validation, and moderation guards
│   │   ├── models/             # Schema definitions and type guards
│   │   ├── routes/             # Unified Express router
│   │   ├── services/           # Business logic, DB service layer, and AI moderation
│   │   └── index.ts            # Entry point for the server
│   └── package.json
│
└── data/                       # Directory for local JSON data store (Mock Mode fallback)
```

---

## ⚡ Core Features

- **Domain-Restricted Signups**: Restricts registrations strictly to verified college email domains (e.g. `@college.edu`).
- **Domain-Driven Modular Frontend**: Organizes Vue modules under a unified folder structure (Auth, Profile, Feed, Interviews, Resources, Moderation, and AI Recommendations).
- **Flexible Dual-Database Backend**: Connects directly to Firestore (via SDK or local Emulators) when credentials are provided, or transparently falls back to a file-based JSON database under `data/` for immediate setup.
- **Pre-Moderation AI Service**: Intercepts content creation (posts/comments), evaluates against safety guidelines, assigns a toxicity score, and routes flagged items to a Moderator dashboard.
- **Lightweight Interest-Based Recommendations**: Recommends posts, resources, and interview experiences by matching tags to user profiles (skills, programming languages, and tech stacks).
- **Optimistic UI Updates & Virtual Feed**: Provides instantaneous feedback on user interactions (likes, bookmarks) and loads the home feed lazily.

---

## ⚙️ Environment Configuration

### Backend (`server/`)
Create a `.env` file in the `server/` directory:

```env
PORT=5000
NODE_ENV=development

# JWT Secrets for Authorization
JWT_SECRET=college-social-network-access-secret-1234
REFRESH_SECRET=college-social-network-refresh-secret-5678

# Firebase Credentials (Optional)
# If left empty, the application falls back to Mock local JSON database mode.
FIREBASE_SERVICE_ACCOUNT={"type":"service_account","project_id":"..."}

# Or Firebase Local Emulator settings (Optional)
# FIRESTORE_EMULATOR_HOST=localhost:8080
# FIREBASE_AUTH_EMULATOR_HOST=localhost:9099
```

### Frontend (`client/`)
Create a `.env` file in the `client/` directory:

```env
VITE_API_URL=http://localhost:5000/api/v1
```

---

## 🚀 Getting Started

### 📋 Prerequisites
- **Node.js**: v18.x or later
- **npm**: v9.x or later

---

### 1. Backend Setup & Run

Navigate to the `server/` folder, install dependencies, and start the development server:

```bash
cd server
npm install

# Run backend in development mode (with hot-reload via ts-node-dev)
npm run dev
```

The server will spin up at `http://localhost:5000` with the following output indicating the active database mode:
*   **Firebase Service Account Mode:** `⚡ Firebase Admin initialized with Service Account.`
*   **Emulator Mode:** `⚡ Connected to Firebase Local Emulators.`
*   **Mock File Mode:** `⚠️ No Firebase credentials or emulators detected. Falling back to local data store.`

---

### 2. Frontend Setup & Run

Navigate to the `client/` folder, install dependencies, and start the Vite development server:

```bash
cd ../client
npm install

# Run frontend in development mode
npm run dev
```

The application will be accessible at `http://localhost:5173`.

---

### 📦 Production Builds

To compile and bundle both packages for production deployment, navigate to the root directory and run:

```bash
npm run build
```

This will run the build processes in sequence, installing frontend dependencies, compiling Vite/Vue assets, and exporting the static bundle into the root `/dist` directory for simplified serving.

---

## 🛡️ Content Moderation & Safety Workflows

The platform prioritizes community safety through a dedicated moderation layer:

```
[User Submits Content] ──> [AI Moderation Service]
                                  │
         ┌────────────────────────┼────────────────────────┐
         ▼ (Score < 0.4)          ▼ (0.4 <= Score < 0.7)    ▼ (Score >= 0.7)
    [Approved & Live]        [Visible + Caution Flag]  [Flagged / Hidden]
                                                           │
                                                           ▼
                                                   [Moderator Queue]
                                                           │
                                         ┌─────────────────┴─────────────────┐
                                         ▼ (Override Approve)                ▼ (Confirm Hide)
                                    [Make Post Live]                 [Permanently Hidden]
```

- **Toxicity Score < 0.4**: Content is immediately approved and visible on the feed.
- **Toxicity Score 0.4 - 0.7**: Content is published but tagged with a caution flag for review.
- **Toxicity Score >= 0.7**: Content is flagged as a high-confidence violation, instantly hidden from view, logged in the Audit Trail, and sent to the Moderator dashboard for final action.

---

## 📊 Database Collections & Schemas

### **Users (`users` collection)**
Stores user profiles, academic details, social handles, portfolio/projects, and followers/following metadata.

### **Posts (`posts` collection)**
Stores content blocks containing rich-text markup, attached documents, structural code snippets, likes/tags, and AI scoring metrics.

### **Comments (`comments` collection)**
Self-referential document tree that supports replies and sub-threads for deep discussion forums.

### **Interview Experiences (`interviewExperiences` collection)**
Maintains detailed placement timelines, round-by-round descriptions, code/system-design questions, preparation guides, and interview outcomes.

### **Resources (`resources` collection)**
Aggregates shared files, roadmap URLs, textbook guides, and notes, filtered by semester and academic categories.
