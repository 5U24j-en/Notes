
# Authentication & JWT Flow — Current Implementation

## 1. User Registration Flow

> Endpoint: `POST /sqlalchemy/users/` → [users.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/routers/users.py)

```mermaid
flowchart TD
    A(["🧑 Client sends POST /sqlalchemy/users/<br/>{email, password}"]) --> B["Pydantic validates input<br/>(createUser schema)"]
    B --> C["Auto-generates UUID via<br/>uid: UUID = Field(default_factory=uuid4)"]
    C --> D["utils.hash(password)<br/>bcrypt hashing via passlib"]
    D --> E["user.password = hashed_password"]
    E --> F["models.Users(**user.model_dump())<br/>Create SQLAlchemy model instance"]
    F --> G["db.add(new_User)<br/>db.commit()<br/>db.refresh(new_User)"]

    G --> H{DB Error?}
    H -->|Yes| I["db.rollback()<br/>❌ 500 Internal Server Error"]
    H -->|No| J["✅ 201 Created<br/>Response: {email, uid}<br/>(responseUser schema — password excluded)"]

    style I fill:#ff6b6b,stroke:#c92a2a,color:#fff
    style J fill:#51cf66,stroke:#2b8a3e,color:#fff
    style D fill:#339af0,stroke:#1971c2,color:#fff
```

### Files involved:
| Step | File | Function / Class |
|------|------|-----------------|
| Schema validation | [schemas.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/schemas.py) | `createUser`, `responseUser` |
| Password hashing | [utils.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/utils.py) | `hash()` using bcrypt |
| DB model | [models.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/models.py) | `Users` |
| Route handler | [users.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/routers/users.py) | `create_user()` |

---

## 2. Login Flow (Token Creation)

> Endpoint: `POST /login` → [auth.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/routers/auth.py)

```mermaid
flowchart TD
    A(["🧑 Client sends POST /login<br/>(OAuth2PasswordRequestForm:<br/>username=email, password)"]) --> B["Query DB:<br/>Users.filter(email == username).first()"]

    B --> C{DB Error?}
    C -->|Yes| D["❌ 500 Internal Server Error"]
    C -->|No| E{User found<br/>in DB?}

    E -->|No| F["❌ 403 Forbidden<br/>'Invalid Credentials'"]
    E -->|Yes| G["utils.verify(plain_password, hashed_password)<br/>bcrypt comparison via passlib"]

    G --> H{Password<br/>matches?}
    H -->|No| F
    H -->|Yes| I["Build payload:<br/>data = {'user_id': user_db.id}"]

    I --> J["oauth2.create_access_token(data)"]
    J --> K["Copy payload<br/>Add exp = now(UTC) + 30 min"]
    K --> L["jwt.encode(payload, SECRET_KEY, HS256)<br/>using python-jose"]
    L --> M["✅ 200 OK<br/>{access_token: 'eyJ...', token_type: 'bearer'}"]

    style D fill:#ff6b6b,stroke:#c92a2a,color:#fff
    style F fill:#ff6b6b,stroke:#c92a2a,color:#fff
    style M fill:#51cf66,stroke:#2b8a3e,color:#fff
    style G fill:#339af0,stroke:#1971c2,color:#fff
    style L fill:#ffd43b,stroke:#f08c00,color:#333
```

### Files involved:
| Step | File | Function / Class |
|------|------|-----------------|
| Form parsing | FastAPI built-in | `OAuth2PasswordRequestForm` |
| Route handler | [auth.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/routers/auth.py) | `user_login()` |
| Password verify | [utils.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/utils.py) | `verify()` |
| Token creation | [oauth2.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/oauth2.py) | `create_access_token()` |
| Response schema | [schemas.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/schemas.py) | `Token` |

---

## 3. Protected Route Access (Token Verification)

> Applied to: `POST`, `DELETE`, `PUT` on `/sqlalchemy/posts/` → [posts.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/routers/posts.py)

```mermaid
flowchart TD
    A(["🧑 Client sends request<br/>Header: Authorization: Bearer eyJ..."]) --> B["FastAPI Dependency Injection<br/>Depends(oauth2.get_current_user)"]

    B --> C["OAuth2PasswordBearer extracts token<br/>from 'Authorization: Bearer ...' header"]

    C --> D{Token present<br/>in header?}
    D -->|No| E["❌ 401 Unauthorized<br/>'Not authenticated'"]

    D -->|Yes| F["verify_access_token(token, exception)"]
    F --> G["jwt.decode(token, SECRET_KEY, HS256)<br/>using python-jose"]

    G --> H{JWTError?<br/>expired / invalid<br/>/ tampered}
    H -->|Yes| I["❌ 401 Unauthorized<br/>'Authentication Failed. Access Denied'"]

    H -->|No| J["Extract: user_id = payload.get('user_id')"]
    J --> K{user_id<br/>is None?}
    K -->|Yes| I
    K -->|No| L["Create tokenData(id=user_id)<br/>Pydantic model"]
    L --> M["Return tokenData to route handler<br/>as 'user_token_id' parameter"]
    M --> N["✅ Proceed to route logic<br/>(create / delete / update post)"]

    style E fill:#ff6b6b,stroke:#c92a2a,color:#fff
    style I fill:#ff6b6b,stroke:#c92a2a,color:#fff
    style N fill:#51cf66,stroke:#2b8a3e,color:#fff
    style G fill:#ffd43b,stroke:#f08c00,color:#333
```

### Files involved:
| Step | File | Function / Class |
|------|------|-----------------|
| Token extraction | [oauth2.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/oauth2.py) | `OAuth2PasswordBearer(tokenUrl="login")` |
| Token verification | [oauth2.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/oauth2.py) | `verify_access_token()` |
| Dependency injection | [oauth2.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/oauth2.py) | `get_current_user()` |
| Token data schema | [schemas.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/schemas.py) | `tokenData` |
| Protected routes | [posts.py](file:///home/userx/Desktop/FastAPI/freeCodeCamp/DesUno/app/routers/posts.py) | `orm_create_post`, `orm_delete_post`, `orm_update_post` |

---

## 4. JWT Token Structure

```mermaid
flowchart LR
    subgraph JWT["Encoded JWT Token (eyJ...)"]
        direction TB
        H["📋 HEADER<br/>{'alg': 'HS256', 'typ': 'JWT'}"]
        P["📦 PAYLOAD<br/>{'user_id': 5, 'exp': 1744482600}"]
        S["🔐 SIGNATURE<br/>HMAC-SHA256(header.payload, SECRET_KEY)"]
        H --- P --- S
    end

    subgraph Config["Config — oauth2.py"]
        SK["SECRET_KEY = '09d25e...d3e7'"]
        AL["ALGORITHM = 'HS256'"]
        EX["EXPIRE = 30 minutes"]
    end

    Config -.-> JWT
```

---

## 5. Complete End-to-End Sequence

```mermaid
sequenceDiagram
    actor Client
    participant Users as users.py<br/>POST /sqlalchemy/users/
    participant Auth as auth.py<br/>POST /login
    participant OAuth as oauth2.py
    participant Posts as posts.py<br/>/sqlalchemy/posts/
    participant Utils as utils.py
    participant DB as PostgreSQL

    Note over Client,DB: ── PHASE 1: REGISTRATION ──

    Client->>Users: POST /sqlalchemy/users/ {email, password}
    Users->>Utils: hash(password)
    Utils-->>Users: bcrypt hash
    Users->>DB: INSERT INTO users (uid, email, password, ...)
    DB-->>Users: new user row
    Users-->>Client: 201 {email, uid}

    Note over Client,DB: ── PHASE 2: LOGIN ──

    Client->>Auth: POST /login (username=email, password)
    Auth->>DB: SELECT * FROM users WHERE email = ?
    DB-->>Auth: user row
    Auth->>Utils: verify(password, user.password)
    Utils-->>Auth: True ✅
    Auth->>OAuth: create_access_token({"user_id": id})
    OAuth->>OAuth: payload + exp → jwt.encode(SECRET_KEY, HS256)
    OAuth-->>Auth: "eyJhbGci..."
    Auth-->>Client: 200 {access_token, token_type: "bearer"}

    Note over Client,DB: ── PHASE 3: PROTECTED REQUEST ──

    Client->>Posts: POST /sqlalchemy/posts/ {title, content}<br/>Authorization: Bearer eyJhbGci...
    Posts->>OAuth: Depends(get_current_user)
    OAuth->>OAuth: jwt.decode(token, SECRET_KEY, HS256)
    OAuth->>OAuth: Extract user_id → tokenData(id=user_id)
    OAuth-->>Posts: tokenData ✅
    Posts->>DB: INSERT INTO posts2 (title, content, ...)
    DB-->>Posts: new post row
    Posts-->>Client: 201 {id, title, content, published, created_at}
```

---

## 6. Which Routes Are Protected?

| Route | Method | Auth Required? | Dependency |
|-------|--------|---------------|------------|
| `/sqlalchemy/users/` | POST | ❌ No | — |
| `/sqlalchemy/users/{id}` | GET | ❌ No | — |
| `/login` | POST | ❌ No | — |
| `/sqlalchemy/posts/` | GET | ❌ No | — |
| `/sqlalchemy/posts/{id}` | GET | ❌ No | — |
| `/sqlalchemy/posts/` | **POST** | ✅ **Yes** | `Depends(oauth2.get_current_user)` |
| `/sqlalchemy/posts/{id}` | **DELETE** | ✅ **Yes** | `Depends(oauth2.get_current_user)` |
| `/sqlalchemy/posts/{id}` | **PUT** | ✅ **Yes** | `Depends(oauth2.get_current_user)` |
