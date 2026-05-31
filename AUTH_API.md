# Forsaken Auth API

The bot exposes a simple HTTP auth endpoint via `keep_alive.py`.
You need to extend `keep_alive.py` (or run a separate Flask/FastAPI server)
to handle login requests from the loader.

## Endpoint

```
POST /auth
Content-Type: application/json

{
  "username": "string",
  "password": "string",
  "hwid":     "string"   // hardware fingerprint from the exe
}
```

## Response

```json
{ "success": true,  "message": "OK" }
{ "success": false, "message": "HWID mismatch. Contact support to reset." }
{ "success": false, "message": "Invalid password." }
{ "success": false, "message": "Username not found." }
{ "success": false, "message": "Your key has been disabled. Contact support." }
```

## HWID Binding Logic

- First login after registration → HWID is **null** → binds automatically.
- Subsequent logins → HWID must match the stored value.
- Admin uses `/hwidreset` in Discord to clear the HWID (login info preserved).

## Password Storage

Passwords are stored as SHA-256 hashes in `db.json`.
The loader should send the **plaintext** password over HTTPS;
the server hashes it before comparing.

## Key Format

Keys are only used during `/register` (Discord).
The loader uses **username + password** only — never the key.
