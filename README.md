# quali.chat

Equipping your decentralized conversations with superior quality and bringing all your token-gated communities into one easy-to-use chat and collaboration app.

## Key Features

- **Web3 Authentication:** Sign in with Ethereum (SIWE) and OIDC for seamless wallet-based login.
- **Matrix Protocol:** Robust, federated chat infrastructure.
- **Token-Gated Access:** Room membership and permissions based on on-chain balances.
- **Multi-Platform:** Web, Android, and iOS clients.

---

## Repository Structure

- **[quali-chat-core](https://github.com/quali-chat/quali-chat-core):** Core backend logic for token-gated access, room management, and bot orchestration.
- **[synapse-homeserver-patch](https://github.com/quali-chat/synapse-homeserver-patch):** Custom Synapse server configuration and modules for Matrix.
- **[quali-chat-web](https://github.com/quali-chat/quali-chat-web):** Web client, a customized fork of element-web.
- **[quali-chat-android](https://github.com/quali-chat/quali-chat-android) / [quali-chat-ios](https://github.com/quali-chat/quali-chat-ios):** Native mobile clients.

---

## Prerequisites

Set up the following services. These are open source repositories. quali.chat requires a few custom configurations, which are defined here.

- Protocols
  - Matrix protocol ([https://matrix.org/](https://matrix.org/))
  - OIDC protocol ([https://auth0.com/docs/authenticate/protocols/openid-connect-protocol](https://auth0.com/docs/authenticate/protocols/openid-connect-protocol))

- Implementation and configuration
  - Synapse: Matrix homeserver ([element-hq/synapse](https://github.com/element-hq/synapse))
    - [quali-chat/synapse-homeserver-patch](https://github.com/quali-chat/synapse-homeserver-patch.git) contains configurations, custom modules, and pre-registered bot accounts needed for quali.chat.
  - SIWE: Sign in with Ethereum OIDC server ([spruceid/siwe-oidc](https://github.com/spruceid/siwe-oidc))
    - Set `CLIENT_ID` and `CLIENT_SECRET` as defined in the homeserver setup, and set `REDIRECT_URI` to `https://MATRIX_HOMESERVER_URL/_synapse/client/oidc/callback`.
  - Both the SIWE-OIDC and Matrix homeserver require secure (HTTPS) connections. You must use TLS keys to ensure encrypted communication.

---

## Run custom services

Once you have your Matrix homeserver and SIWE OIDC provider running, you can immediately set up and use the quali.chat open source components.

- Node.js (latest LTS)
- Docker & Docker Compose
- Alchemy API key ([get one](https://dashboard.alchemy.com/))
- Redis (for caching/session management)

### 1. Setup quali-chat-core

This is the core logic that manages user room memberships and permissions.

- In a new terminal, clone and set up the core backend:

  ```sh
  git clone https://github.com/quali-chat/quali-chat-core.git
  cd quali-chat-core
  npm install
  cp .env.example .env
  # Edit .env with your API keys and configuration
  docker run --name redis -p 6379:6379 -d redis
  npm run dev
  ```

---

### 2. Setup quali-chat-web

- In a new terminal, clone and set up the web client:

  ```sh
  git clone https://github.com/quali-chat/quali-chat-web.git
  cd quali-chat-web
  yarn install
  cp config.sample.json config.json # edit the Matrix homeserver URL
  yarn start
  ```

### 3. Setup native clients

Setup custom Android and IOS client as per the README.

- [quali-chat/quali-chat-android](https://github.com/quali-chat/quali-chat-android.git)
- [quali-chat/quali-chat-ios](https://github.com/quali-chat/quali-chat-ios.git)

## Quick Start Guide for local development

The following guide helps you set up a local development environment running all services.

### 1. Setup SIWE OIDC Provider

- Clone the official SIWE OIDC provider:

  ```sh
  git clone https://github.com/spruceid/siwe-oidc.git
  cd siwe-oidc
  ```

- Follow the instructions in the README.
- Install dependencies and configure your `PROJECT_ID` environment variable with your WalletConnect(reown now) project ID.
- Follow the [Development section of the spruceid/siwe-oidc README](https://github.com/spruceid/siwe-oidc?tab=readme-ov-file#development) to set up a local development environment.
- Check environment variables: `CLIENT_ID`, `CLIENT_SECRET`, `REDIRECT_URI`.
- If using the docker-compose file:
  - In `test/docker-compose.yml`, add your Matrix homeserver callback URLs (`https://MATRIX_HOMESERVER_URL/_synapse/client/oidc/callback`) to `redirect_uris` under `SIWEOIDC_DEFAULT_CLIENTS`.
- **Important:** Both the OIDC and Matrix servers require secure (HTTPS) connections. You must use TLS keys to ensure encrypted communication.
- Ensure the OIDC provider is running and accessible.

---

### 2. Setup Synapse Server (synapse-homeserver-patch)

- Clone the Synapse patch repository:

  ```sh
  git clone https://github.com/quali-chat/synapse-homeserver-patch.git
  cd synapse-homeserver-patch
  ```

- Create and activate a Python virtual environment:

  ```sh
  make venv && . ./env/bin/activate
  ```

- Reset and set up the Matrix server:

  ```sh
  make reset
  ```

- Start the server in development mode:

  ```sh
  make dev
  ```

- Ensure the Synapse server is running and configured for OIDC.

---

### 3. Run custom services

Refer to the earlier section [Run custom services](#run-custom-services)

---

## Troubleshooting

- **Invalid access token:** Clear the `data` directory in quali-chat-core.
- **Password login disabled:** Ensure `password_config` is set to `enabled: true` in your Matrix server config.
- **Redis/Database issues:** Ensure Redis is running and accessible.
- **SIWE OIDC build errors:** Uncomment the `RUN apk add --no-cache python3 make g++` line in the Dockerfile.

---

## References

- [Matrix API Documentation](https://matrix.org/docs/api/)
- [OIDC Specifications](https://openid.net/developers/specs/)
- [SIWE (Sign-In with Ethereum)](https://docs.login.xyz/)
- [Alchemy API Documentation](https://docs.alchemy.com/)

---
