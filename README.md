# JobGuard AI

An AI-assisted job and internship offer safety checker. It highlights possible
warning signs, reassuring details, and practical verification steps. It is not a
fraud determination.

The app also includes sample offers, a four-point recruiter verification
checklist with a suggested next step, and a downloadable assessment.

## 1. Requirements
- Python 3.10 or newer (Python 3.11 recommended)
- A Gemini API key from https://aistudio.google.com/apikey
- VS Code (recommended)

## 2. Setup on Windows (PowerShell)
Open this project folder in VS Code, open Terminal > New Terminal, then run:

```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
Copy-Item .env.example .env
```

Open `.env` and replace `put_your_gemini_api_key_here` with your actual Gemini API key. Save the file. Never share the key or upload `.env` to GitHub.

If PowerShell blocks activation, run:
```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.venv\Scripts\Activate.ps1
```

## 3. Run the app
```powershell
streamlit run app.py
```
Open the local URL shown in the terminal, usually http://localhost:8501. The
verification checklist stays in the current browser session. Offer text is sent
to Gemini for analysis and is not saved by this app.

## 4. macOS / Linux
```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
# Edit .env and add GEMINI_API_KEY
streamlit run app.py
```

## 5. Docker test
```bash
docker build -t jobguard-ai .
docker run --rm -p 8080:8080 --env-file .env jobguard-ai
```
Open http://localhost:8080.

## 6. Deploy to Streamlit Community Cloud

1. Push this project to a GitHub repository. Do not include `.env`,
   `.streamlit/secrets.toml`, or your Gemini API key.
2. Sign in at https://share.streamlit.io/ and select **Create app**.
3. Choose the repository, branch, and `app.py` as the main file.
4. In **Advanced settings → Secrets**, add:

   ```toml
   GEMINI_API_KEY = "your-gemini-api-key"
   GEMINI_MODEL = "gemini-2.5-flash"
   ```

5. Deploy. Streamlit Cloud installs the packages from `requirements.txt`.

The app reads the Gemini key from Streamlit secrets in the cloud and from the
environment or `.env` file when run locally.

## 7. Deploy to Google Cloud Run
The app includes a Dockerfile for Cloud Run. Install and sign in to Google Cloud
CLI first. Set a billing-enabled Google Cloud project and enable Cloud Run, Cloud
Build, and Secret Manager APIs. Create a Secret Manager secret named
`GEMINI_API_KEY`, add your Gemini API key to it, and grant the Cloud Run service
identity permission to access that secret. Then:

```bash
gcloud auth login
gcloud config set project YOUR_PROJECT_ID
gcloud services enable run.googleapis.com cloudbuild.googleapis.com secretmanager.googleapis.com
gcloud run deploy jobguard-ai --source . --region asia-south1 --allow-unauthenticated --set-env-vars GEMINI_MODEL=gemini-2.5-flash --set-secrets GEMINI_API_KEY=GEMINI_API_KEY:latest
```

Do not put the key directly in source code, the Docker image, or command
history. You can also deploy through Google Cloud Console and configure the
secret under Variables & Secrets.

## Notes
- The result is an AI-assisted risk assessment, not proof that a job offer is fraudulent or genuine.
- The app does not save offer text or analysis history. Cloud Run's local
  filesystem is ephemeral; a persistent multi-user history feature would need a
  managed database and an explicit data-retention policy.
