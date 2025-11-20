# Troubleshooting ESG Data Upload Error

If you're getting "Failed to upload ESG data (network or server error)" from `https://esg-dashboard-pied.vercel.app/dashboard`, follow these steps:

## 1. Verify Backend API URL

The frontend needs to call the correct backend API URL. Check that your frontend is configured with the correct API base URL.

**Example:**
- Backend deployed at: `https://esg-backend.vercel.app` (or your actual backend URL)
- Frontend should call: `https://esg-backend.vercel.app/api/esg-upload`

## 2. Set CORS Environment Variable

In your Vercel backend project dashboard:

1. Go to **Settings** → **Environment Variables**
2. Add or update `FRONTEND_ORIGIN`:
   ```
   FRONTEND_ORIGIN=https://esg-dashboard-pied.vercel.app
   ```
3. If you have multiple origins, separate with commas:
   ```
   FRONTEND_ORIGIN=https://esg-dashboard-pied.vercel.app,http://localhost:3000
   ```
4. **Redeploy** the backend after adding environment variables

## 3. Verify File Upload Request

The frontend should send the file with:
- **Field name**: `file` (required)
- **Content-Type**: `multipart/form-data`
- **Method**: `POST`

**Example fetch request:**
```javascript
const formData = new FormData();
formData.append('file', fileInput.files[0]);

const response = await fetch('https://your-backend-url.vercel.app/api/esg-upload', {
  method: 'POST',
  body: formData,
  // Don't set Content-Type header - browser will set it with boundary
});
```

**Example axios request:**
```javascript
const formData = new FormData();
formData.append('file', file);

const response = await axios.post(
  'https://your-backend-url.vercel.app/api/esg-upload',
  formData,
  {
    headers: {
      'Content-Type': 'multipart/form-data',
    },
  }
);
```

## 4. Check Backend Logs

In Vercel dashboard:
1. Go to your backend project
2. Click **Logs** or **Functions**
3. Check for error messages when upload is attempted
4. Look for CORS errors, file size errors, or parsing errors

## 5. Test the API Endpoint

Test the upload endpoint directly using curl or Postman:

```bash
curl -X POST https://your-backend-url.vercel.app/api/esg-upload \
  -H "Origin: https://esg-dashboard-pied.vercel.app" \
  -F "file=@/path/to/test.xlsx"
```

## 6. Common Issues and Solutions

### Issue: CORS Error
**Error**: `Access to fetch at '...' from origin '...' has been blocked by CORS policy`

**Solution**:
- Add `FRONTEND_ORIGIN` environment variable in Vercel backend project
- Include the exact frontend URL: `https://esg-dashboard-pied.vercel.app`
- Redeploy backend

### Issue: 404 Not Found
**Error**: `Failed to fetch` or `404 Not Found`

**Solution**:
- Verify the backend API URL is correct
- Check that the backend is deployed
- Ensure routes are prefixed with `/api/`

### Issue: File Size Too Large
**Error**: `File size too large` or `413 Payload Too Large`

**Solution**:
- Current limit is 50MB
- For larger files, update `limits.fileSize` in `server.js`
- Note: Vercel has a 4.5MB limit for serverless functions on free tier

### Issue: No File Uploaded
**Error**: `No file uploaded. Please ensure the file field name is 'file'`

**Solution**:
- Ensure FormData field name is exactly `file`
- Verify file is being sent in the request
- Check browser console for request details

## 7. Verify Backend Deployment

1. Test health check endpoint:
   ```
   GET https://your-backend-url.vercel.app/api/health
   ```
   Should return: `{"status":"ok","timestamp":"..."}`

2. Test if backend is reachable:
   ```
   GET https://your-backend-url.vercel.app/api/esg-data
   ```

## 8. Quick Fix Checklist

- [ ] Backend is deployed on Vercel
- [ ] `FRONTEND_ORIGIN` environment variable is set in backend project
- [ ] Backend has been redeployed after adding environment variables
- [ ] Frontend is using the correct backend API URL
- [ ] File upload uses field name `file`
- [ ] Content-Type is `multipart/form-data`
- [ ] File size is under 50MB (and Vercel limits)

## 9. Get More Debugging Info

The backend now logs detailed information. Check Vercel function logs for:
- Request headers
- File information
- Error details
- CORS origin checks

## Still Having Issues?

1. Check browser Network tab:
   - Status code
   - Response body
   - Request headers
   - Response headers

2. Check Vercel function logs:
   - Go to Vercel dashboard → Your project → Functions → View logs
   - Look for errors when upload is attempted

3. Test with a simple file first:
   - Start with a small JSON file
   - Then try a small Excel file
   - This helps isolate the issue

