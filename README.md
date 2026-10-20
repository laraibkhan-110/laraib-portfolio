# Laraib Portfolio - backend folder

`server.js` is the backend. It has **no npm packages** (Node.js 18+).
It serves the website from `public/` and the API (login, save content, image upload).
The `public/` folder is included because the server needs it to show the site.

## Run on your computer
1. Install Node.js (nodejs.org).
2. In this folder run: `node server.js`
3. Open http://localhost:3000
4. Open the dashboard: click the page and press **Ctrl+Alt+D**, or add `#admin` to the end of the link (works on phones).
   First time it asks you to create a password (min 8 characters).

## Put it online (website + backend together)
GitHub Pages cannot run a backend. Use a host that runs Node.js. Push this folder to a GitHub repo, then:

### Render (easy)
1. render.com -> New -> Web Service -> connect your GitHub repo.
2. Runtime: Node. Build command: `echo ok`. Start command: `node server.js`.
3. Environment: `ADMIN_PASSWORD` = your password (min 8 characters).
4. **Persistent disk:** add a Disk (mount path `/var/data`) and set `DATA_DIR=/var/data`. This needs a paid instance.
   Without a disk, content and uploaded images are deleted every time the service restarts or redeploys.
   (`render.yaml` in this folder already contains these settings.)

### Railway / Fly.io / VPS / Docker
- Start command: `node server.js`. Set `PORT` if the host asks for it.
- Mount a volume and set `DATA_DIR` to its path so `content.json`, `auth.json` and `uploads/` survive restarts.
- Docker: `docker build -t portfolio .` then `docker run -p 3000:3000 -v portfolio-data:/app/data portfolio`

Always use HTTPS (hosts do this automatically).

## Files
- `server.js` - backend
- `default-content.json` - starting content
- `public/` - the website
- `data/` - created by the backend: `content.json`, `auth.json` (password hash, keep private), `uploads/`

## API
| Method | Path | Who | What |
|---|---|---|---|
| GET | /api/content | everyone | skills, certificates, links, about |
| GET | /api/me | everyone | `{admin, setup}` |
| POST | /api/setup | first run only | create password |
| POST | /api/login | everyone (5 tries / 10 min) | `{password}` |
| POST | /api/logout | everyone | end session |
| POST | /api/password | admin | change password |
| PUT | /api/content | admin | save content |
| POST | /api/upload | admin | `{data:"data:image/jpeg;base64,..."}` |
| DELETE | /api/upload/:id | admin | delete image |

Forgot the password? Stop the server, delete `data/auth.json`, start again and create a new one.
