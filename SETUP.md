# 🚀 AI Risk Manager - Setup Guide

## Project Overview

**AI Risk Manager** is a production-grade fraud detection system that scores transactions in real-time with explainable decisions, Razorpay integration, and comprehensive audit trails.

- **Fraud Detection API**: FastAPI inference server (uvicorn)
- **Monitoring Dashboard**: Streamlit real-time analytics dashboard
- **Model**: LightGBM with SHAP explainability
- **Audit Trail**: Immutable decision logging
- **Integration**: Direct Razorpay test-mode API integration

---

## 📋 Prerequisites

### System Requirements
- Python 3.12+
- pip (Python package manager)
- Git (for version control)

### Razorpay Account
- Free Razorpay account: https://razorpay.com
- Test mode enabled by default (no activation needed)

---

## ⚙️ Local Development Setup

### 1. Clone the Repository

```bash
git clone <your-repo-url>
cd AI_Risk_Manager
```

### 2. Create Virtual Environment

```bash
# Windows
python -m venv venv
venv\Scripts\activate

# macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure Environment Variables

**Option A: Using provided development config**
```bash
# Copy the development configuration
cp .env.development .env
```

**Option B: Manual setup (if using .env.example)**
```bash
cp .env.example .env
```

Then edit `.env` with your credentials:

```env
# Razorpay Test Mode (from your Razorpay dashboard)
RAZORPAY_KEY_ID=rzp_test_TWr4fta9oXjr7X
RAZORPAY_KEY_SECRET=gUmodDq44vUvZwKk1pj2Fsom

# Local API settings
API_BASE=http://localhost:8000
MERCHANT_WEBHOOK_URL=http://localhost:8000/webhooks/merchant
```

### 5. Run the API Server

```bash
uvicorn api.main:app --reload --host 0.0.0.0 --port 8000
```

**Expected output:**
```
Uvicorn running on http://0.0.0.0:8000
Press CTRL+C to quit
```

### 6. Run the Dashboard (in a new terminal)

```bash
# Activate virtual environment first
source venv/bin/activate  # or venv\Scripts\activate on Windows

streamlit run dashboard/app.py
```

**Expected output:**
```
  You can now view your Streamlit app in your browser.

  Local URL: http://localhost:8501
  Network URL: http://YOUR_IP:8501
```

---

## 🔑 Razorpay Credentials Guide

### Getting Test Credentials

1. **Sign up** at https://razorpay.com (free account)
2. Go to **Dashboard** → **Settings** → **API Keys**
3. Ensure you're on **Test Mode** (toggle in top-right)
4. Copy your keys:
   - **Key ID**: Starts with `rzp_test_`
   - **Key Secret**: Your private key

### Test vs Live Mode

| Feature | Test Mode | Live Mode |
|---------|-----------|-----------|
| Key Prefix | `rzp_test_` | `rzp_live_` |
| Real Charges | ❌ No | ✅ Yes |
| KYC Required | ❌ No | ✅ Yes |
| Activation | ✅ Instant | 📋 Manual review |
| Use Case | Development | Production |

### Switching to Live Mode

1. Complete Razorpay KYC verification
2. Get Live credentials from dashboard
3. Update `.env.production` with live keys
4. Deploy to Render with live credentials

---

## 📁 Environment Files Explained

### `.env.development` (Development)
- **When to use**: Local development & testing
- **Contents**: Test credentials, localhost URLs
- **Security**: Included in .gitignore (safe to commit to templates)

### `.env.production` (Production/Render)
- **When to use**: Cloud deployment
- **Contents**: Template with placeholders
- **Security**: ⚠️ DO NOT commit actual credentials
- **Setup**: Set variables in Render Dashboard

### `.env.example` (Template)
- **Purpose**: Reference for new developers
- **Contents**: All available configuration options with descriptions
- **Instructions**: Copy → edit → use

---

## 🔐 Security Best Practices

### DO ✅
- ✅ Use test credentials for development
- ✅ Store `.env` files locally (not in git)
- ✅ Set production secrets in Render Dashboard
- ✅ Rotate credentials regularly
- ✅ Use environment variables for all secrets

### DON'T ❌
- ❌ Commit `.env` files to git
- ❌ Share credentials in messages/emails
- ❌ Use live credentials in development
- ❌ Push test credentials to production
- ❌ Store secrets in code

### .gitignore Configuration

```bash
# Already configured to ignore:
.env
.env.local
.env.*.local
```

Verify it's working:
```bash
git status  # Should NOT show .env files
```

---

## 🚀 Deployment to Render

### Step 1: Prepare for Deployment

1. Push code to GitHub (without .env files)
2. Create Render account: https://render.com
3. Connect your GitHub repo

### Step 2: Create Services

**For API Service:**
1. New → Web Service
2. Select your repository
3. **Name**: `ai-risk-manager-api`
4. **Runtime**: Python 3.12
5. **Build Command**: `pip install -r requirements.txt`
6. **Start Command**: `uvicorn api.main:app --host 0.0.0.0 --port $PORT`

**For Dashboard Service:**
1. New → Web Service
2. Select your repository
3. **Name**: `ai-risk-manager-dashboard`
4. **Runtime**: Python 3.12
5. **Build Command**: `pip install -r requirements.txt`
6. **Start Command**: `streamlit run dashboard/app.py --server.port $PORT --server.address 0.0.0.0`

### Step 3: Set Environment Variables

In Render Dashboard for **both services**:

```
RAZORPAY_KEY_ID=rzp_live_YOUR_LIVE_KEY
RAZORPAY_KEY_SECRET=YOUR_LIVE_SECRET
API_BASE=https://ai-risk-manager-api.onrender.com
DASHBOARD_ENVIRONMENT=production
LOG_LEVEL=INFO
```

### Step 4: Deploy

1. Click "Deploy" in Render Dashboard
2. Monitor logs for errors
3. Access your services:
   - API: `https://ai-risk-manager-api.onrender.com`
   - Dashboard: `https://ai-risk-manager-dashboard.onrender.com`

---

## 📊 Testing the API

### Test Endpoint (Health Check)

```bash
curl http://localhost:8000/health
```

Expected response:
```json
{
  "status": "healthy",
  "version": "1.0.0"
}
```

### Score a Transaction

```bash
curl -X POST http://localhost:8000/score \
  -H "Content-Type: application/json" \
  -d '{
    "order_id": "TEST_ORDER_123",
    "customer_email": "test@example.com",
    "amount": 5000,
    "currency": "INR"
  }'
```

### Create Order (Razorpay Integration)

```bash
curl -X POST http://localhost:8000/orders \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 5000,
    "currency": "INR",
    "receipt": "test-receipt-001"
  }'
```

---

## 🔍 Configuration Reference

### Core Settings

| Variable | Dev Value | Prod Value | Purpose |
|----------|-----------|-----------|---------|
| `API_ENVIRONMENT` | development | production | Environment type |
| `LOG_LEVEL` | DEBUG | INFO | Logging verbosity |
| `FRAUD_THRESHOLD_DECLINE` | 0.85 | 0.90 | Auto-decline risk score |
| `FRAUD_THRESHOLD_2FA` | 0.65 | 0.70 | 2FA requirement risk score |

### Razorpay Integration

| Variable | Example | Purpose |
|----------|---------|---------|
| `RAZORPAY_KEY_ID` | `rzp_test_abc123` | API authentication |
| `RAZORPAY_KEY_SECRET` | `secret_key` | API authentication |

### Webhook Configuration

| Variable | Purpose |
|----------|---------|
| `MERCHANT_WEBHOOK_URL` | Where fraud decisions are sent |
| `WEBHOOK_TIMEOUT` | Seconds to wait for webhook response |
| `WEBHOOK_RETRY_ATTEMPTS` | Number of retries on failure |

---

## 🐛 Troubleshooting

### "RAZORPAY_KEY_ID and RAZORPAY_KEY_SECRET must be set"

**Solution:**
```bash
# Verify .env file exists
ls -la .env

# Verify credentials are set
python -c "from dotenv import load_dotenv; load_dotenv(); import os; print('Key:', os.getenv('RAZORPAY_KEY_ID'))"
```

### "Connection refused" when accessing API

**Solution:**
```bash
# Make sure API is running
# Terminal 1:
uvicorn api.main:app --reload

# Terminal 2:
curl http://localhost:8000/health
```

### "Module not found" errors

**Solution:**
```bash
# Reinstall dependencies
pip install -r requirements.txt --force-reinstall
```

### Dashboard can't connect to API

**Solution:**
1. Verify API is running: `http://localhost:8000`
2. Check `API_BASE` in `.env`:
   ```env
   API_BASE=http://localhost:8000
   ```
3. Restart dashboard:
   ```bash
   streamlit run dashboard/app.py
   ```

---

## 📚 Useful Commands

### Development

```bash
# Start API (with auto-reload)
uvicorn api.main:app --reload

# Start Dashboard
streamlit run dashboard/app.py

# Run tests
pytest tests/

# Format code
black .

# Lint code
pylint **/*.py
```

### Environment Management

```bash
# Load specific environment
export $(cat .env.development | xargs)

# Verify environment variables
env | grep RAZORPAY

# Clear environment
unset RAZORPAY_KEY_ID RAZORPAY_KEY_SECRET
```

### Git

```bash
# Verify .env is ignored
git status  # Should NOT show .env

# Add to gitignore (if missing)
echo ".env" >> .gitignore
echo ".env.local" >> .gitignore
```

---

## 📖 Documentation Files

- **README.md**: Project overview & features
- **SETUP.md** (this file): Setup & deployment guide
- **docs/architecture.md**: System architecture
- **requirements.txt**: Python dependencies

---

## 🆘 Need Help?

1. **Check Razorpay Dashboard**: https://dashboard.razorpay.com
2. **Review Logs**: Check terminal output for error messages
3. **Run Tests**: `pytest tests/`
4. **Check Configuration**: Verify all env variables are set correctly

---

## ✅ Quick Verification Checklist

- [ ] Virtual environment activated
- [ ] Dependencies installed: `pip list | grep -E "fastapi|streamlit|lightgbm"`
- [ ] `.env` file exists with credentials
- [ ] Razorpay credentials are valid
- [ ] API runs: `curl http://localhost:8000/health`
- [ ] Dashboard loads: http://localhost:8501
- [ ] Tests pass: `pytest tests/`

---

**Last Updated**: 2026-09-01  
**Project**: AI Risk Manager  
**Version**: 1.0.0
