# Tata Steel PDF Extractor — Full Stack

Extract Parameters + Results from Tata Steel tensile test certificates.

**Stack (100% free):**
- React frontend → Vercel (free)
- FastAPI backend → Render.com (free)
- Vision AI → GitHub Models / GPT-4o mini (free, 50 req/day)

---

## Project Structure

```
tata-steel-extractor/
├── backend/
│   ├── main.py              ← FastAPI app
│   └── requirements.txt
├── frontend/
│   ├── src/
│   │   ├── App.js           ← React UI
│   │   ├── App.css
│   │   └── index.js
│   ├── public/
│   │   └── index.html
│   ├── package.json
│   └── .env.example
├── render.yaml              ← Render deploy config
├── vercel.json              ← Vercel deploy config
└── README.md
```

---

## Step 1 — Deploy Backend on Render (free)

1. Push this repo to GitHub
2. Go to https://render.com → Sign up free
3. Click **New → Web Service**
4. Connect your GitHub repo
5. Settings:
   - **Root directory**: `backend`
   - **Runtime**: Python 3
   - **Build command**:
     ```
     apt-get update && apt-get install -y tesseract-ocr poppler-utils libgl1 && pip install -r requirements.txt
     ```
   - **Start command**: `uvicorn main:app --host 0.0.0.0 --port $PORT`
   - **Plan**: Free
6. Under **Environment Variables**, add:
   - `GITHUB_TOKEN` = your `ghp_...` token from github.com/settings/tokens
7. Click **Deploy**
8. Wait ~5 min. Your backend URL will be: `https://YOUR-APP.onrender.com`

> ⚠️ Free Render instances sleep after 15 min of inactivity. First request after sleep takes ~30s.

---

## Step 2 — Deploy Frontend on Vercel (free)

1. Go to https://vercel.com → Sign up free with GitHub
2. Click **New Project** → Import your repo
3. Settings:
   - **Framework**: Create React App
   - **Root directory**: `frontend`
4. Under **Environment Variables**, add:
   - `REACT_APP_API_URL` = `https://YOUR-APP.onrender.com` (from Step 1)
5. Click **Deploy**
6. Your app is live at `https://YOUR-PROJECT.vercel.app`

---

## Step 3 — Local Development

**Backend:**
```bash
cd backend
pip install -r requirements.txt
# Install system deps (Ubuntu/WSL):
sudo apt install tesseract-ocr poppler-utils
# Windows: install manually (paths in original script)

export GITHUB_TOKEN=ghp_your_token_here
uvicorn main:app --reload
# Runs at http://localhost:8000
```

**Frontend:**
```bash
cd frontend
cp .env.example .env
# Edit .env: REACT_APP_API_URL=http://localhost:8000
npm install
npm start
# Runs at http://localhost:3000
```

---

## API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| GET | `/health` | Health check |
| POST | `/extract` | Extract data → JSON |
| POST | `/extract-excel` | Extract data → .xlsx download |

---

## Getting Your Free GitHub Token

1. Go to https://github.com/settings/tokens
2. Click **Generate new token (classic)**
3. Give any name, leave all scopes unchecked
4. Click **Generate token**
5. Copy the `ghp_...` token

> Free tier: 50 requests/day per token. Each PDF page = 1 request.

---

## Notes

- Tesseract OCR handles Parameters table (fast, no API calls)
- GPT-4o mini handles Results table (1 API call per page)
- Excel output has 3 sheets: Combined, Parameters, Results Table
