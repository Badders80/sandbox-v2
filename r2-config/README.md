# Evolution Stables V2 - Cloudflare R2 Configuration

## Buckets

### Production: `evolution-v2-horse-images`
- **Purpose**: Public horse images for the V2 marketplace
- **Access**: Public read, private write
- **File Structure**: `{horse-slug}/{image-number}` (e.g., `lady-ketchikan/01`)
- **Naming Convention**: 
  - Files named sequentially starting from `01`
  - Minimum 3 images required for a horse to be "Available"
  - No file extension in the path (determined by Content-Type)

### Preview: `evolution-v2-horse-images-preview` (Optional)
- **Purpose**: Staging images for preview deployments
- **Access**: Same as production
- **File Structure**: Same as production

## Access Configuration

### Public Access
Horse images are publicly accessible via r2.dev URL:
```
https://pub-9c312b6c990143c9be2d837682f667c7.r2.dev/{slug}/{image-number}
```

Enabled with:
```bash
wrangler r2 bucket dev-url enable evolution-v2-horse-images
```

### CORS Policy
Required for website access. **Manual setup required** in Cloudflare Dashboard:

1. Go to: https://dash.cloudflare.com/
2. Navigate to: R2 → `evolution-v2-horse-images` bucket
3. Find the "CORS" section
4. Add this configuration:
```json
[
  {
    "AllowedOrigins": ["https://www.evolutionstables.nz", "https://evolutionstables.nz"],
    "AllowedMethods": ["GET", "HEAD"],
    "MaxAgeSeconds": 86400
  }
]
```

Public access is already enabled at:
```
https://pub-9c312b6c990143c9be2d837682f667c7.r2.dev/{slug}/{image-number}
```

## Fallback Plan

If R2 access is unavailable during development:
1. Use Supabase Storage as a temporary image store
2. Store only the `image_count` in the database (as specified in PLAN.md)
3. Migrate to R2 when token/access is available
4. Update the website app to point to the R2 bucket

## Integration Notes

- The website app should construct URLs as:
  `https://{account-id}.r2.cloudflarestorage.com/evolution-v2-horse-images/{slug}/{image-number}`
- Only `image_count` is stored in the database - no image paths
- A rename moves the entire folder (no redirect table needed)

## Next Steps

1. Create the preview bucket (optional)
2. Configure public access and CORS
3. Update PLAN.md with these configuration details
4. Add the binding configuration to the website app when ready