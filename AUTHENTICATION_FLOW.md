# 🔐 Authentication Flow Diagram

## System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    TREASURE HUNT GAME                        │
│                      (final2.py)                             │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                   Django API Layer                           │
│                  (DjangoAPI Class)                           │
│  ┌────────────────────────────────────────────────────┐    │
│  │  Try Django Backend First                          │    │
│  │  ↓                                                  │    │
│  │  If Successful → Use Database ✅                   │    │
│  │  ↓                                                  │    │
│  │  If Failed → Fallback to JSON Auth Manager ⚠️      │    │
│  └────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
                              │
                ┌─────────────┴─────────────┐
                │                           │
                ▼                           ▼
    ┌─────────────────────┐     ┌─────────────────────┐
    │  Django Database    │     │  JSON Auth Manager  │
    │   (PostgreSQL)      │     │  (auth_manager.py)  │
    │                     │     │                     │
    │  • Users Table      │     │  • users.json       │
    │  • Achievements     │     │  • SHA-256 Hashing  │
    │  • Leaderboards     │     │  • Local Storage    │
    └─────────────────────┘     └─────────────────────┘
                                            │
                                            ▼
                                  ┌─────────────────────┐
                                  │    users.json       │
                                  │  ┌───────────────┐  │
                                  │  │ {             │  │
                                  │  │   "users": [  │  │
                                  │  │     {user1},  │  │
                                  │  │     {user2}   │  │
                                  │  │   ]           │  │
                                  │  │ }             │  │
                                  │  └───────────────┘  │
                                  └─────────────────────┘
```

---

## Registration Flow

```
┌───────────────────────────────────────────────────────────────────┐
│                    USER REGISTRATION FLOW                         │
└───────────────────────────────────────────────────────────────────┘

    User enters:
    • Username (min 3 chars)
    • Password (min 6 chars)
    • Email (optional)
            │
            ▼
    ┌─────────────────┐
    │  Input Validation│
    │                 │
    │ ✓ Username ≥3   │
    │ ✓ Password ≥6   │
    │ ✓ No duplicates │
    └─────────────────┘
            │
            ▼
    ┌─────────────────────────────────────┐
    │  Try Django Backend Registration    │
    │                                     │
    │  POST /api/auth/register/           │
    │  {username, email, password}        │
    └─────────────────────────────────────┘
            │
            ├──────────────┬──────────────┐
            │              │              │
            ▼              ▼              ▼
    ✅ Backend Success  ⚠️ Backend Down  ❌ Backend Error
            │              │              │
            │              └──────┬───────┘
            │                     │
            │                     ▼
            │           ┌─────────────────────┐
            │           │  JSON Fallback      │
            │           │                     │
            │           │  1. Hash password   │
            │           │  2. Create user obj │
            │           │  3. Save to JSON    │
            │           └─────────────────────┘
            │                     │
            └──────────┬──────────┘
                       ▼
            ┌─────────────────────┐
            │   Auto-Login User   │
            │                     │
            │ • Set session state │
            │ • Display success   │
            │ • Redirect to game  │
            └─────────────────────┘
                       ▼
            🎮 START PLAYING!
```

---

## Login Flow

```
┌───────────────────────────────────────────────────────────────────┐
│                      USER LOGIN FLOW                              │
└───────────────────────────────────────────────────────────────────┘

    User enters:
    • Username
    • Password
            │
            ▼
    ┌─────────────────────────────┐
    │  Try Django Backend Login   │
    │                             │
    │  POST /api/auth/login/      │
    │  {username, password}       │
    └─────────────────────────────┘
            │
            ├──────────────┬──────────────┐
            │              │              │
            ▼              ▼              ▼
    ✅ Success         ⚠️ Backend Down  ❌ Failed
    (200 OK)              │          (Invalid credentials)
            │              │              │
            │              ▼              │
            │    ┌─────────────────────┐ │
            │    │  JSON Auth Fallback │ │
            │    │                     │ │
            │    │  1. Load users.json │ │
            │    │  2. Find username   │ │
            │    │  3. Hash password   │ │
            │    │  4. Compare hashes  │ │
            │    └─────────────────────┘ │
            │              │              │
            │              ├──────┬───────┘
            │              │      │
            ▼              ▼      ▼
         ✅ Match      ✅ Match  ❌ No Match
            │              │       │
            └──────┬───────┘       │
                   ▼               ▼
        ┌─────────────────┐  ┌──────────────┐
        │  Login Success  │  │ Login Failed │
        │                 │  │              │
        │ • Set session   │  │ • Show error │
        │ • Update stats  │  │ • Try again  │
        │ • Enter game    │  └──────────────┘
        └─────────────────┘
                   ▼
           🎮 GAME STARTS!
```

---

## Password Hashing Process

```
┌───────────────────────────────────────────────────────────────────┐
│                    PASSWORD SECURITY FLOW                         │
└───────────────────────────────────────────────────────────────────┘

    REGISTRATION:
    ═════════════
    
    User Password: "mypassword123"
            │
            ▼
    ┌─────────────────────────────┐
    │  SHA-256 Hash Function      │
    │                             │
    │  hashlib.sha256(password    │
    │    .encode()).hexdigest()   │
    └─────────────────────────────┘
            │
            ▼
    Hashed: "ef92b778bafe771e89245b89ecbc08a44a4e166c06659911881f383d4473e94f"
            │
            ▼
    ┌─────────────────────────────┐
    │  Save to users.json         │
    │                             │
    │  {                          │
    │    "password": "ef92b7..."  │
    │  }                          │
    └─────────────────────────────┘


    LOGIN:
    ══════
    
    User enters: "mypassword123"
            │
            ▼
    ┌─────────────────────────────┐
    │  Hash entered password      │
    └─────────────────────────────┘
            │
            ▼
    Hash: "ef92b778bafe771e89245b89ecbc08a44a4e166c06659911881f383d4473e94f"
            │
            ▼
    ┌─────────────────────────────┐
    │  Compare with stored hash   │
    │                             │
    │  entered_hash == stored_hash│
    └─────────────────────────────┘
            │
            ├─────────────┬─────────────┐
            ▼             ▼             ▼
         ✅ Match    ❌ No Match   ❌ User Not Found
            │             │             │
            ▼             ▼             ▼
      LOGIN SUCCESS   LOGIN FAIL    LOGIN FAIL
```

---

## Data Storage Structure

```
users.json
══════════

{
  "users": [
    {
      "username": "alice",                          ← Unique identifier
      "password": "4e40e8ffe0ee32fa...",           ← SHA-256 hash
      "email": "alice@example.com",                ← Optional
      "created_at": "2025-10-24T15:32:47",        ← Registration date
      "last_login": "2025-10-24T15:32:48",        ← Last login time
      "total_games": 1,                            ← Games played
      "high_score": 85                             ← Best score
    },
    {
      "username": "bob",
      "password": "ed4d9437294706c6...",
      ...
    }
  ]
}
```

---

## Automatic Fallback System

```
┌───────────────────────────────────────────────────────────────────┐
│              INTELLIGENT AUTHENTICATION ROUTING                   │
└───────────────────────────────────────────────────────────────────┘

    User Action (Login/Register)
            │
            ▼
    ┌─────────────────────────────┐
    │  Check Backend Status       │
    │                             │
    │  requests.post(API_URL)     │
    └─────────────────────────────┘
            │
            ├─────────────────┬─────────────────┐
            │                 │                 │
            ▼                 ▼                 ▼
    🌐 Backend Online   ⚠️ Timeout       ❌ Connection Error
    (Django Running)    (Slow response)   (Backend down)
            │                 │                 │
            │                 └────────┬────────┘
            │                          │
            ▼                          ▼
    ┌──────────────────┐    ┌──────────────────────┐
    │  Use Database    │    │  Use JSON Fallback   │
    │                  │    │                      │
    │  ✓ Full features │    │  ✓ Works offline     │
    │  ✓ Achievements  │    │  ✓ Local storage     │
    │  ✓ Leaderboards  │    │  ✓ No internet       │
    │  ✓ Cloud backup  │    │  ✓ Fast & reliable   │
    └──────────────────┘    └──────────────────────┘
            │                          │
            └────────────┬─────────────┘
                         ▼
            🎮 USER SUCCESSFULLY AUTHENTICATED!
```

---

## Session Management

```
┌───────────────────────────────────────────────────────────────────┐
│                    STREAMLIT SESSION STATE                        │
└───────────────────────────────────────────────────────────────────┘

After Successful Login:
═══════════════════════

st.session_state = {
    "logged_in": True,                    ← Authentication flag
    "username": "alice",                  ← Current user
    "auth_token": "abc123...",           ← JWT token (if backend)
    "user_id": 42,                       ← Database ID (if backend)
    "start_time": datetime.now(),        ← Session start
    "level": 0,                          ← Current game level
    "score": 0,                          ← Current score
    "hints_used": 0,                     ← Hints counter
    ...                                  ← Game state
}

┌─────────────────────────────────────┐
│  Session persists across pages     │
│  Auto-logout on browser close      │
│  Secure state management           │
└─────────────────────────────────────┘
```

---

## Error Handling

```
┌───────────────────────────────────────────────────────────────────┐
│                      ERROR HANDLING FLOW                          │
└───────────────────────────────────────────────────────────────────┘

    Try Authentication
            │
            ▼
    ┌─────────────────┐
    │  Django Backend │
    └─────────────────┘
            │
            ├─────────────────┬──────────────┬─────────────┐
            │                 │              │             │
            ▼                 ▼              ▼             ▼
    ✅ Success       ⚠️ Timeout     ❌ Connection   ❌ Server
    (200 OK)        (5 seconds)      Error         Error
            │                 │              │             │
            │                 └──────┬───────┴─────────────┘
            │                        │
            │                        ▼
            │              ┌─────────────────────┐
            │              │  Fallback to JSON   │
            │              └─────────────────────┘
            │                        │
            └────────────┬───────────┘
                         ▼
            ┌─────────────────────────┐
            │  Try JSON Authentication│
            └─────────────────────────┘
                         │
                         ├──────────────┬──────────────┐
                         │              │              │
                         ▼              ▼              ▼
                  ✅ Success    ❌ Not Found   ❌ Invalid
                         │              │              │
                         │              └──────┬───────┘
                         │                     │
                         ▼                     ▼
              ┌──────────────────┐   ┌──────────────────┐
              │  Return Success  │   │  Return Error    │
              │  + User Data     │   │  + Error Message │
              └──────────────────┘   └──────────────────┘
```

---

## Complete Authentication Lifecycle

```
┌───────────────────────────────────────────────────────────────────┐
│               COMPLETE USER AUTHENTICATION LIFECYCLE              │
└───────────────────────────────────────────────────────────────────┘

1. FIRST TIME USER
   ════════════════
   
   Visit Game → Register → Auto-Login → Play Game
        │           │           │            │
        │           │           │            ▼
        │           │           │      ┌──────────────┐
        │           │           │      │ Game State   │
        │           │           │      │ Saved        │
        │           │           │      └──────────────┘
        │           │           │
        │           │           └─────→ Session Active
        │           │
        │           └─────→ User added to users.json
        │
        └─────→ See login page


2. RETURNING USER
   ═══════════════
   
   Visit Game → Login → Continue Game → Logout
        │          │           │            │
        │          │           │            ▼
        │          │           │      ┌──────────────┐
        │          │           │      │ Update Stats │
        │          │           │      │ in JSON      │
        │          │           │      └──────────────┘
        │          │           │
        │          │           └─────→ Resume from level
        │          │
        │          └─────→ Validate credentials
        │
        └─────→ Load existing session


3. GAME COMPLETION
   ═══════════════
   
   Finish Game → Update Stats → Save Score → Show Rank
        │             │             │            │
        │             │             │            ▼
        │             │             │      ┌──────────────┐
        │             │             │      │ Display      │
        │             │             │      │ Achievement  │
        │             │             │      └──────────────┘
        │             │             │
        │             │             └─────→ high_score updated
        │             │
        │             └─────→ total_games incremented
        │
        └─────→ Calculate final score
```

---

**Made with ❤️ for Software Freedom Day**  
*Celebrating Open Source • Building Digital Freedom*
