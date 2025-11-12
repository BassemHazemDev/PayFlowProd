# 🚀 PayFlow Vercel Deployment Guide

## 📋 Overview

This guide will help you deploy the PayFlow full-stack application on Vercel's free tier using:
- **Frontend**: React + Vite (Static Site)
- **Backend**: Node.js + Express (Serverless Functions)
- **File Storage**: Cloudinary (Free tier)
- **Database**: MongoDB Atlas (Free tier)
- **Cron Jobs**: External service (cron-job.org)

**Total Cost: $0/month** 🎉

---

## ✅ Pre-Deployment Checklist

### Required Accounts:
- [ ] GitHub account (repository ready)
- [ ] Vercel account (https://vercel.com)
- [ ] MongoDB Atlas account (database configured)
- [ ] Cloudinary account (for file uploads)
- [ ] Redis Cloud account (optional - for caching)
- [ ] Stripe account (API keys ready)
- [ ] cron-job.org account (for daily billing)

### Code Changes Completed:
- [x] Root `vercel.json` configured
- [x] Backend updated for serverless export
- [x] Cron job converted to API endpoint (`/api/cron/daily-billing`)
- [x] Cloudinary integration added
- [x] Frontend environment configured
- [x] Package dependencies updated

---

## 📝 Step 1: Set Up Cloudinary

### 1.1 Create Cloudinary Account
1. Go to https://cloudinary.com/users/register/free
2. Sign up for a **free account** (25GB storage, 25GB bandwidth/month)
3. Verify your email

### 1.2 Get Cloudinary Credentials
1. Login to Cloudinary dashboard
2. Go to **Dashboard** → **Account Details**
3. Copy these values:
   - **Cloud Name**: `dxxxxxxxxx`
   - **API Key**: `123456789012345`
   - **API Secret**: `abcdefghijklmnopqrstuvwxyz`

**Save these values** - you'll need them for environment variables!

---

## 📝 Step 2: Prepare MongoDB Atlas

### 2.1 Configure Network Access
1. Login to MongoDB Atlas
2. Go to **Network Access** (Security menu)
3. Click **Add IP Address**
4. Select **Allow Access from Anywhere** (0.0.0.0/0)
5. Click **Confirm**

> **Why?** Vercel serverless functions use dynamic IPs, so you need to allow all IPs.

### 2.2 Get Connection String
1. Go to **Database** → **Connect**
2. Choose **Connect your application**
3. Copy the connection string
4. Replace `<password>` with your actual password

Example:
```
mongodb+srv://username:PASSWORD@cluster.mongodb.net/?retryWrites=true&w=majority
```

---

## 📝 Step 3: Install Dependencies

```powershell
# Install Cloudinary packages in backend
cd backend
npm install cloudinary multer-storage-cloudinary

# Verify installation
cd ..
```

---

## 📝 Step 4: Deploy to Vercel

### 4.1 Push to GitHub

```powershell
# Add all changes
git add .

# Commit
git commit -m "Configure for Vercel deployment with Cloudinary"

# Push to GitHub
git push origin main
```

### 4.2 Import Project to Vercel

1. **Go to** https://vercel.com/dashboard
2. **Click** "Add New..." → "Project"
3. **Import** your GitHub repository: `BassemHazemDev/PayFlowProd`
4. **Click** "Import"

### 4.3 Configure Project Settings

**Framework Preset**: Other (monorepo)

**Root Directory**: Leave empty (monorepo root)

**Build & Development Settings**:
- Leave default - Vercel will detect the configuration from `vercel.json`

---

## 📝 Step 5: Add Environment Variables

### 5.1 Backend Environment Variables

Click **"Environment Variables"** tab and add the following:

#### Database
```bash
MONGO_URL=mongodb+srv://username:password@cluster.mongodb.net/?retryWrites=true&w=majority
MONGO_DB=payflow_production
```

#### Redis (Optional)
```bash
REDIS_HOST=redis-xxxxx.c1.us-east-1-2.ec2.cloud.redislabs.com
REDIS_PORT=6379
REDIS_PASSWORD=your_redis_password
```

#### JWT Configuration
```bash
JWT_SECRET=your_super_secret_jwt_key_minimum_32_characters_long
JWT_EXPIRES_IN=90d
JWT_COOKIE_EXPIRES_IN=90
```

#### Google OAuth
```bash
GOOGLE_CLIENT_ID=xxxxx.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=GOCSPX-xxxxx
```

#### Stripe
```bash
STRIPE_SECRET_KEY=sk_test_51S2lreCuYjWP9nyZ...
```

#### AI Services
```bash
OPENAI_API_KEY=sk-proj-xxxxx
GEMINI_API_KEY=AIzaSyxxxxx
```

#### Cloudinary (NEW - Important!)
```bash
CLOUDINARY_CLOUD_NAME=dxxxxxxxxx
CLOUDINARY_API_KEY=123456789012345
CLOUDINARY_API_SECRET=abcdefghijklmnopqrstuvwxyz
USE_CLOUDINARY=true
```

#### Cron Secret (NEW - Important!)
```bash
CRON_SECRET=your_random_secret_token_for_cron_endpoint
```
> Generate a random token: https://randomkeygen.com/

#### URLs
```bash
FRONTEND_URL=https://your-app.vercel.app
API_URL=https://your-app.vercel.app/api
NODE_ENV=production
```

### 5.2 Frontend Environment Variables

```bash
VITE_STRIPE_PUBLISHABLE_KEY=pk_test_51S2lreCuYjWP9nyZIv15AjO9bEQa9Tpws0BPdukXeOYmq5krGdfp78cPH0Ke90t1qSbmXJvp6PJW3cE0CBskcx1a00vt3Tt6zl
VITE_API_URL=/api
```

> **Note**: We use `/api` (relative path) because frontend and backend are on the same domain

---

## 📝 Step 6: Deploy!

1. **Click** "Deploy"
2. **Wait** 3-5 minutes for deployment
3. **Copy** your deployment URL: `https://your-app.vercel.app`

### 6.1 Update URLs

After first deployment, update these environment variables:

1. Go to **Settings** → **Environment Variables**
2. Update:
   - `FRONTEND_URL` = `https://your-app.vercel.app`
   - `API_URL` = `https://your-app.vercel.app/api`
3. **Redeploy** from **Deployments** tab

---

## 📝 Step 7: Configure Google OAuth

### 7.1 Update Redirect URIs

1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. Navigate to **APIs & Services** → **Credentials**
3. Click on your OAuth 2.0 Client ID
4. Add to **Authorized redirect URIs**:
   ```
   https://your-app.vercel.app/api/auth/google/callback
   ```
5. Add to **Authorized JavaScript origins**:
   ```
   https://your-app.vercel.app
   ```
6. Click **"Save"**

---

## 📝 Step 8: Set Up Daily Billing Cron Job

Since Vercel free tier doesn't support cron jobs, we'll use an external service.

### 8.1 Create Account on cron-job.org

1. Go to https://cron-job.org/en/
2. Sign up for **free account**
3. Verify your email

### 8.2 Create Cron Job

1. Click **"Create cronjob"**
2. **Title**: `PayFlow Daily Billing Charge`
3. **Address (URL)**: `https://your-app.vercel.app/api/cron/daily-billing`
4. **Schedule**: 
   - Every day
   - At: `00:00` (midnight)
5. **Request method**: `POST`
6. **Headers** → Click "Add" → Add:
   - **Name**: `Authorization`
   - **Value**: `Bearer YOUR_CRON_SECRET`
   
   > Replace `YOUR_CRON_SECRET` with the token you set in environment variables
   
7. **Click** "Create cronjob"

### 8.3 Test the Cron Job

1. Click **"Execute now"** button
2. Check **Status** - should be `200 OK`
3. Check **Response** - should see success message
4. Check your MongoDB - billing records should be created

---

## 🧪 Step 9: Testing Your Deployment

### 9.1 Test Frontend
1. Open: `https://your-app.vercel.app`
2. Should see PayFlow login page
3. Try to sign up/login

### 9.2 Test Backend API
```powershell
# Test API health
curl https://your-app.vercel.app/api/auth/

# Should return 404 (normal - route doesn't exist)
```

### 9.3 Test File Uploads
1. Login to your account
2. Go to **Products** → **Add Product**
3. Upload a product image
4. **Check**: Image should display from Cloudinary URL (not localhost)
5. **Verify** in Cloudinary dashboard → Media Library

### 9.4 Test AI Features
1. Navigate to **AI Assistant**
2. Ask a question
3. Verify AI responds correctly

### 9.5 Test Stripe Payments
Use Stripe test cards:
- **Success**: `4242 4242 4242 4242`
- **Decline**: `4000 0000 0000 0002`
- **CVV**: Any 3 digits
- **Expiry**: Any future date

---

## 🔍 Troubleshooting

### ❌ Build Fails with "Cannot find module"

**Cause**: Missing dependencies

**Solution**:
```powershell
cd backend
npm install cloudinary multer-storage-cloudinary
git add package.json package-lock.json
git commit -m "Add Cloudinary dependencies"
git push origin main
```

### ❌ File Uploads Return 500 Error

**Cause**: Cloudinary credentials not set or incorrect

**Solution**:
1. Go to Vercel → Settings → Environment Variables
2. Verify:
   - `CLOUDINARY_CLOUD_NAME`
   - `CLOUDINARY_API_KEY`
   - `CLOUDINARY_API_SECRET`
   - `USE_CLOUDINARY=true`
3. Redeploy from Deployments tab

### ❌ CORS Errors in Browser Console

**Cause**: `FRONTEND_URL` not set correctly

**Solution**:
1. Update backend environment variable
2. `FRONTEND_URL=https://your-app.vercel.app`
3. Make sure no trailing slash
4. Redeploy

### ❌ Google OAuth Returns 400 Error

**Cause**: Redirect URI not configured

**Solution**:
1. Update Google Cloud Console redirect URIs (Step 7)
2. Wait 5 minutes for propagation
3. Clear browser cache
4. Try again

### ❌ Images Not Loading

**Cause**: Files uploaded before Cloudinary setup

**Solution**:
- Delete old products/companies
- Re-upload images after deployment
- New uploads will use Cloudinary

### ❌ Cron Job Fails with 401 Unauthorized

**Cause**: `Authorization` header missing or incorrect

**Solution**:
1. Check cron-job.org headers
2. Verify format: `Bearer YOUR_TOKEN` (with space)
3. Make sure token matches `CRON_SECRET` environment variable

### ❌ Function Timeout Errors

**Cause**: Vercel free tier has 10s timeout limit

**Solution**:
- Optimize database queries
- Add indexes to MongoDB
- Consider upgrading to Pro plan for 60s timeout

---

## 📊 Monitoring & Logs

### View Logs

1. Go to Vercel Dashboard
2. Click on your project
3. Navigate to **Deployments**
4. Click on latest deployment
5. View **Function Logs** or **Build Logs**

### Check Cloudinary Usage

1. Login to Cloudinary
2. Go to **Dashboard**
3. Monitor:
   - Storage used
   - Bandwidth used
   - Transformations

---

## 💰 Cost Summary

| Service | Free Tier Limit | Cost |
|---------|----------------|------|
| **Vercel** | 100GB bandwidth/month | $0 |
| **Cloudinary** | 25GB storage, 25GB bandwidth | $0 |
| **MongoDB Atlas** | 512MB storage | $0 |
| **Redis Cloud** | 30MB storage | $0 |
| **cron-job.org** | Unlimited jobs | $0 |
| **Total** | | **$0/month** 🎉 |

---

## 🚀 Post-Deployment Tasks

### Immediate (First Hour)
- [ ] Test all features (login, signup, products, invoices)
- [ ] Verify file uploads work with Cloudinary
- [ ] Test AI assistant
- [ ] Confirm cron job runs (check logs next day)
- [ ] Test payment flow with Stripe test cards

### Within 24 Hours
- [ ] Monitor Vercel function logs for errors
- [ ] Check Cloudinary usage dashboard
- [ ] Verify email notifications work
- [ ] Test from different devices/browsers
- [ ] Set up Vercel monitoring alerts

### Within 1 Week
- [ ] Add custom domain (optional)
- [ ] Configure production Stripe keys
- [ ] Run load testing
- [ ] Create database backup strategy
- [ ] Document any production issues

---

## 🔐 Security Best Practices

### 1. Rotate Secrets Regularly
- [ ] Change `JWT_SECRET` every 90 days
- [ ] Rotate API keys quarterly
- [ ] Update `CRON_SECRET` monthly

### 2. Monitor Access Logs
- [ ] Check Vercel logs weekly for suspicious activity
- [ ] Set up error alerts
- [ ] Monitor Cloudinary access patterns

### 3. Keep Dependencies Updated
```powershell
# Check for updates
cd backend
npm outdated

cd ../frontend
npm outdated
```

---

## 🔄 Making Changes After Deployment

### Update Code
```powershell
# Make your changes
git add .
git commit -m "Your change description"
git push origin main

# Vercel auto-deploys on push!
```

### Update Environment Variables
1. Go to Vercel Dashboard
2. Settings → Environment Variables
3. Update values
4. Redeploy from Deployments tab

---

## 📞 Quick Reference

### Important URLs
- **Application**: https://your-app.vercel.app
- **API Base**: https://your-app.vercel.app/api
- **Cron Endpoint**: https://your-app.vercel.app/api/cron/daily-billing

### Cron Job Header
```
Authorization: Bearer YOUR_CRON_SECRET
```

### Test Cron Endpoint (PowerShell)
```powershell
$headers = @{
    "Authorization" = "Bearer YOUR_CRON_SECRET"
}
Invoke-RestMethod -Uri "https://your-app.vercel.app/api/cron/daily-billing" -Method POST -Headers $headers
```

---

## 🆘 Getting Help

1. **Check Logs**: Vercel Dashboard → Function Logs
2. **Vercel Status**: https://www.vercel-status.com/
3. **Vercel Docs**: https://vercel.com/docs
4. **Cloudinary Docs**: https://cloudinary.com/documentation
5. **Community**: Vercel Discord, Stack Overflow

---

## 📚 Additional Resources

- **Vercel Serverless Functions**: https://vercel.com/docs/functions
- **Cloudinary Node.js SDK**: https://cloudinary.com/documentation/node_integration
- **MongoDB Atlas Docs**: https://docs.atlas.mongodb.com/
- **Stripe Testing**: https://stripe.com/docs/testing
- **cron-job.org Docs**: https://cron-job.org/en/documentation/

---

## ✅ Deployment Complete!

Your PayFlow application should now be live at:
**https://your-app.vercel.app**

**Next Steps:**
1. Share the URL with your team
2. Test all features thoroughly
3. Set up monitoring alerts
4. Plan for scaling if needed
5. Consider custom domain

---

**🎉 Congratulations on deploying PayFlow on Vercel! 🎉**

---

## 📝 Environment Variables Quick Copy

### Backend (.env)
```bash
# Database
MONGO_URL=mongodb+srv://username:password@cluster.mongodb.net/?retryWrites=true&w=majority
MONGO_DB=payflow_production

# Redis
REDIS_HOST=redis-xxxxx.c1.us-east-1-2.ec2.cloud.redislabs.com
REDIS_PORT=6379
REDIS_PASSWORD=your_redis_password

# JWT
JWT_SECRET=your_super_secret_jwt_key_minimum_32_characters_long
JWT_EXPIRES_IN=90d
JWT_COOKIE_EXPIRES_IN=90

# OAuth
GOOGLE_CLIENT_ID=xxxxx.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=GOCSPX-xxxxx

# Stripe
STRIPE_SECRET_KEY=sk_test_xxxxx

# AI
OPENAI_API_KEY=sk-proj-xxxxx
GEMINI_API_KEY=AIzaSyxxxxx

# Cloudinary
CLOUDINARY_CLOUD_NAME=dxxxxxxxxx
CLOUDINARY_API_KEY=123456789012345
CLOUDINARY_API_SECRET=abcdefghijklmnopqrstuvwxyz
USE_CLOUDINARY=true

# Cron
CRON_SECRET=your_random_secret_token

# URLs
FRONTEND_URL=https://your-app.vercel.app
API_URL=https://your-app.vercel.app/api
NODE_ENV=production
```

### Frontend (.env)
```bash
VITE_STRIPE_PUBLISHABLE_KEY=pk_test_xxxxx
VITE_API_URL=/api
```
