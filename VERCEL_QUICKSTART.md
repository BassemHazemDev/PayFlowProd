# 🚀 PayFlow Vercel Deployment - Quick Start

## ✅ All Configuration Files Ready!

Your PayFlow project has been configured for Vercel deployment with:
- ✅ `vercel.json` - Main deployment configuration
- ✅ Backend serverless export ready
- ✅ Cron job converted to API endpoint
- ✅ Cloudinary integration implemented
- ✅ Frontend production environment configured
- ✅ Dependencies updated in package.json

---

## 🎯 Key Changes Made

### Backend Changes
1. **Serverless Export**: `backend/src/index.js` now exports the Express app for Vercel
2. **Cloudinary Upload**: Smart file storage that switches between local (dev) and Cloudinary (production)
3. **Cron API Endpoint**: `/api/cron/daily-billing` - Protected with Bearer token
4. **Dependencies Added**: `cloudinary` and `multer-storage-cloudinary`

### Frontend Changes
1. **Production Environment**: `.env.production` with relative API URL (`/api`)
2. **Build Script**: `vercel-build` added to package.json

### New Files Created
- `backend/src/utils/middlewares/upload.cloudinary.js` - Cloudinary upload handler
- `backend/src/utils/middlewares/upload.config.js` - Smart upload switcher
- `backend/src/utils/helpers/fileUpload.helper.js` - File URL helper functions
- `backend/src/modules/cron/cron.routes.js` - Cron API routes
- `backend/src/modules/cron/cron.controller.js` - Protected cron endpoint

---

## 🚀 Deployment Steps (30 minutes)

### Step 1: Install Cloudinary Packages (2 min)
```powershell
cd backend
npm install
cd ..
```

### Step 2: Set Up Cloudinary Account (5 min)
1. Go to https://cloudinary.com/users/register/free
2. Sign up (free - 25GB storage)
3. Get credentials from Dashboard:
   - Cloud Name
   - API Key
   - API Secret

### Step 3: Configure MongoDB Atlas (3 min)
1. Login to MongoDB Atlas
2. Network Access → Add IP → Allow 0.0.0.0/0
3. Copy your connection string

### Step 4: Push to GitHub (1 min)
```powershell
git add .
git commit -m "Configure for Vercel deployment with Cloudinary"
git push origin main
```

### Step 5: Deploy on Vercel (15 min)
1. Go to https://vercel.com
2. New Project → Import from GitHub
3. Select `PayFlowProd` repository
4. Add Environment Variables (see below)
5. Deploy!

### Step 6: Set Up Cron Job (3 min)
1. Go to https://cron-job.org
2. Create account
3. Create job:
   - URL: `https://your-app.vercel.app/api/cron/daily-billing`
   - Method: POST
   - Schedule: Daily at 00:00
   - Header: `Authorization: Bearer YOUR_CRON_SECRET`

### Step 7: Update Google OAuth (1 min)
1. Google Cloud Console
2. Add redirect URI: `https://your-app.vercel.app/api/auth/google/callback`

---

## 📋 Environment Variables Checklist

### Must Have (Backend)
```bash
# Database
MONGO_URL=mongodb+srv://...
MONGO_DB=payflow_production

# JWT
JWT_SECRET=your_secret_32_chars_minimum
JWT_EXPIRES_IN=90d
JWT_COOKIE_EXPIRES_IN=90

# Cloudinary (IMPORTANT!)
CLOUDINARY_CLOUD_NAME=dxxxxxxxxx
CLOUDINARY_API_KEY=123456789012345
CLOUDINARY_API_SECRET=abcdefgh...
USE_CLOUDINARY=true

# Cron (IMPORTANT!)
CRON_SECRET=random_token_for_cron

# Stripe
STRIPE_SECRET_KEY=sk_test_...

# OAuth
GOOGLE_CLIENT_ID=xxxxx.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=GOCSPX-xxxxx

# URLs (Update after first deploy)
FRONTEND_URL=https://your-app.vercel.app
API_URL=https://your-app.vercel.app/api
NODE_ENV=production
```

### Optional (Backend)
```bash
# Redis
REDIS_HOST=redis-xxxxx...
REDIS_PORT=6379
REDIS_PASSWORD=...

# AI
OPENAI_API_KEY=sk-proj-...
GEMINI_API_KEY=AIzaSy...
```

### Frontend
```bash
VITE_STRIPE_PUBLISHABLE_KEY=pk_test_...
VITE_API_URL=/api
```

---

## 🧪 Testing After Deployment

### 1. Test Homepage
Visit: `https://your-app.vercel.app`

### 2. Test File Upload
1. Login
2. Add Product with image
3. Check image loads from Cloudinary (right-click → Copy image address)
4. Should see: `res.cloudinary.com/...`

### 3. Test Cron Endpoint
```powershell
$headers = @{
    "Authorization" = "Bearer YOUR_CRON_SECRET"
}
Invoke-RestMethod -Uri "https://your-app.vercel.app/api/cron/daily-billing" -Method POST -Headers $headers
```

Should return success message.

---

## ⚠️ Common Issues

### Issue: Build Fails
**Solution**: Run `npm install` in backend folder first

### Issue: File Upload Returns 500
**Solution**: Check Cloudinary credentials in environment variables

### Issue: CORS Errors
**Solution**: Update `FRONTEND_URL` environment variable

### Issue: Cron Returns 401
**Solution**: Check Authorization header format: `Bearer YOUR_TOKEN` (with space!)

---

## 💰 Cost: $0/month

All services on free tier:
- Vercel: Free (100GB bandwidth)
- Cloudinary: Free (25GB storage)
- MongoDB: Free (512MB)
- Cron-job.org: Free

---

## 📚 Full Documentation

For detailed step-by-step guide, see:
**`VERCEL_DEPLOYMENT_GUIDE.md`**

---

## 🆘 Need Help?

1. Check Vercel logs (Dashboard → Function Logs)
2. Review full deployment guide
3. Check Cloudinary dashboard for uploads
4. Test cron endpoint manually

---

**Ready to deploy? Let's go! 🚀**

Next command:
```powershell
cd backend
npm install
cd ..
git add .
git commit -m "Configure for Vercel deployment"
git push origin main
```

Then go to Vercel and click Import! 🎉
