# 🏗️ FOSS Treasure Hunt - System Architecture

## 📐 Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        USER BROWSER                             │
│                     http://localhost:8501                       │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                  STREAMLIT FRONTEND (final2.py)                 │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  UI COMPONENTS                                           │  │
│  │  • Login/Registration Forms                              │  │
│  │  • Game Interface                                        │  │
│  │  • Leaderboard Display                                   │  │
│  │  • Achievement Badges                                    │  │
│  └──────────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  DjangoAPI CLASS (Backend Integration)                   │  │
│  │  • register_user()                                       │  │
│  │  • login_user()                                          │  │
│  │  • check_backend_status()                                │  │
│  └──────────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  GAME LOGIC                                              │  │
│  │  • Question/Answer Validation                            │  │
│  │  • Scoring System                                        │  │
│  │  • Level Progress                                        │  │
│  │  • Hint System                                           │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │ HTTP Requests
                             │ (POST/GET)
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│               DJANGO BACKEND (localhost:8000)                   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  API ENDPOINTS (authentication/views.py)                 │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │  Authentication                                    │  │  │
│  │  │  • POST /api/auth/register/                       │  │  │
│  │  │  • POST /api/auth/login/                          │  │  │
│  │  │  • POST /api/auth/logout/                         │  │  │
│  │  │  • GET  /api/auth/profile/                        │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │  Game Management                                   │  │  │
│  │  │  • POST /api/auth/game/start/                     │  │  │
│  │  │  • GET  /api/auth/game/session/                   │  │  │
│  │  │  • PUT  /api/auth/game/session/<id>/              │  │  │
│  │  │  • POST /api/auth/game/level/progress/            │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │  Achievements & Leaderboard                        │  │  │
│  │  │  • GET  /api/auth/achievements/all/               │  │  │
│  │  │  • POST /api/auth/achievements/unlock/            │  │  │
│  │  │  • GET  /api/auth/leaderboard/                    │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  └──────────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  SERIALIZERS (Data Validation)                           │  │
│  │  • UserRegistrationSerializer                            │  │
│  │  • LoginSerializer                                       │  │
│  │  • GameSessionSerializer                                 │  │
│  └──────────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  DJANGO ORM (Object-Relational Mapping)                  │  │
│  │  • Query Builder                                         │  │
│  │  • SQL Injection Protection                              │  │
│  │  • Transaction Management                                │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             │ SQL Queries
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                  SQLITE DATABASE (treasure_hunt.db)             │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  TABLES                                                  │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │  authentication_user                               │  │  │
│  │  │  • id (PK)                                        │  │  │
│  │  │  • username (UNIQUE)                              │  │  │
│  │  │  • email                                          │  │  │
│  │  │  • password (HASHED with PBKDF2)                 │  │  │
│  │  │  • total_score                                    │  │  │
│  │  │  • games_played                                   │  │  │
│  │  │  • best_streak                                    │  │  │
│  │  │  • rank                                           │  │  │
│  │  │  • created_at                                     │  │  │
│  │  │  • last_login_at                                  │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │  authentication_gamesession                        │  │  │
│  │  │  • id (PK)                                        │  │  │
│  │  │  • user_id (FK → authentication_user)             │  │  │
│  │  │  • session_token                                  │  │  │
│  │  │  • score                                          │  │  │
│  │  │  • current_level                                  │  │  │
│  │  │  • max_streak                                     │  │  │
│  │  │  • started_at                                     │  │  │
│  │  │  • completed_at                                   │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │  authentication_levelprogress                      │  │  │
│  │  │  authentication_achievement                        │  │  │
│  │  │  authentication_userachievement                    │  │  │
│  │  │  authentication_leaderboard                        │  │  │
│  │  │  authtoken_token                                   │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🔄 Data Flow Diagrams

### 1. User Registration Flow

```
┌──────────┐
│  User    │
└────┬─────┘
     │ 1. Fills registration form
     ▼
┌─────────────────────────────────┐
│  Streamlit Frontend             │
│  • Validates input (client)     │
│  • Checks password match        │
│  • Validates email format       │
└────┬────────────────────────────┘
     │ 2. POST /api/auth/register/
     │    JSON: {username, email, password}
     ▼
┌─────────────────────────────────┐
│  Django Backend                 │
│  • UserRegistrationSerializer   │
│  • Validates uniqueness         │
│  • Hashes password (PBKDF2)     │
└────┬────────────────────────────┘
     │ 3. INSERT INTO authentication_user
     ▼
┌─────────────────────────────────┐
│  SQLite Database                │
│  • Creates user record          │
│  • Generates auth token         │
└────┬────────────────────────────┘
     │ 4. Returns user data + token
     ▼
┌─────────────────────────────────┐
│  Streamlit Frontend             │
│  • Stores token in session      │
│  • Auto-login user              │
│  • Starts game                  │
└─────────────────────────────────┘
```

### 2. User Login Flow

```
┌──────────┐
│  User    │
└────┬─────┘
     │ 1. Enters credentials
     ▼
┌─────────────────────────────────┐
│  Streamlit Frontend             │
│  • Validates input              │
└────┬────────────────────────────┘
     │ 2. POST /api/auth/login/
     │    JSON: {username, password}
     ▼
┌─────────────────────────────────┐
│  Django Backend                 │
│  • LoginSerializer              │
│  • Finds user by username       │
│  • Verifies password hash       │
└────┬────────────────────────────┘
     │ 3. SELECT FROM authentication_user
     │    WHERE username = ?
     ▼
┌─────────────────────────────────┐
│  SQLite Database                │
│  • Returns user record          │
└────┬────────────────────────────┘
     │ 4. Password verified?
     ├─ YES → Generate/return token
     └─ NO  → Return 401 error
     ▼
┌─────────────────────────────────┐
│  Streamlit Frontend             │
│  • Stores token & user_id       │
│  • Updates session state        │
│  • Redirects to game            │
└─────────────────────────────────┘
```

### 3. Offline Fallback Flow

```
┌──────────┐
│  User    │
└────┬─────┘
     │ 1. Tries to register/login
     ▼
┌─────────────────────────────────┐
│  Streamlit Frontend             │
│  • Calls DjangoAPI.login_user() │
└────┬────────────────────────────┘
     │ 2. HTTP Request to Django
     ▼
┌─────────────────────────────────┐
│  Django Backend (Not Running)   │
│  ❌ Connection Error             │
└────┬────────────────────────────┘
     │ 3. ConnectionError exception
     ▼
┌─────────────────────────────────┐
│  Streamlit Frontend             │
│  • Catches exception            │
│  • Activates offline mode       │
│  • Uses fallback password       │
│  • Creates session-only account │
│  • Shows warning message        │
└─────────────────────────────────┘
```

---

## 🔒 Security Architecture

### Authentication Flow

```
┌──────────────────────────────────────────────────────────────┐
│  PASSWORD SECURITY                                           │
│                                                              │
│  User Password (Plain Text)                                 │
│         ↓                                                    │
│  PBKDF2 Algorithm (Django Default)                          │
│  • Salt: Random 12-byte string                              │
│  • Iterations: 600,000                                      │
│  • Hash Function: SHA256                                    │
│         ↓                                                    │
│  Stored Hash:                                               │
│  pbkdf2_sha256$600000$salt$hash                             │
│         ↓                                                    │
│  Never reversible - One-way encryption                      │
└──────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────┐
│  TOKEN AUTHENTICATION                                        │
│                                                              │
│  Login Success                                              │
│         ↓                                                    │
│  Django generates random token                              │
│  • 40-character hex string                                  │
│  • Cryptographically secure                                 │
│         ↓                                                    │
│  Token stored in database                                   │
│  • Links to user_id                                         │
│  • Used for API authentication                              │
│         ↓                                                    │
│  Frontend stores in session_state                           │
│  • Sent with every API request                              │
│  • Header: Authorization: Token <token>                     │
└──────────────────────────────────────────────────────────────┘
```

### Input Validation Layers

```
Layer 1: Frontend (Streamlit)
┌─────────────────────────────────────────┐
│ • Username length (≥3 chars)            │
│ • Email format (@, .)                   │
│ • Password length (≥6 chars)            │
│ • Password confirmation match           │
│ • Whitespace trimming                   │
└────────────┬────────────────────────────┘
             ▼
Layer 2: Django Serializers
┌─────────────────────────────────────────┐
│ • Field type validation                 │
│ • Required field checks                 │
│ • Custom validators                     │
│ • Username uniqueness                   │
│ • Email uniqueness                      │
└────────────┬────────────────────────────┘
             ▼
Layer 3: Django ORM
┌─────────────────────────────────────────┐
│ • SQL injection prevention              │
│ • Type coercion                         │
│ • Constraint enforcement                │
│ • Transaction safety                    │
└─────────────────────────────────────────┘
```

---

## 📦 Component Breakdown

### Frontend Components (final2.py)

```
┌─────────────────────────────────────────────────────────────┐
│  SESSION STATE MANAGEMENT                                   │
│  • logged_in: bool                                          │
│  • username: str                                            │
│  • backend_connected: bool                                  │
│  • auth_token: str                                          │
│  • user_id: int                                             │
│  • score: int                                               │
│  • level: int                                               │
│  • streak: int                                              │
│  • achievements: list                                       │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  UI PAGES                                                   │
│  • render_login_page()                                      │
│    - Tab 1: Login Form                                      │
│    - Tab 2: Registration Form                               │
│  • render_main_game()                                       │
│    - Question Display                                       │
│    - Answer Input                                           │
│    - Hint System                                            │
│    - Score Display                                          │
│  • render_leaderboard()                                     │
│  • render_achievements()                                    │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  GAME LOGIC                                                 │
│  • validate_answer()                                        │
│  • calculate_score()                                        │
│  • check_achievements()                                     │
│  • update_streak()                                          │
│  • process_hint()                                           │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  DJANGOAPI CLASS                                            │
│  • register_user(username, email, password)                 │
│    - POST to /api/auth/register/                            │
│    - Returns (success, message, data)                       │
│  • login_user(username, password)                           │
│    - POST to /api/auth/login/                               │
│    - Returns (success, message, data)                       │
│  • check_backend_status()                                   │
│    - GET to /api/auth/achievements/all/                     │
│    - Returns bool                                           │
└─────────────────────────────────────────────────────────────┘
```

### Backend Components (Django)

```
┌─────────────────────────────────────────────────────────────┐
│  MODELS (authentication/models.py)                          │
│  • User (extends AbstractUser)                              │
│  • GameSession                                              │
│  • LevelProgress                                            │
│  • Achievement                                              │
│  • UserAchievement                                          │
│  • Leaderboard                                              │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  SERIALIZERS (authentication/serializers.py)                │
│  • UserSerializer                                           │
│  • UserRegistrationSerializer                               │
│  • LoginSerializer                                          │
│  • GameSessionSerializer                                    │
│  • AchievementSerializer                                    │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  VIEWS (authentication/views.py)                            │
│  • register_user() - @api_view(['POST'])                    │
│  • login_user() - @api_view(['POST'])                       │
│  • logout_user() - @api_view(['POST'])                      │
│  • get_user_profile() - @api_view(['GET'])                  │
│  • create_game_session() - @api_view(['POST'])              │
│  • get_active_session() - @api_view(['GET'])                │
│  • update_game_session() - @api_view(['PUT'])               │
│  • save_level_progress() - @api_view(['POST'])              │
│  • list_achievements() - @api_view(['GET'])                 │
│  • unlock_achievement() - @api_view(['POST'])               │
│  • get_leaderboard() - @api_view(['GET'])                   │
└─────────────────────────────────────────────────────────────┘
```

---

## 🌐 Network Communication

### HTTP Request/Response Examples

#### Registration Request
```http
POST http://localhost:8000/api/auth/register/
Content-Type: application/json

{
    "username": "johndoe",
    "email": "john@example.com",
    "password": "secure123",
    "password_confirm": "secure123"
}
```

#### Registration Response (Success)
```http
HTTP/1.1 201 Created
Content-Type: application/json

{
    "success": true,
    "message": "Registration successful",
    "user": {
        "id": 5,
        "username": "johndoe",
        "email": "john@example.com",
        "total_score": 0,
        "games_played": 0,
        "rank": "🔰 BEGINNER CODER"
    },
    "token": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
}
```

#### Login Request
```http
POST http://localhost:8000/api/auth/login/
Content-Type: application/json

{
    "username": "johndoe",
    "password": "secure123"
}
```

#### Login Response (Success)
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "success": true,
    "message": "Welcome, johndoe!",
    "user": {
        "id": 5,
        "username": "johndoe",
        "email": "john@example.com"
    },
    "token": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
}
```

---

## 💾 Database Schema Relationships

```
┌─────────────────────────────────────────────────────────────┐
│                      DATABASE RELATIONSHIPS                 │
└─────────────────────────────────────────────────────────────┘

          ┌──────────────────┐
          │  User            │
          │  • id (PK)       │
          │  • username      │
          │  • email         │
          │  • password      │
          └────────┬─────────┘
                   │
          ┌────────┼─────────┬────────────┬────────────────┐
          │        │         │            │                │
          │        │         │            │                │
          ▼        ▼         ▼            ▼                ▼
    ┌─────────┐ ┌──────┐ ┌──────┐ ┌──────────┐ ┌────────────┐
    │ Game    │ │Level │ │User  │ │Leader    │ │ Auth       │
    │ Session │ │Prog  │ │Achiev│ │board     │ │ Token      │
    │         │ │ress  │ │ement │ │          │ │            │
    └─────────┘ └──────┘ └───┬──┘ └──────────┘ └────────────┘
                              │
                              │ Many-to-Many
                              │
                              ▼
                        ┌──────────┐
                        │Achieve   │
                        │ment      │
                        └──────────┘

Relationships:
• User ←→ GameSession (1:N) - One user, many sessions
• User ←→ LevelProgress (1:N) - One user, many level records
• User ←→ UserAchievement (1:N) - One user, many achievements
• User ←→ Leaderboard (1:1) - One user, one leaderboard entry
• User ←→ Token (1:1) - One user, one active token
• Achievement ←→ UserAchievement (1:N) - One achievement, unlocked by many users
```

---

## 🔄 State Management

### Streamlit Session State

```python
# Session State Structure
st.session_state = {
    # Authentication
    'logged_in': False,
    'username': '',
    'backend_connected': False,
    'auth_token': None,
    'user_id': None,
    
    # Game Progress
    'level': 0,
    'score': 0,
    'streak': 0,
    'max_streak': 0,
    'hints_used': 0,
    'wrong_attempts': 0,
    'perfect_levels': 0,
    
    # Timing
    'start_time': None,
    'level_start_time': None,
    
    # Achievements
    'unlocked_achievements': [],
    'combo_multiplier': 1.0,
    
    # Phase Control
    'riddle_solved': False,
    'security_passed': False
}
```

---

## 🚀 Deployment Architecture (Future)

```
┌────────────────────────────────────────────────────────────┐
│  PRODUCTION DEPLOYMENT                                     │
│                                                            │
│  Frontend: Streamlit Cloud / Heroku                        │
│         ↓ HTTPS                                            │
│  Backend: Django on AWS EC2 / DigitalOcean                 │
│         ↓                                                  │
│  Database: PostgreSQL (RDS) / MySQL                        │
│         ↓                                                  │
│  Static Files: AWS S3 / CDN                                │
│         ↓                                                  │
│  Media Files: Cloud Storage                                │
└────────────────────────────────────────────────────────────┘
```

---

## 📊 Performance Considerations

### Current Architecture (Development)
- **Frontend:** Streamlit (single-threaded, synchronous)
- **Backend:** Django development server (single-threaded)
- **Database:** SQLite (file-based, no concurrent writes)

### Scalability Improvements
- **Frontend:** Deploy on Streamlit Cloud with session affinity
- **Backend:** Use Gunicorn + Nginx for production
- **Database:** Migrate to PostgreSQL for concurrent access
- **Caching:** Implement Redis for session/token caching
- **Load Balancing:** Add reverse proxy for multiple backend instances

---

## 🎯 Summary

This architecture provides:
- ✅ **Clean Separation** - Frontend and backend are independent
- ✅ **RESTful API** - Standard HTTP methods for all operations
- ✅ **Secure Authentication** - Password hashing and token-based auth
- ✅ **Scalable Design** - Can be deployed to production with minimal changes
- ✅ **Offline Resilience** - Fallback mode when backend unavailable
- ✅ **Data Persistence** - All user data stored in database
- ✅ **Modern Stack** - Python, Django, REST, Streamlit

**Perfect for learning full-stack development!** 🚀
