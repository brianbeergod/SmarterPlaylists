# Smarter Playlists

Create and run programmable Spotify playlists with a web-based editor and a Python API server.

## Quickstart (Local Development)

### Expected environment
- **OS:** Linux or macOS
- **Runtime:** Python 2.7 (server code uses Python 2 print syntax)
- **Services:** Redis 3+ (for auth/session/program storage)
- **Optional:** Nginx (for reverse proxy in production)

### Windows 10/11 notes
Running locally on Windows is possible but requires a Python 2.7 runtime and Redis. The easiest path is:
1. **Use WSL2 (recommended):** Install Ubuntu via WSL2 and follow the Linux instructions above inside the WSL shell.
2. **Or run natively on Windows (legacy):**
   - Install Python 2.7 and ensure `python` is on your PATH.
   - Install Redis for Windows (e.g., via WSL2 or a compatible Windows build).
   - Use PowerShell equivalents of the commands below:
     ```powershell
     $env:SPOTIPY_CLIENT_ID="..."
     $env:SPOTIPY_CLIENT_SECRET="..."
     $env:SPOTIPY_REDIRECT_URI="http://localhost:8000/auth.html"
     ```
   - Start Redis, then run the same `server/start_debug_server` and `python -m SimpleHTTPServer 8000` commands from PowerShell or Git Bash.

### Start the service
1. **Export required Spotify credentials** (used by `server/spotify_auth.py`):
   ```bash
   export SPOTIPY_CLIENT_ID="<spotify client id>"
   export SPOTIPY_CLIENT_SECRET="<spotify client secret>"
   export SPOTIPY_REDIRECT_URI="http://localhost:8000/auth.html"
   ```
2. **Start Redis** (example on Linux/macOS):
   ```bash
   redis-server
   ```
3. **Start the API server**:
   ```bash
   cd server
   ./start_debug_server
   ```
   The API listens on `http://localhost:5000/SmarterPlaylists/`.
4. **Serve the web UI** (static files):
   ```bash
   cd web
   python -m SimpleHTTPServer 8000
   ```
   Then open `http://localhost:8000/index.html`.

---

## Project Overview
Smarter Playlists combines a browser-based editor (static HTML/JS) with a Python Flask API that compiles and runs playlist programs against Spotify. The UI communicates with the API at `/SmarterPlaylists/*`.

## Architecture
- **Web UI:** Static HTML/JS in `web/`.
- **API Server:** Flask app in `server/flask_server.py`.
- **Redis:** Stores auth tokens, user/program metadata, and scheduling info.

## Configuration
The API relies on Spotify credentials defined via environment variables:
- `SPOTIPY_CLIENT_ID`
- `SPOTIPY_CLIENT_SECRET`
- `SPOTIPY_REDIRECT_URI`

For local development, the default redirect URI should match the static server:
`http://localhost:8000/auth.html`.

## Running in Production (Nginx)
See `nginx/smarterplaylists` for an example reverse-proxy configuration that:
- Serves the static site from `/home/www/smarterplaylists/`.
- Proxies `/SmarterPlaylists` to the Flask server on port 5000.
- Proxies `/api` to port 8000 (older API layout).

## Repository Layout
- `server/` — Flask API server, compiler, scheduler, Spotify auth.
- `web/` — Static frontend assets and HTML pages.
- `nginx/` — Example Nginx site configs.
- `docs/` — Historical notes and setup references.

## Additional Notes
- The API server expects Redis on `localhost:6379` (see `server/flask_server.py`).
- `server/start_simple_server` and `server/start_debug_server` source a `credentials.sh` file in their original environment; for local use, export the variables directly instead.

## Documentation & References
- Historical setup notes: `docs/setup.txt`
- Auth flow notes: `docs/auth.txt`
- Refactor notes: `docs/refactornotes.txt`
