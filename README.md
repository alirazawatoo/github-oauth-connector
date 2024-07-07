# GitHub OAuth Connector

An Express/TypeScript API and Angular client that connect a user to GitHub through OAuth, persist integration status in MongoDB, and support remote token revocation.

## Flow

```text
Angular connect button -> Express /connect -> GitHub authorization
                                            |
Angular integration UI <- callback + token <-+
         |                    |
         `-- status/remove ---+-- MongoDB user + integration records
```

The backend exchanges the authorization code, reads the GitHub user profile, stores the current access token, and redirects to the Angular integration screen. The client can then query connection status or request removal; removal revokes the GitHub token and deletes local records.

## Local setup

Requirements: Node.js 18, MongoDB, and a GitHub OAuth App whose callback URL is `http://localhost:3000/api/github/callback`.

```bash
cd backend
cp .env.example .env
npm install
npm run dev
```

Configure the database URL, OAuth client ID, client secret, token-exchange URL, and server port in `backend/.env`. Treat every value in local environment files as a secret or local placeholder.

In another terminal:

```bash
cd frontend
npm install
npm start
```

The Angular client expects the API at `http://localhost:3000/api/github` and runs at `http://localhost:4200`.

## Boundaries

- Access tokens are persisted in MongoDB and browser `localStorage`; production use needs encryption, tighter scopes, CSRF/state validation, and secure session design.
- Callback and client URLs are hard-coded for local development.
- The requested GitHub scope includes `repo` and `user`, which should be reduced to the minimum required by a real product.
