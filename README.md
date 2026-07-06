# Voxtalk

A language-exchange social app. Users sign up, get matched with people who speak their target language, add them as friends, and chat or video call. Built on MERN with GetStream's Chat and Video SDKs handling the messaging/calling infrastructure.

## Read this before you use it

**Your `.env` files are committed to this zip with real values in them** — `backend/.env` has your Mongo connection string, JWT secret, and Stream API key/secret; `frontend/.env` has your Stream API key. If this ever got pushed to GitHub with those files tracked, anyone could pull your database credentials and impersonate your app's Stream account. Check right now whether these were ever committed to git history — if they were, rotate all of these credentials (new Mongo password, new JWT secret, new Stream keys) before doing anything else. A `.gitignore` entry alone doesn't retroactively remove secrets already in a commit history.

**On the internal project name:** your `package.json` names this project `"streamify"`, not Voxtalk. This is a widely-taught tutorial project (chat/video app using GetStream, often called Streamify). Nothing wrong with building from a tutorial to learn — but if this goes on a resume, don't describe the architecture as original design. Be ready to explain the friend-request flow, the JWT cookie auth, and how Stream tokens are generated and used, without hesitation. If you can't, don't claim it.

## Features

- Email/password signup and login with JWT stored in an httpOnly cookie
- Onboarding step to set native language, learning language, bio, and location
- Friend system: send, view, and accept friend requests; see recommended (non-friend) users
- Real-time chat via Stream Chat SDK
- Video calling via Stream Video SDK
- Theme switching (DaisyUI themes) on the frontend

## Tech Stack

**Frontend:** React 19, Vite, React Router v7, Zustand (theme store), TanStack Query, Tailwind CSS + DaisyUI, Axios, react-hot-toast

**Backend:** Node.js, Express 4, MongoDB with Mongoose, JWT, bcryptjs, cookie-parser

**Third-party:**
- [GetStream Chat](https://getstream.io/chat/) — messaging
- [GetStream Video](https://getstream.io/video/) — video calls

## Project Structure

```
Voxtalk-main/
├── backend/
│   └── src/
│       ├── controllers/     # auth, user (friends), chat (stream token)
│       ├── middleware/       # auth.middleware.js — JWT verification
│       ├── models/           # User, FriendRequest
│       ├── lib/               # db.js (Mongo connect), stream.js (Stream client + token gen)
│       ├── routes/            # auth, user, chat route definitions
│       └── server.js
└── frontend/
    └── src/
        ├── components/        # Navbar, Sidebar, FriendCard, CallButton, ThemeSelector...
        ├── pages/              # Login, SignUp, Onboarding, Home, Chat, Call, Notifications
        ├── hooks/               # useAuthUser, useLogin, useSignUp, useLogout
        ├── store/               # useThemeStore (Zustand)
        └── lib/                 # axios instance, api functions, utils
```

## Getting Started

### Prerequisites
- Node.js (v18+)
- A MongoDB instance (local or Atlas)
- A [GetStream](https://getstream.io) account for API key/secret (free tier available)

### 1. Clone and install
```bash
git clone https://github.com/Suwalkya-ji/Voxtalk.git
cd Voxtalk
npm install --prefix backend
npm install --prefix frontend
```
(The root `package.json` also has a `build` script that does both installs plus the frontend build, useful for deployment.)

### 2. Environment variables

**`backend/.env`** — rotate these values before reusing this repo publicly:
```env
PORT=5001
MONGO_URL=your_mongodb_connection_string
STREAM_API_KEY=your_stream_api_key
STREAM_API_SECRET=your_stream_api_secret
JWT_SECRET_KEY=your_jwt_secret
NODE_ENV=development
```

**`frontend/.env`:**
```env
VITE_STREAM_API_KEY=your_stream_api_key
```

### 3. Run it
```bash
# Backend
cd backend
npm run dev

# Frontend (separate terminal)
cd frontend
npm run dev
```
Frontend runs on `http://localhost:5173`, backend on `http://localhost:5001` by default.

## Key API Routes

| Method | Route | Description |
|---|---|---|
| POST | `/api/auth/signup` | Register a new user |
| POST | `/api/auth/login` | Log in, sets JWT cookie |
| POST | `/api/auth/logout` | Clear auth cookie |
| POST | `/api/auth/onboarding` | Set language/bio/location (authenticated) |
| GET | `/api/auth/me` | Get current logged-in user |
| GET | `/api/users` | Get recommended (non-friend) users |
| GET | `/api/users/friends` | Get current user's friends |
| POST | `/api/users/friend-request/:id` | Send a friend request |
| PUT | `/api/users/friend-request/:id/accept` | Accept a friend request |
| GET | `/api/users/friend-requests` | Get incoming friend requests |
| GET | `/api/users/outgoing-friend-requests` | Get outgoing friend requests |
| GET | `/api/chat/token` | Get a Stream token for the logged-in user (authenticated) |

## Immediate to-dos

1. **Rotate every credential in `backend/.env` and `frontend/.env`** if this repo has ever been pushed anywhere, public or private, with those files tracked.
2. Confirm `.env` is actually in `.gitignore` going forward (check — don't assume) and run `git rm --cached backend/.env frontend/.env` if they're currently tracked.
3. Add a `.env.example` with variable names only, no real values.
4. Add a LICENSE if this is going public.
