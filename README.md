# Ark
[<img alt="github" src="https://img.shields.io/badge/%20GitHub-zeljkovranjes%2Fark-orange" height="20">](https://github.com/zeljkovranjes/brute)
[<img alt="version" src="https://img.shields.io/badge/%20Release-v0.1.0-green" height="20">](/)

Ark is a monolithic backend application written in Rust that focuses heavily on Identity and Access Management (IAM).
While it was originally intended to be closed-source for a side project, it has been released publicly.

This project is not production-ready.
Some fundamental components are missing, and the frontend has not been implemented yet. Use this project as a foundation, not a drop-in solution.

- **IAM-First Design** — Ark is built around roles, permissions, and OAuth-based identity from the ground up.

- **Explicit Authorization Model** — Roles and permissions must exist before they can be linked. Nothing is implicitly created.

- **Monolithic Architecture** — All services live in a single application. No microservices, no network overhead.

- **OAuth Integration** — Supports OAuth providers (example: Discord) for authentication and user provisioning.

## Installation

This section walks through setting up Ark locally for development and experimentation.

## Prerequisites

Ensure the following dependencies are installed before continuing:
* Rust (stable)
* Cargo
* PostgreSQL
* Redis
* OAuth Provider (e.g. Discord)

### From Source
```
git clone https://github.com/zeljkovranjes/ark.git
cd ark
cargo build
cargo run
```

### Environment Variables
```
# PostgreSQL
PG_HOST=
PG_USER=
PG_PASSWORD=
PG_DBNAME=

# Redis
REDIS_HOST=
REDIS_USER=
REDIS_PASSWORD=
REDIS_DBNAME=

# OAuth (Discord example)
DISCORD_CLIENT_ID=
DISCORD_CLIENT_SECRET=
DISCORD_AUTH_URL=https://discord.com/oauth2/authorize
DISCORD_TOKEN_URL=https://discord.com/api/oauth2/token
DISCORD_REVOCATION_URL=https://discord.com/api/oauth2/token/revoke

# OAuth redirect
OAUTH2_REDIRECT_URL=http://localhost:3000/auth/callback

# Security
COOKIE_ENCRYPTION_KEY=CHANGE_THIS_TO_A_LONG_RANDOM_KEY
```

## IAM Overview
Ark’s IAM system is intentionally strict.
Roles and permissions are *case-sensitive*, and entities must exist before being referenced.

All IAM-related logic is commented in the source code.

### Creating a User
```rust
let user = User::builder()
    .oauth_id("oauth_id")
    .oauth_provider("discord")
    .username("hello")
    .permission(vec!["special.permission".to_string()])
    .role(vec!["Admin".to_string()])
    .build();

UserManager::create_user(user).unwrap();
```

### Creating a Role
```rust
let role = Role::builder()
    .role_name("Admin")
    .build();

RoleManager::create_role(role).unwrap();
```

### Updating a Role
```rust
RoleManager::update_role(
    "dd2546c3-e34a-4fcb-9b12-1a96eb6873e3",
    "role_name",
    "Admin"
);

RoleManager::update_role(
    "Admin",
    "role_name",
    "Administrator"
);
```

### Linking a Permission to a Role
```rust
RoleManager::link_permission_to_role("Admin", "ban.user").unwrap();
```

### Creating a Permission
```rust
let permission = Permission::builder()
    .permission_name("Ban User")
    .permission_key("ban.user")
    .build();

PermissionManager::create_permission(permission).unwrap();
```

### Updating a Permission
```rust
PermissionManager::update_permission(
    "dd2546c3-e34a-4fcb-9b12-1a96eb6873e3",
    "permission_name",
    "admin ban user."
);

PermissionManager::update_permission(
    "admin ban user.",
    "permission_key",
    "admin.ban.key"
);
```

## Tests
There are currently **no automated tests**.
This project is still under active development and should be treated as experimental.

## Final Notes
Ark is meant to be extended, not deployed as-is.
If you plan on using this as a base for a real application, expect to add:
* Migrations
* Tests
* Frontend
* Deployment tooling
* Security hardening
