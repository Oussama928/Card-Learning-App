# Card Learning App

A full-stack language learning platform built with Next.js 15, featuring spaced repetition flashcards, skill trees, study groups, AI tutoring, and real-time collaboration.

![Home Screen](/public/showcase/home_screen.png)

---

## What This Is

A language learning application that combines spaced repetition flashcards with structured skill trees, community features, and AI-powered tutoring. Built as a full-stack Next.js application with a custom Node.js backend, PostgreSQL database, and real-time WebSocket support.


---

## Features

### Spaced Repetition Flashcards
Smart scheduling algorithm (SM-2 based) that adapts review intervals based on your performance. Cards you struggle with appear more often; mastered cards fade into the background.

![Adding Cards](/public/showcase/adding_card.png)

### Skill Trees
Structured learning paths for languages (French, Spanish, etc.) with progressive unlocking. Complete lessons to unlock new branches and track your proficiency visually.

![Skill Tree](/public/showcase/skill_tree.png)

### Study Groups
Create or join study groups, share card decks, compete on leaderboards, and study together in real-time.

| Study Groups Main | Group Details |
|-------------------|---------------|
| ![Study Groups](/public/showcase/study_groups_main_screen.png) | ![Group Details](/public/showcase/study_group_details.png) |

### AI Tutor
Chat with an AI tutor powered by Hugging Face for explanations, translations, and practice conversations.

![AI Tutor](/public/showcase/AI_TUTOR.png)

### Achievements & Progression
Earn XP, unlock achievements, climb leaderboards, and maintain study streaks. Tier-based progression with cosmetic rewards.

### Real-time Notifications
Push notifications (Web Push API) for study reminders, group activity, and achievement unlocks. Works offline via Service Worker.

### Authentication
- Email/password with bcrypt hashing
- JWT access + refresh tokens (httpOnly cookies)
- Email verification & password reset flows
- Role-based access (user, admin, moderator)

---

## Tech Stack

| Layer | Technology |
|-------|------------|
| **Frontend** | Next.js 15 (App Router), React 19, TypeScript, Tailwind CSS, Radix UI |
| **Backend** | Node.js (custom server), Express-style routing, Knex.js query builder |
| **Database** | PostgreSQL (Knex migrations), full-text search via tsvector |
| **Real-time** | Socket.io (study groups, notifications) |
| **Auth** | JWT (access + refresh), httpOnly cookies, bcrypt |
| **AI** | Hugging Face Inference API |
| **Push** | Web Push (VAPID), Service Worker |
| **Observability** | Sentry (error tracking), custom logger |
| **Testing** | Vitest, React Testing Library |
| **Deployment** | Docker, Vercel (frontend), Railway/Render (backend) |

---

## Project Structure

```
card-learning-app/
├── app/                    # Next.js App Router pages
│   ├── (root)/            # Public pages (home, login, signup)
│   ├── learning/          # Flashcard study sessions
│   ├── skill-tree/        # Skill tree progression
│   ├── study-groups/      # Group collaboration
│   ├── community/         # Public decks & leaderboards
│   ├── profile/           # User profile & stats
│   ├── api/               # API routes (Next.js)
│   └── components/        # Shared React components
├── lib/                    # Core libraries & utilities
│   ├── db.js              # Knex instance
│   ├── spacedRepetition.ts # SM-2 algorithm
│   ├── skillTreeService.ts # Skill tree logic
│   ├── studyGroups.ts     # Group management
│   ├── socketServer.js    # Socket.io server
│   ├── pushNotifications.ts # Web Push
│   └── authTokens.ts      # JWT utilities
├── migrations/             # Knex migrations (PostgreSQL)
├── services/               # Business logic (service layer)
│   ├── authService.ts
│   ├── cardService.ts
│   ├── notificationService.ts
│   └── studyGroupService.ts
├── hooks/                  # React hooks (useCards, etc.)
├── types/                  # TypeScript definitions
├── public/showcase/        # Screenshots for README
├── scripts/                # Migration & seed scripts
├── tests/                  # Vitest tests
├── server.js               # Custom Node server (Next.js + Socket.io)
├── knexfile.js             # Knex configuration
└── Dockerfile              # Multi-stage Docker build
```

---

## Getting Started

### Prerequisites

- Node.js 20+
- PostgreSQL 15+
- Redis (optional, for Socket.io scaling)
- pnpm (recommended) or npm

### Environment Variables

Copy `.env.example` to `.env` and configure:

```bash
# Database
DATABASE_URL=postgresql://user:pass@localhost:5432/card_learning
# or individual params:
DB_HOST=localhost
DB_PORT=5432
DB_NAME=card_learning
DB_USER=postgres
DB_PASSWORD=secret

# Auth
JWT_SECRET=your-super-secret-jwt-key-min-32-chars
JWT_REFRESH_SECRET=another-super-secret-refresh-key
NEXT_PUBLIC_APP_URL=http://localhost:3000

# Email (verification/reset)
SMTP_HOST=smtp.example.com
SMTP_PORT=587
SMTP_USER=your-smtp-user
SMTP_PASS=your-smtp-pass
EMAIL_FROM=noreply@yourapp.com

# Push Notifications (VAPID)
VAPID_PUBLIC_KEY=your-vapid-public-key
VAPID_PRIVATE_KEY=your-vapid-private-key
VAPID_SUBJECT=mailto:admin@yourapp.com

# AI (Hugging Face)
HUGGINGFACE_API_KEY=hf_your_token

# Sentry (optional)
SENTRY_DSN=https://xxx@sentry.io/xxx

# Sanity CMS (optional)
SANITY_PROJECT_ID=xxx
SANITY_DATASET=production
SANITY_API_TOKEN=xxx
```

### Database Setup

```bash
# Run migrations
npm run migrate

# (Optional) Seed French skill tree
npm run seed:french-skill-tree
```

### Development

```bash
# Install dependencies
pnpm install

# Start dev server (Next.js + Socket.io)
npm run dev

# Run tests
npm run test

# Type check
npm run typecheck

# Lint
npm run lint
```

Open [http://localhost:3000](http://localhost:3000)

### Production Build

```bash
npm run build
npm start
```

### Docker

```bash
docker build -t card-learning-app .
docker run -p 3000:3000 --env-file .env card-learning-app
```

---

## API Overview

### Authentication
| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/auth/register` | Register new user |
| `POST` | `/api/auth/login` | Login (sets httpOnly cookies) |
| `POST` | `/api/auth/refresh` | Refresh access token |
| `POST` | `/api/auth/logout` | Clear cookies |
| `POST` | `/api/auth/verify-email` | Verify email token |
| `POST` | `/api/auth/forgot-password` | Request password reset |
| `POST` | `/api/auth/reset-password` | Reset password |

### Cards & Progress
| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/cards` | List user's cards (paginated) |
| `POST` | `/api/cards` | Create new card |
| `GET` | `/api/cards/:id` | Get card details |
| `DELETE` | `/api/cards/:id` | Delete card |
| `POST` | `/api/cards/:id/favorite` | Toggle favorite |
| `GET` | `/api/progress` | Get spaced repetition progress |
| `POST` | `/api/progress` | Submit review result |

### Skill Trees
| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/skill-tree/:language` | Get skill tree |
| `POST` | `/api/skill-tree/unlock` | Unlock skill node |

### Study Groups
| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/study-groups` | List groups |
| `POST` | `/api/study-groups` | Create group |
| `POST` | `/api/study-groups/:id/join` | Join group |
| `GET` | `/api/study-groups/:id` | Group details |

### Achievements & Stats
| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/achievements` | User achievements |
| `GET` | `/api/leaderboard` | Global leaderboard |
| `GET` | `/api/stats/global` | Global statistics |

### Push Notifications
| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/push/subscribe` | Subscribe to push |
| `POST` | `/api/push/unsubscribe` | Unsubscribe |

### AI Tutor
| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/chat` | Chat with AI tutor |

### WebSocket Events (Socket.io)
| Event | Direction | Payload |
|-------|-----------|---------|
| `join-group` | Client → Server | `{ groupId }` |
| `leave-group` | Client → Server | `{ groupId }` |
| `group-message` | Bidirectional | `{ groupId, userId, content }` |
| `study-session-start` | Server → Client | `{ groupId, deckId }` |
| `notification` | Server → Client | `{ type, title, body, data }` |

---

## Database Schema (Key Tables)

```sql
users                 -- auth, profile, stats, preferences
cards                 -- user-created flashcards
card_progress         -- SM-2 spaced repetition data per user/card
skill_trees           -- language skill tree definitions
skill_nodes           -- individual lessons/nodes
user_skill_progress   -- user progress on skill nodes
study_groups          -- group metadata
group_members         -- many-to-many users ↔ groups
group_decks           -- shared card decks in groups
achievements          -- achievement definitions
user_achievements     -- earned achievements
push_subscriptions    -- Web Push endpoints
notifications         -- in-app notifications
study_activity        -- daily study logs for streaks
```

Run `npm run migrate` to apply all migrations (see `migrations/`).

---

## Spaced Repetition Algorithm

Uses a modified **SM-2** algorithm (`lib/spacedRepetition.ts`):

- **Ease Factor**: Starts at 2.5, adjusts ±0.15 based on performance
- **Intervals**: 1 day → 6 days → exponential growth
- **Lapses**: Reset interval to 1 day, reduce ease factor
- **Scheduling**: `nextReviewAt = lastReviewedAt + intervalDays`

Progress stored per-user-per-card in `card_progress`.

---

## Skill Trees

Defined in `scripts/seedFrenchSkillTree.js` (run with `npm run seed:french-skill-tree`).

```
French
├── Basics
│   ├── Greetings (unlocks → Basics 2)
│   ├── Numbers
│   └── Essentials
├── Grammar
│   ├── Present Tense
│   ├── Past Tense
│   └── Subjunctive
└── Vocabulary
    ├── Food
    ├── Travel
    └── Business
```

Nodes unlock based on prerequisite completion. Progress tracked in `user_skill_progress`.

---

## Push Notifications

- **VAPID Keys**: Generate with `npx web-push generate-vapid-keys`
- **Service Worker**: `public/push-sw.js` handles background notifications
- **Subscribe**: `POST /api/push/subscribe` (stores endpoint in `push_subscriptions`)
- **Send**: `notificationService.sendNotification(userId, payload)`

---

## Testing

```bash
# Run all tests
npm run test

# Watch mode
npm run test:watch

# Coverage report
npm run test -- --coverage
```

Tests located in `tests/` (API integration) and `app/**/*.test.tsx` (components).

---

## Deployment

### Vercel (Frontend)
1. Connect repository to Vercel
2. Add environment variables
3. Deploy — `vercel.json` handles API route rewrites

### Railway / Render / Fly.io (Backend + WebSocket)
The custom `server.js` runs both Next.js and Socket.io. Deploy as a Node.js service.

```bash
npm run build
npm start
```

### Database
Use managed PostgreSQL (Neon, Supabase, Railway, RDS). Run migrations on deploy:

```bash
npm run migrate
```

---

## Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feat/amazing-feature`
3. Commit changes: `git commit -m 'Add amazing feature'`
4. Push: `git push origin feat/amazing-feature`
5. Open a Pull Request

### Code Style
- TypeScript strict mode
- ESLint + Prettier (`npm run lint`)
- Conventional commits
- Write tests for new features

---

## License

MIT License — see [LICENSE](LICENSE) for details.

---

## Acknowledgments

- **SM-2 Algorithm** — SuperMemo
- **Radix UI** — Accessible component primitives
- **Tailwind CSS** — Utility-first styling
- **Hugging Face** — Free inference API for AI tutor
- **Socket.io** — Real-time engine
- **web-push** — Web Push protocol library

---

## Screenshots

| Home | Add Card | Skill Tree |
|------|----------|------------|
| ![Home](/public/showcase/home_screen.png) | ![Add Card](/public/showcase/adding_card.png) | ![Skill Tree](/public/showcase/skill_tree.png) |

| Study Groups | Group Details | AI Tutor |
|--------------|---------------|----------|
| ![Groups](/public/showcase/study_groups_main_screen.png) | ![Group Details](/public/showcase/study_group_details.png) | ![AI Tutor](/public/showcase/AI_TUTOR.png) |

---


