# 🚀 PayFlow Render Deployment Guide

## 📋 Pre-Deployment Checklist

### ✅ Required Accounts
- [ ] GitHub account (repository connected)
- [ ] Render account (https://render.com)
- [ ] MongoDB Atlas account (database ready)
- [ ] Redis Cloud account (optional, for caching)
- [ ] Stripe account (API keys ready)
- [ ] OpenAI/Gemini account (AI features)

### ✅ Files Created
- [x] `render.yaml` - Infrastructure configuration
- [x] `backend/build.sh` - Build script
- [x] `frontend/.env.production` - Production environment variables
- [x] `backend/src/index.js` - Updated with production config

---

## 🎯 Deployment Strategy

**Total Cost: ~$7/month**
- Backend (Render Web Service - Hobby): $7/month
- Frontend (Render Static Site): FREE
- MongoDB Atlas: FREE (512MB tier)
- Redis Cloud: FREE (30MB tier)

---

## 📝 Step-by-Step Deployment

### Phase 1: Prepare Repository

#### 1.1 Commit Changes
```powershell
# Review changes
git status

# Add all files
git add .

# Commit
git commit -m "Configure for Render deployment"

# Push to GitHub
git push origin main
```

---

### Phase 2: Deploy Backend on Render

#### 2.1 Create Web Service

1. **Go to** https://dashboard.render.com
2. **Sign up/Login** with GitHub
3. **Click "New +"** → **"Web Service"**
4. **Connect your repository**: `BassemHazemDev/PayFlowProd`
5. **Select branch**: `main` (or `dev`)

#### 2.2 Configure Backend Service

**Basic Settings:**
- **Name**: `payflow-backend`
- **Region**: `Oregon` (or closest to your users)
- **Branch**: `main`
- **Root Directory**: `backend`
- **Runtime**: `Node`
- **Build Command**: `npm install`
- **Start Command**: `npm start`

**Plan Selection:**
- **Select**: `Starter` ($7/month) or `Hobby`
  - ✅ Includes 512MB RAM
  - ✅ Includes persistent disk storage
  - ✅ Always-on (no sleeping)

#### 2.3 Add Environment Variables

Click "Advanced" → "Add Environment Variable" and add:

**Required Variables:**

| Key | Value | Notes |
|-----|-------|-------|
| `NODE_ENV` | `production` | Required |
| `PORT` | `10000` | Render default |
| `ENABLE_CRON` | `true` | Enable daily billing |
| | | |
| **Database** | | |
| `MONGO_URL` | `mongodb+srv://...` | From MongoDB Atlas |
| `MONGO_DB` | `payflow_production` | Your database name |
| | | |
| **Redis (Optional)** | | |
| `REDIS_HOST` | `redis-xxxxx.c1.us-east-1-2.ec2.cloud.redislabs.com` | From Redis Cloud |
| `REDIS_PORT` | `6379` | Default port |
| `REDIS_PASSWORD` | `your_redis_password` | From Redis Cloud |
| | | |
| **JWT Settings** | | |
| `JWT_SECRET` | `your_super_secret_key_here` | Generate strong key |
| `JWT_EXPIRES_IN` | `90d` | Token expiration |
| `JWT_COOKIE_EXPIRES_IN` | `90` | Cookie expiration (days) |
| | | |
| **OAuth (Google)** | | |
| `GOOGLE_CLIENT_ID` | `xxxxx.apps.googleusercontent.com` | From Google Console |
| `GOOGLE_CLIENT_SECRET` | `GOCSPX-xxxxx` | From Google Console |
| | | |
| **Stripe** | | |
| `STRIPE_SECRET_KEY` | `sk_test_xxxxx` | From Stripe Dashboard |
| | | |
| **AI Services** | | |
| `OPENAI_API_KEY` | `sk-proj-xxxxx` | From OpenAI |
| `GEMINI_API_KEY` | `AIzaSyxxxxx` | From Google AI Studio |
| | | |
| **URLs (Update after deployment)** | | |
| `FRONTEND_URL` | `https://payflow-frontend.onrender.com` | Add after frontend deployed |
| `API_URL` | `https://payflow-backend.onrender.com/api` | Your backend URL |

#### 2.4 Add Persistent Disk Storage

Scroll to **"Disks"** section:
1. Click **"Add Disk"**
2. **Name**: `payflow-uploads`
3. **Mount Path**: `/opt/render/project/src/backend/uploads`
4. **Size**: `10 GB` (adjust as needed)
5. Click **"Save"**

#### 2.5 Deploy Backend

1. Click **"Create Web Service"**
2. Wait 5-10 minutes for deployment
3. **Copy your backend URL**: `https://payflow-backend.onrender.com`

---

### Phase 3: Update Google OAuth Redirect URLs

**Important:** Update your Google OAuth settings with Render URLs

1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. Navigate to **APIs & Services** → **Credentials**
3. Click on your OAuth 2.0 Client ID
4. Add to **Authorized redirect URIs**:
   ```
   https://payflow-backend.onrender.com/api/auth/google/callback
   ```
5. Click **"Save"**

---

### Phase 4: Deploy Frontend on Render

#### 4.1 Create Static Site

1. **Click "New +"** → **"Static Site"**
2. **Connect same repository**: `BassemHazemDev/PayFlowProd`
3. **Select branch**: `main`

#### 4.2 Configure Frontend Service

**Basic Settings:**
- **Name**: `payflow-frontend`
- **Region**: `Oregon` (same as backend)
- **Branch**: `main`
- **Root Directory**: `frontend`
- **Build Command**: `npm install && npm run build`
- **Publish Directory**: `dist`

#### 4.3 Add Environment Variables

| Key | Value |
|-----|-------|
| `VITE_STRIPE_PUBLISHABLE_KEY` | `pk_test_51S2lre...` |
| `VITE_API_URL` | `https://payflow-backend.onrender.com/api` |

#### 4.4 Deploy Frontend

1. Click **"Create Static Site"**
2. Wait 3-5 minutes for deployment
3. **Copy your frontend URL**: `https://payflow-frontend.onrender.com`

---

### Phase 5: Update Backend with Frontend URL

**Important:** Backend needs to know frontend URL for CORS

1. Go back to **Backend Service** settings
2. Click **"Environment"** tab
3. Update `FRONTEND_URL` variable:
   - Value: `https://payflow-frontend.onrender.com`
4. Click **"Save Changes"**
5. Service will automatically redeploy

---

### Phase 6: Configure Cron Job (Optional)

**Note:** Cron is already configured in code, but you can also set it via dashboard

1. Go to **Backend Service**
2. Navigate to **"Cron Jobs"** tab
3. Click **"Add Cron Job"**
4. Configure:
   - **Name**: `daily-billing-charge`
   - **Schedule**: `0 0 * * *` (daily at midnight UTC)
   - **Command**: 
     ```bash
     node -e "import('./src/modules/pricing_billing/pricing_billing.service.js').then(m => m.chargeAllUsers())"
     ```
5. Click **"Create Cron Job"**

---

## 🧪 Testing Your Deployment

### 1. Test Backend Health
```powershell
# Check if backend is running
curl https://payflow-backend.onrender.com/api/

# Should return: Cannot GET /api/ (404) - This is normal, API is working
```

### 2. Test Frontend
1. Open: `https://payflow-frontend.onrender.com`
2. Should see PayFlow login page
3. Try to sign up/login

### 3. Test File Uploads
1. Login to your account
2. Go to Products → Add Product
3. Upload a product image
4. Verify image displays correctly

### 4. Test AI Features
1. Navigate to AI Assistant
2. Ask a question
3. Verify AI responds correctly

---

## 🔍 Monitoring & Debugging

### View Logs

**Backend Logs:**
1. Go to Backend Service
2. Click **"Logs"** tab
3. Monitor real-time logs

**Frontend Logs:**
1. Go to Frontend Static Site
2. Click **"Logs"** tab (build logs only)

### Common Issues & Solutions

#### ❌ Backend Keeps Crashing
**Cause:** Environment variables missing or database connection failed

**Solution:**
1. Check logs for error messages
2. Verify all environment variables are set
3. Test MongoDB connection string
4. Check if MongoDB Atlas allows Render's IP (0.0.0.0/0)

#### ❌ Frontend Shows Blank Page
**Cause:** VITE_API_URL not set correctly

**Solution:**
1. Check browser console (F12)
2. Verify `VITE_API_URL` in frontend environment
3. Ensure URL ends with `/api` (no trailing slash)

#### ❌ File Uploads Not Working
**Cause:** Disk not mounted or path incorrect

**Solution:**
1. Verify disk is attached in Render dashboard
2. Check mount path: `/opt/render/project/src/backend/uploads`
3. Restart service after adding disk

#### ❌ CORS Errors
**Cause:** FRONTEND_URL not updated in backend

**Solution:**
1. Update backend `FRONTEND_URL` environment variable
2. Include full URL: `https://payflow-frontend.onrender.com`
3. Wait for automatic redeploy

#### ❌ Google OAuth Not Working
**Cause:** Redirect URI not updated

**Solution:**
1. Update Google Cloud Console redirect URIs
2. Add: `https://payflow-backend.onrender.com/api/auth/google/callback`
3. Wait 5 minutes for propagation

---

## 🎨 Custom Domain (Optional)

### Add Custom Domain to Frontend

1. Go to Frontend Static Site
2. Click **"Settings"** → **"Custom Domains"**
3. Click **"Add Custom Domain"**
4. Enter your domain (e.g., `payflow.com`)
5. Add DNS records as instructed by Render
6. SSL certificate is automatic!

### Update Backend FRONTEND_URL
After adding custom domain, update backend environment:
- `FRONTEND_URL`: `https://payflow.com`

---

## 📊 Cost Management

### Current Setup
- **Backend**: $7/month (Starter/Hobby plan)
- **Frontend**: $0/month (Static site is free)
- **Total**: **$7/month**

### Scaling Options

| Plan | RAM | Disk | Price |
|------|-----|------|-------|
| Starter | 512MB | 100GB | $7/month |
| Standard | 2GB | 100GB | $25/month |
| Pro | 4GB | 100GB | $85/month |

**Start with Starter** - Handles ~10,000 requests/month comfortably

---

## 🔐 Security Best Practices

### 1. Rotate Secrets Regularly
- [ ] Change `JWT_SECRET` every 90 days
- [ ] Rotate API keys quarterly
- [ ] Update database password monthly

### 2. Monitor Access Logs
- [ ] Check logs weekly for suspicious activity
- [ ] Set up error alerts in Render

### 3. Keep Dependencies Updated
```powershell
# Check for updates
cd backend
npm outdated

cd ../frontend
npm outdated
```

---

## 🚀 Post-Deployment Tasks

### Immediate
- [ ] Test all features (login, signup, products, invoices)
- [ ] Verify file uploads work
- [ ] Test AI assistant
- [ ] Confirm cron job runs (check logs next day)
- [ ] Test payment flow with Stripe test cards

### Within 24 Hours
- [ ] Monitor error logs
- [ ] Check disk usage
- [ ] Verify email notifications work
- [ ] Test from different devices/browsers

### Within 1 Week
- [ ] Set up custom domain (optional)
- [ ] Configure monitoring alerts
- [ ] Run load testing
- [ ] Create database backup strategy
- [ ] Document any production issues

---

## 📚 Helpful Resources

- **Render Documentation**: https://render.com/docs
- **MongoDB Atlas**: https://cloud.mongodb.com
- **Stripe Testing**: https://stripe.com/docs/testing
- **Redis Cloud**: https://redis.com/try-free

---

## 🆘 Support & Troubleshooting

### Need Help?
1. Check Render logs first
2. Review this deployment guide
3. Check Render status page: https://status.render.com
4. Contact Render support (response within 24h)

### Rollback Strategy
If deployment fails:
```powershell
# Revert to previous commit
git revert HEAD
git push origin main

# Render will auto-deploy previous version
```

---

## ✅ Deployment Complete!

Your PayFlow application should now be live at:
- **Frontend**: https://payflow-frontend.onrender.com
- **Backend**: https://payflow-backend.onrender.com/api

**Next Steps:**
1. Share the frontend URL with your team
2. Test all features thoroughly
3. Set up monitoring alerts
4. Plan for scaling if needed

---

## 📞 Quick Reference

### Environment Variables Template
```bash
# Backend Environment Variables
NODE_ENV=production
PORT=10000
ENABLE_CRON=true
MONGO_URL=mongodb+srv://...
MONGO_DB=payflow_production
REDIS_HOST=...
REDIS_PORT=6379
REDIS_PASSWORD=...
JWT_SECRET=...
JWT_EXPIRES_IN=90d
JWT_COOKIE_EXPIRES_IN=90
GOOGLE_CLIENT_ID=...
GOOGLE_CLIENT_SECRET=...
STRIPE_SECRET_KEY=sk_test_...
OPENAI_API_KEY=sk-proj-...
GEMINI_API_KEY=AIzaSy...
FRONTEND_URL=https://payflow-frontend.onrender.com
API_URL=https://payflow-backend.onrender.com/api

# Frontend Environment Variables
VITE_STRIPE_PUBLISHABLE_KEY=pk_test_...
VITE_API_URL=https://payflow-backend.onrender.com/api
```

---

**🎉 Congratulations on deploying PayFlow on Render! 🎉**
