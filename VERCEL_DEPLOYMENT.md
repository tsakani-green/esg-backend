# Vercel Deployment Guide for ESG Backend

This guide will help you deploy the ESG backend to Vercel.

## Prerequisites

1. A Vercel account (sign up at https://vercel.com)
2. GitHub repository: https://github.com/tsakani-green/esg-backend
3. Vercel CLI (optional, for local testing)

## Deployment Steps

### Option 1: Deploy via Vercel Dashboard (Recommended)

1. **Go to Vercel Dashboard**
   - Visit https://vercel.com/dashboard
   - Sign in with your GitHub account

2. **Import Your Repository**
   - Click "Add New..." → "Project"
   - Import the repository: `tsakani-green/esg-backend`
   - Select the branch: `feature/update-env-config` (or merge to main first)

3. **Configure Project Settings**
   - **Framework Preset**: Other
   - **Root Directory**: `./` (default)
   - **Build Command**: Leave empty (not needed for Node.js API)
   - **Output Directory**: Leave empty
   - **Install Command**: `npm install`

4. **Set Environment Variables**
   Click "Environment Variables" and add the following:
   
   ```
   PORT=3000
   NODE_ENV=production
   ```
   
   **Optional (if using MongoDB):**
   ```
   MONGO_URI=your_mongodb_connection_string
   ```
   
   **Optional (if using OpenAI):**
   ```
   OPENAI_API_KEY=your_openai_api_key
   ```
   
   **Required (for CORS if frontend is on different domain):**
   ```
   FRONTEND_ORIGIN=https://esg-dashboard-pied.vercel.app
   ```
   
   If you have multiple origins, separate with commas:
   ```
   FRONTEND_ORIGIN=https://esg-dashboard-pied.vercel.app,http://localhost:3000
   ```

5. **Deploy**
   - Click "Deploy"
   - Wait for the deployment to complete
   - Your API will be available at: `https://your-project-name.vercel.app`

### Option 2: Deploy via Vercel CLI

1. **Install Vercel CLI**
   ```bash
   npm i -g vercel
   ```

2. **Login to Vercel**
   ```bash
   vercel login
   ```

3. **Deploy**
   ```bash
   vercel
   ```
   
   Follow the prompts:
   - Link to existing project? Yes
   - Project name: esg-backend (or your preferred name)
   - Directory: `./`
   - Override settings? No

4. **Set Environment Variables**
   ```bash
   vercel env add PORT
   vercel env add NODE_ENV
   vercel env add MONGO_URI  # if using MongoDB
   vercel env add OPENAI_API_KEY  # if using OpenAI
   vercel env add FRONTEND_ORIGIN  # if needed for CORS
   ```

5. **Deploy to Production**
   ```bash
   vercel --prod
   ```

## API Endpoints

Once deployed, your API will be available at:
- Base URL: `https://your-project-name.vercel.app`
- ESG Data: `GET /api/esg-data`
- Environmental Insights: `GET /api/environmental-insights`
- Social Insights: `GET /api/social-insights`
- Governance Insights: `GET /api/governance-insights`
- Upload ESG Data: `POST /api/esg-upload`

## Important Notes

1. **Serverless Functions**: Vercel runs your Express app as serverless functions. Each API route will be a separate function.

2. **MongoDB Connection**: 
   - MongoDB connections in serverless environments should use connection pooling
   - Consider using MongoDB Atlas with connection string optimized for serverless
   - The app will continue to work without MongoDB (data won't be persisted)

3. **File Uploads**: 
   - File uploads use `multer.memoryStorage()`, which works well with serverless
   - No persistent file storage is used

4. **Cold Starts**: 
   - First request after inactivity may be slower (cold start)
   - Subsequent requests will be faster

5. **Environment Variables**: 
   - Always set sensitive variables in Vercel dashboard, never commit them
   - Update environment variables for production, preview, and development environments as needed

## Troubleshooting

### Deployment Fails
- Check that all dependencies are in `package.json`
- Verify Node.js version compatibility (Vercel uses Node 18.x by default)
- Check build logs in Vercel dashboard

### API Returns 404
- Verify routes are prefixed with `/api/`
- Check Vercel function logs in the dashboard

### MongoDB Connection Issues
- Ensure `MONGO_URI` is set correctly
- Check MongoDB Atlas IP whitelist (allow Vercel IPs or 0.0.0.0/0)
- Consider using MongoDB connection pooling for serverless

### CORS Issues
- Set `FRONTEND_ORIGIN` environment variable to your frontend URL
- Or update CORS settings in `server.js` if needed

## Next Steps

1. Merge `feature/update-env-config` to `main` branch
2. Set up automatic deployments from `main` branch
3. Configure custom domain (optional)
4. Set up monitoring and logging

## Support

For Vercel-specific issues, check:
- [Vercel Documentation](https://vercel.com/docs)
- [Vercel Community](https://github.com/vercel/vercel/discussions)

