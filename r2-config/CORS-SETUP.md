# Cloudflare R2 CORS Manual Setup

## Steps to Configure CORS

1. **Log in to Cloudflare Dashboard**
   - Visit: https://dash.cloudflare.com/

2. **Navigate to R2 Buckets**
   - From the left sidebar: R2 → Buckets
   - Select: `evolution-v2-horse-images`

3. **Find CORS Settings**
   - In the bucket details page, look for the "CORS" section
   - Click "Edit" or "Configure"

4. **Add CORS Configuration**
   ```json
   [
     {
       "AllowedOrigins": [
         "https://www.evolutionstables.nz",
         "https://evolutionstables.nz"
       ],
       "AllowedMethods": ["GET", "HEAD"],
       "MaxAgeSeconds": 86400
     }
   ]
   ```

5. **Save Changes**
   - Click "Save" or "Apply"

## Verification

After setup, verify CORS is working by:

1. Uploading a test image to the bucket
2. Accessing it from the website domain
3. Checking the response headers include:
   ```
   Access-Control-Allow-Origin: https://www.evolutionstables.nz
   ```

## Public Access URL

Images are accessible at:
```
https://pub-9c312b6c990143c9be2d837682f667c7.r2.dev/{slug}/{image-number}
```

Example:
```
https://pub-9c312b6c990143c9be2d837682f667c7.r2.dev/lady-ketchikan/01
```