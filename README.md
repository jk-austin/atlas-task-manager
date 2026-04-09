# Project Atlas

A full-stack task management application built to demonstrate professional web development practices 
including REST API design, JWT authentication, relational data modeling, and automated testing.

🔗 Live Demo — coming soon

---

## Features

- User registration and login with JWT authentication
- Create, update, and delete projects
- Create, complete, and delete tasks within projects
- Protected routes — unauthenticated users cannot access the app
- Consistent error handling across all API endpoints

### Roadmap
- Social login via Google and GitHub (Passport.js)
- Task due dates, priority levels, and labels
- Drag and drop task reordering
- Team collaboration and shared projects
- Short-lived access tokens with refresh token rotation

---

## Architecture

Project Atlas is a decoupled full-stack application with a separate backend API and Angular frontend.

### Request Flow
```
Angular Frontend (Vercel)
  → AuthInterceptor attaches JWT to every request
  → Express.js REST API (Railway)
    → Joi validates request body
    → Auth middleware validates JWT
    → Prisma ORM queries Supabase Postgres
  → Response returned to frontend
  → AuthInterceptor redirects to /login on 401
```

### Key Design Decisions

**JWT stored in localStorage** — chosen for simplicity in this implementation. The production approach would use httpOnly cookies with short-lived access tokens (15 min) and long-lived refresh tokens (7 days) to mitigate XSS risk.

**Prisma ORM over Supabase client** — since this app manages its own authentication, connecting directly to Postgres via Prisma is cleaner than using the Supabase client, which is designed for apps using Supabase Auth.

**Cascade delete at the database level** — deleting a project automatically removes all its tasks via a Prisma `onDelete: Cascade` constraint, rather than handling this in application code.

**Centralized error handling** — all errors flow through a single Express middleware that returns a consistent JSON shape, making frontend error handling predictable.

---

## Tech Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Frontend | Angular | Widely used in enterprise and agency work |
| UI Components | Angular Material | Google's official Angular component library |
| Backend | Express.js | Lightweight, widely used Node.js framework |
| Authentication | JWT + bcrypt | Built from scratch to demonstrate auth fundamentals |
| Validation | Joi | Keeps validation logic out of route handlers |
| Database | Supabase Postgres | Managed Postgres with a generous free tier |
| ORM | Prisma | Type-safe database access with migration support |
| Testing | Jest | Consistent testing framework across frontend and backend |
| Deployment | Railway (API) + Vercel (frontend) | Industry standard deployment platforms |

---

## Running Locally

### Prerequisites
- Node.js v18+
- npm

### Backend

1. Clone the repo
```bash
   git clone https://github.com/jk-austin/atlas-task-manager.git
   cd atlas-backend
```

2. Install dependencies
```bash
   npm install
```

3. Create a `.env` file in the root:
```
   PORT=3000
   JWT_SECRET=your_secret_key_here
   DATABASE_URL=your_supabase_connection_string
```

4. Run database migrations
```bash
   npx prisma migrate dev
```

5. Start the development server
```bash
   npm run dev
```

API runs at `http://localhost:3000`

### Frontend
*Instructions coming once Angular app is scaffolded*

---

## API Reference

### Auth
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/register` | Create account, returns JWT |
| POST | `/api/auth/login` | Login, returns JWT |

### Projects
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/projects` | Get all projects for logged in user |
| POST | `/api/projects` | Create a new project |
| GET | `/api/projects/:id` | Get a single project |
| PUT | `/api/projects/:id` | Update a project |
| DELETE | `/api/projects/:id` | Delete project and its tasks |

### Tasks
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/projects/:id/tasks` | Get all tasks for a project |
| POST | `/api/projects/:id/tasks` | Create a task |
| PUT | `/api/projects/:id/tasks/:tid` | Update a task |
| DELETE | `/api/projects/:id/tasks/:tid` | Delete a task |

---

## Environment Variables

| Variable | Description | Where to get it |
|----------|-------------|-----------------|
| `PORT` | Server port | Any number, 3000 is standard |
| `JWT_SECRET` | Secret key for signing tokens | Generate a random string |
| `DATABASE_URL` | Postgres connection string | Supabase dashboard → Settings → Database |

---

## Testing
```bash
npm test
```

Tests are written alongside each feature using Jest. Coverage includes:
- Auth — registration, login, token validation
- Projects — full CRUD, ownership validation, cascade delete
- Tasks — full CRUD, status enum validation
- Error handling — consistent JSON shape, correct status codes

---

## Security Notes

- Passwords hashed with bcrypt before storage
- JWT tokens expire after 7 days
- All project and task routes validate resource ownership
- Unauthenticated requests return 401, unauthorized requests return 403
- `node_modules` and `.env` excluded from version control

---

## License

MIT
