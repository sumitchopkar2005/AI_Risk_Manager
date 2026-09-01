# 🔐 AI Risk Manager - Credentials Reference

## Overview

This document details all credentials required to run the AI Risk Manager project across different environments.

---

## 🔑 Required Credentials

### 1. Razorpay API Keys (REQUIRED)

**Where to Get:**
1. Visit: https://dashboard.razorpay.com/app/keys
2. Sign in with your Razorpay account
3. Look for your **Test Mode** keys (top section)

**Credentials:**

| Key | Example | Status |
|-----|---------|--------|
| `RAZORPAY_KEY_ID` | `rzp_test_TWr4fta9oXjr7X` | ✅ Provided |
| `RAZORPAY_KEY_SECRET` | `gUmodDq44vUvZwKk1pj2Fsom` | ✅ Provided |

**Security Notes:**
- 🔒 NEVER share these keys
- 🔒 NEVER commit to git
- 🔒 Rotate periodically
- 🔒 Use different keys for test and live

**Your Test Credentials:**
```env
RAZORPAY_KEY_ID=rzp_test_TWr4fta9oXjr7X
RAZORPAY_KEY_SECRET=gUmodDq44vUvZwKk1pj2Fsom
```

---

## 📦 Environment-Specific Credentials

### Development Environment (Local)

**File**: `.env.development`  
**Status**: ✅ Ready to use

```env
# Razorpay Test Credentials
RAZORPAY_KEY_ID=rzp_test_TWr4fta9oXjr7X
RAZORPAY_KEY_SECRET=gUmodDq44vUvZwKk1pj2Fsom

# API Settings
API_BASE=http://localhost:8000
MERCHANT_WEBHOOK_URL=http://localhost:8000/webhooks/merchant
```

**Setup Command:**
```bash
cp .env.development .env
```

### Production Environment (Render/Cloud)

**File**: `.env.production` (template only)  
**Status**: ⚠️ Requires setup in Render Dashboard

**Steps to Deploy to Render:**

1. **Create Render Account**: https://render.com
2. **Connect GitHub Repository**
3. **Create Services** (API & Dashboard)
4. **Set Environment Variables** in Render Dashboard:

```
RAZORPAY_KEY_ID=rzp_live_YOUR_LIVE_KEY_HERE
RAZORPAY_KEY_SECRET=YOUR_LIVE_KEY_SECRET_HERE
API_BASE=https://ai-risk-manager-api.onrender.com
API_ENVIRONMENT=production
LOG_LEVEL=INFO
```

**IMPORTANT**: 
- ⚠️ DO NOT put credentials in `.env.production` file
- ⚠️ Set all secrets in Render Dashboard
- ⚠️ Use LIVE keys only after Razorpay activation

---

## 🔄 Credential Lifecycle

### Test Mode (Development)

```
1. Create Razorpay Account ✅
   └─ Automatically in Test Mode

2. Get Test Keys ✅
   └─ Available immediately
   └─ No activation needed

3. Setup Local Development
   ├─ Copy .env.development → .env
   ├─ Run API: uvicorn api.main:app --reload
   └─ Run Dashboard: streamlit run dashboard/app.py

4. Test Transactions ✅
   └─ Use test cards
   └─ No real charges
```

### Live Mode (Production)

```
1. Activate Razorpay Account
   ├─ Complete KYC verification
   ├─ Submit business details
   └─ Wait for approval (~1-2 days)

2. Get Live Keys
   └─ Dashboard → Settings → API Keys
   └─ Switch toggle to "LIVE"

3. Deploy to Production
   ├─ Set RAZORPAY_KEY_ID (live)
   ├─ Set RAZORPAY_KEY_SECRET (live)
   └─ Deploy to Render

4. Process Live Transactions ✅
   └─ Real charges applied
   └─ Money deposited to account
```

---

## 🔍 Credential Locations Reference

### Razorpay Dashboard

| Information | Location | Purpose |
|-------------|----------|---------|
| API Keys | Settings → API Keys | Authentication |
| Test/Live Toggle | Top-right corner | Switch modes |
| Transaction Logs | Transactions | View payments |
| Webhooks | Settings → Webhooks | Configure callbacks |

### Your Project

| File | Purpose | Contains Secrets |
|------|---------|------------------|
| `.env.development` | Local dev config | ✅ YES (test keys) |
| `.env.production` | Prod template | ❌ NO (placeholders) |
| `.env.example` | Reference template | ❌ NO (examples only) |
| `render.yaml` | Deployment config | ❌ NO |

---

## ⚙️ Configuration Options by Environment

### Key Differences

| Setting | Development | Production |
|---------|-------------|------------|
| **Razorpay Mode** | Test | Live |
| **API URL** | `localhost:8000` | `onrender.com` |
| **Log Level** | DEBUG | INFO |
| **CORS** | Open (*) | Restricted |
| **Fraud Thresholds** | 0.85 / 0.65 | 0.90 / 0.70 |

---

## 🚨 Security Checklist

### Before Committing Code
- [ ] `.env` file is in `.gitignore`
- [ ] No hardcoded credentials in source code
- [ ] All secrets in environment variables
- [ ] `.env.development` contains only TEST credentials

### Before Deploying to Production
- [ ] Razorpay account is fully activated
- [ ] Have LIVE credentials (not test)
- [ ] Set secrets in Render Dashboard (not .env file)
- [ ] Enable HTTPS/SSL
- [ ] Configure CORS for your domain only
- [ ] Set up webhook verification

### After Deployment
- [ ] Test with real transaction (small amount)
- [ ] Verify webhook delivery
- [ ] Monitor error logs
- [ ] Setup backup credentials

---

## 📋 Setup Verification

### Verify Test Credentials

```bash
# Check if credentials are loaded
python -c "
from dotenv import load_dotenv
import os
load_dotenv()
print('✅ Key ID:', os.getenv('RAZORPAY_KEY_ID'))
print('✅ Has Secret:', bool(os.getenv('RAZORPAY_KEY_SECRET')))
"
```

### Test API Connection

```bash
# Run API server
uvicorn api.main:app --reload

# In another terminal, test health endpoint
curl http://localhost:8000/health

# Expected response: {"status": "healthy", "version": "1.0.0"}
```

### Test Razorpay Connection

```bash
# Create a test order
curl -X POST http://localhost:8000/orders \
  -H "Content-Type: application/json" \
  -d '{"amount": 1000, "currency": "INR"}'

# Expected: Returns order details with razorpay_order_id
```

---

## 🔄 Credential Rotation Guide

### When to Rotate
- Every 90 days (security best practice)
- After team member leaves
- If credentials are leaked
- When deploying to production

### How to Rotate Test Credentials
1. Go to Razorpay Dashboard
2. Settings → API Keys
3. Click "Regenerate Test Keys"
4. Update `.env.development`
5. Restart API server

### How to Rotate Live Credentials
1. Go to Razorpay Dashboard
2. Settings → API Keys (switch to LIVE)
3. Click "Regenerate Live Keys"
4. Update Render Dashboard environment variables
5. Redeploy application

---

## 🆘 Troubleshooting Credentials

### "Invalid Razorpay credentials"

**Check:**
```bash
# Verify credentials are set
echo $RAZORPAY_KEY_ID
echo $RAZORPAY_KEY_SECRET

# Verify format
# Should start with rzp_test_ or rzp_live_
```

**Fix:**
1. Copy correct credentials from Razorpay Dashboard
2. Update `.env` file
3. Restart API server
4. Test with: `curl http://localhost:8000/health`

### "401 Unauthorized" from Razorpay API

**Possible Causes:**
- ❌ Wrong credentials
- ❌ Expired credentials
- ❌ Using test credentials in live mode
- ❌ Using live credentials in test environment

**Fix:**
```bash
# Verify you're using test credentials for development
grep "rzp_test" .env

# Should output: RAZORPAY_KEY_ID=rzp_test_...
```

### "Key Secret is missing"

**Fix:**
```bash
# Ensure both are set in .env
RAZORPAY_KEY_ID=rzp_test_TWr4fta9oXjr7X
RAZORPAY_KEY_SECRET=gUmodDq44vUvZwKk1pj2Fsom
```

---

## 📞 Support

- **Razorpay Support**: https://support.razorpay.com
- **Razorpay API Docs**: https://razorpay.com/docs/api/
- **Project Issues**: Check `tests/` folder for test cases

---

**Last Updated**: 2026-09-01  
**Credentials Status**: ✅ Ready for Development  
**Next Step**: Copy `.env.development` → `.env` and run locally
