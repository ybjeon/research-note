# Mermaid

Mermaid is a JavaScript-based diagramming tool that renders Markdown-inspired text definitions into diagrams.

## Sequence Diagram

A sequence diagram shows interactions between participants over time.

### Basic Syntax

```mermaid
sequenceDiagram
    participant A as Alice
    participant B as Bob

    A->>B: Hello Bob
    B-->>A: Hello Alice
```

Arrow types:

| Arrow  | Description                |
|--------|----------------------------|
| `->`   | Solid line, no arrowhead   |
| `-->` | Dashed line, no arrowhead  |
| `->>`  | Solid line, arrowhead      |
| `-->>` | Dashed line, arrowhead     |
| `-x`   | Solid line, cross at end   |
| `--x`  | Dashed line, cross at end  |

### Example: HTTP Request/Response

```mermaid
sequenceDiagram
    participant C as Client
    participant S as Server
    participant DB as Database

    C->>S: GET /api/users/42
    S->>DB: SELECT * FROM users WHERE id=42
    DB-->>S: {id: 42, name: "Alice"}
    S-->>C: 200 OK {"id": 42, "name": "Alice"}
```

### Example: Authentication Flow

```mermaid
sequenceDiagram
    participant U as User
    participant C as Client
    participant AS as Auth Server
    participant RS as Resource Server

    U->>C: Login (username, password)
    C->>AS: POST /token (credentials)
    AS-->>C: access_token, refresh_token
    C->>RS: GET /resource (Authorization: Bearer token)
    RS->>AS: Validate token
    AS-->>RS: Token valid
    RS-->>C: 200 OK (resource data)
    C-->>U: Display resource
```

### Activation Boxes

Use `+` / `-` to show when a participant is active.

```mermaid
sequenceDiagram
    participant C as Client
    participant S as Server

    C->>+S: Request
    S->>+S: Processing
    S-->>-S: Done
    S-->>-C: Response
```

### Alt / Opt / Loop

```mermaid
sequenceDiagram
    participant C as Client
    participant S as Server

    C->>S: Authenticate

    alt credentials valid
        S-->>C: 200 OK + token
    else credentials invalid
        S-->>C: 401 Unauthorized
    end

    opt token present
        C->>S: GET /protected
        S-->>C: 200 OK
    end

    loop retry (max 3)
        C->>S: Ping
        S-->>C: Pong
    end
```

### Notes

```mermaid
sequenceDiagram
    participant C as Client
    participant S as Server

    Note over C,S: TLS handshake
    C->>S: ClientHello
    S-->>C: ServerHello + Certificate
    Note right of C: Verify certificate
    C->>S: Key exchange
    S-->>C: Finished
```
