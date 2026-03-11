---
title: "Troubleshooting"
description: "Solutions to common issues with Pix3lTools: database errors, authentication problems, MCP configuration, and Docker setup."
category: "Getting Started"
---

# Troubleshooting

## Database Connection Errors

### `TURSO_DATABASE_URL` or `TURSO_AUTH_TOKEN` missing

**Symptom:** App fails to start or returns 500 errors on all pages.

**Fix:** Make sure both variables are set in your `.env` or Vercel environment variables:
```env
TURSO_DATABASE_URL=libsql://your-database.turso.io
TURSO_AUTH_TOKEN=your-auth-token
```

For Docker deployments using sqld (local LibSQL):
```env
TURSO_DATABASE_URL=http://sqld:8080
TURSO_AUTH_TOKEN=dummy
```

---

### Database tables not found

**Symptom:** Errors like `no such table: boards` or `no such table: wiki_pages`.

**Fix:** The database needs to be initialized. Run:
```bash
# For Pix3lBoard
npm run db:setup

# For Pix3lWiki
npm run db:setup
```

With Docker, the `setup.sh` script handles this automatically. If you set up manually, run the db-init scripts as described in the [deployment guide](/docs/deployment).

---

## Authentication Issues

### Pix3lBoard and Pix3lWiki don't share login sessions

**Symptom:** User is logged in on Pix3lBoard but sees "Unauthorized" on Pix3lWiki (or vice versa).

**Cause:** The two apps use the same JWT format, but they must share the **same `JWT_SECRET`** to validate each other's tokens.

**Fix:** Make sure `JWT_SECRET` is identical in both apps:
```env
# Same value in both .env files
JWT_SECRET=your-random-secret-key-at-least-32-characters-long
```

If using Docker, the `JWT_SECRET` in `.env` is automatically shared by both containers via docker-compose.

---

### Login fails with "Invalid credentials"

**Symptom:** Correct email and password, but login fails.

**Possible causes:**
1. The admin account was not created during database initialization — re-run `setup.sh` or `db-init.sh`
2. The password was set incorrectly — use the admin panel to reset it
3. The `JWT_SECRET` is missing or too short (must be at least 32 characters)

---

### API returns 401 Unauthorized

**Symptom:** API calls return `{ "error": "Unauthorized" }`.

**Fix:**
- Make sure the `Authorization` header is set: `Authorization: Bearer <token>`
- If using a JWT token, check it has not expired (TTL: 7 days)
- If using an API key, verify the key was not revoked
- Check that you're calling the correct Pix3lBoard instance URL

---

## Pix3lMCP Issues

### Claude Code doesn't see Pix3lMCP tools

**Symptom:** After registering the MCP server, no tools appear in Claude Code.

**Fix:**
1. Verify the MCP server is running and reachable:
   ```bash
   curl https://your-mcp-deployment.vercel.app/api/mcp
   # Should return a 200 or method not allowed, not a connection error
   ```
2. Check the registration command used the correct URL:
   ```bash
   claude mcp list
   # Verify the pix3lboard entry points to the right URL
   ```
3. Remove and re-add if needed:
   ```bash
   claude mcp remove pix3lboard
   claude mcp add --transport http --scope user pix3lboard -- https://your-url/api/mcp
   ```

---

### Pix3lMCP authentication fails

**Symptom:** Tools appear but return errors like "authentication failed" or "unauthorized".

**Fix:**
1. Verify `PIX3LBOARD_URL` points to a running Pix3lBoard instance (include `https://` or `http://`)
2. If using API key: confirm `PIX3LBOARD_API_KEY` starts with `pk_live_` and has not been revoked
3. If using email/password: confirm the credentials are correct and the account exists
4. Check `WORKSPACE_IDS` contains valid workspace IDs (visible in the board URL)

---

### Pix3lMCP can't reach Pix3lBoard (Docker)

**Symptom:** MCP tools fail with connection errors in a Docker setup.

**Fix:** In Docker, use the container name as the hostname, not `localhost`:
```env
PIX3LBOARD_URL=http://pix3lboard:3000
```

`localhost` inside a container refers to the container itself, not the host machine.

---

## Docker Issues

### Containers start but apps return errors

**Symptom:** `docker compose up -d` succeeds but apps don't work.

**Fix:** Check the logs:
```bash
docker compose logs pix3lboard
docker compose logs pix3lwiki
docker compose logs sqld
```

Common causes:
- `JWT_SECRET` not set or too short
- Database not initialized (run `setup.sh` or db-init scripts)
- Port already in use (check with `lsof -i :3000`)

---

### File attachments not persisting after restart

**Symptom:** Uploaded files and thumbnails disappear after `docker compose restart`.

**Cause:** The blob storage volume is not mounted correctly.

**Fix:** Make sure the docker-compose.yml has the volume configured:
```yaml
pix3lboard:
  volumes:
    - blob-storage:/data/blob-storage

volumes:
  blob-storage:
```

And that `STORAGE_PROVIDER=local` and `LOCAL_STORAGE_PATH=/data/blob-storage` are set in the environment.

---

### Watchtower sends update notifications but doesn't deploy

**By design.** Watchtower in the pix3ltools-deploy stack is configured to **notify only**, not auto-update. To apply updates:
```bash
docker compose pull
docker compose up -d
```

---

## Vercel Issues

### Deploy fails with build errors

**Fix:** Check the Vercel build logs. Common issues:
- Framework preset not set to **Astro** (website) or **Next.js** (apps)
- Missing environment variables — add them in Vercel → Settings → Environment Variables
- For Pix3lBoard: `TURSO_DATABASE_URL` and `TURSO_AUTH_TOKEN` must be set before first deploy

---

### Changes pushed to GitHub but Vercel doesn't deploy

**Fix:**
1. Check Vercel → Settings → Git → Production Branch is set to `main`
2. Trigger a manual redeploy from Vercel → Deployments → Redeploy
3. If Vercel returns a 500 error, check [vercel-status.com](https://www.vercel-status.com) for ongoing incidents

---

## Getting Help

If the issue is not listed here, check the GitHub repository for the relevant project:

- [pix3lboard issues](https://github.com/Pix3ltools-lab/pix3lboard/issues)
- [pix3lwiki issues](https://github.com/Pix3ltools-lab/pix3lwiki/issues)
- [pix3lmcp issues](https://github.com/Pix3ltools-lab/pix3lmcp/issues)
- [pix3ltools-deploy issues](https://github.com/Pix3ltools-lab/pix3ltools-deploy/issues)
