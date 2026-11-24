# AI Interview Prep — React + FastAPI + Groq

This is a full-stack conversion starter based on the supplied Streamlit app. The React frontend is in `frontend/`; the FastAPI service and reused interview-generation, evaluation, resume-analysis, recommendation and SQLite modules are in `backend/`.

## Run locally

### Backend
```bash
cd backend
python -m venv .venv
# Windows: .venv\\Scripts\\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env  # Windows: copy .env.example .env
# Add your GROQ_API_KEY and a long random APP_SECRET to .env
uvicorn main:app --reload --port 8000
```

### Frontend
```bash
cd frontend
npm install
cp .env.example .env
npm run dev
```

Open http://localhost:5173. API docs: http://localhost:8000/docs.

## Deploy backend to Render
- Create a new Web Service from this repository.
- Root Directory: `backend`
- Build Command: `pip install -r requirements.txt`
- Start Command: `uvicorn main:app --host 0.0.0.0 --port $PORT`
- Add `GROQ_API_KEY`, `GROQ_MODEL`, `APP_SECRET`, `FRONTEND_ORIGINS`, `DB_PATH=/var/data/interview_prep.db`.
- Attach a persistent disk mounted at `/var/data` (the included `render.yaml` describes this service and disk; persistent disks may require a paid Render plan).

## Deploy frontend to Vercel
- Import this Git repository into Vercel.
- Set Root Directory to `frontend`.
- Framework Preset: Vite.
- Add environment variable `VITE_API_URL` = your deployed Render API base URL, without a trailing slash.
- Deploy.

After deploying the frontend, update Render's `FRONTEND_ORIGINS` to the exact Vercel production URL, then redeploy the backend. Keep `GROQ_API_KEY` only on Render; never put it in Vercel or frontend code.

## Notes
- The original app's core Python interview logic and SQLite data layer are reused. The UI is rebuilt in React and the API is implemented in FastAPI.
- Interview sessions are held in backend process memory; if the backend restarts mid-interview, that in-progress session is lost. Completed history is stored in SQLite.
- SQLite persistence requires a persistent disk on Render. Configure a suitable persistent disk before relying on production user data.
- Set a unique, long `APP_SECRET` before production. This starter uses signed session tokens and should be reviewed/hardened before handling sensitive production accounts.
