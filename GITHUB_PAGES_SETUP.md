# GitHub Pages Configuration for Milado Ride Sharing

This guide will help you set up GitHub Pages to host your ride sharing association files.

## 🚀 Quick Setup

### Step 1: Enable GitHub Pages

1. Go to your repository: https://github.com/milado-ride-sharing/mobile-ssociation
2. Click **Settings** → **Pages** (in the left sidebar)
3. Under **Source**, select:
   - Branch: `main`
   - Folder: `/ (root)`
4. Click **Save**

### Step 2: Configure Custom Domain (Recommended)

#### Option A: Using a Subdomain (Recommended)

1. **Update CNAME file** (already created):
   - File contains: `share.milado.app`
   - Change to your preferred subdomain

2. **Configure DNS** with your domain provider:
   - Type: `CNAME`
   - Name: `share` (or whatever subdomain you chose)
   - Value: `milado-ride-sharing.github.io`
   - TTL: 3600 (or default)

3. **Back in GitHub Settings → Pages**:
   - Enter your custom domain: `share.milado.app`
   - Wait for DNS check to pass (can take a few minutes)
   - ✅ Enable "Enforce HTTPS" (required for deep links!)

#### Option B: Using GitHub's Default Domain

If you don't have a custom domain, your site will be at:
```
https://milado-ride-sharing.github.io/mobile-ssociation/
```

**⚠️ Important:** You'll need to update all configurations to use this URL instead.

### Step 3: Update Configuration Files

Once your domain is live, update these values:

#### 1. Backend (riders-service)
```properties
app.share.base-url=https://share.milado.app
```

#### 2. Mobile App (deepLinkService.ts)
```typescript
domain: 'share.milado.app'
```

#### 3. iOS Config (Info.plist & .entitlements)
```xml
<string>applinks:share.milado.app</string>
```

#### 4. Android Config (AndroidManifest.xml)
```xml
<data android:scheme="https" android:host="share.milado.app" />
```

#### 5. Update share.html API URL
Edit `share.html` line 368:
```javascript
const API_BASE_URL = 'https://api.milado.app'; // Your actual API domain
```

## 📁 Repository Structure

Your repository should have:
```
mobile-ssociation/
├── .well-known/
│   ├── apple-app-site-association     (iOS Universal Links)
│   └── assetlinks.json                (Android App Links)
├── share.html                          (Share landing page)
├── index.html                          (Home page)
├── 404.html                            (GitHub Pages routing hack)
├── CNAME                               (Custom domain config)
└── README.md
```

## 🧪 Testing Your Setup

### 1. Verify Files Are Accessible

After deployment (usually takes 1-2 minutes), check:

```bash
# iOS Universal Links
curl -I https://share.milado.app/.well-known/apple-app-site-association

# Android App Links  
curl -I https://share.milado.app/.well-known/assetlinks.json

# Share page
curl -I https://share.milado.app/share.html
```

All should return `200 OK`.

### 2. Test Share Link Format

Your share links will work like:
```
https://share.milado.app/share/abc123token456
```

GitHub Pages will:
1. Receive the request for `/share/abc123token456`
2. Return 404.html (since the path doesn't exist)
3. 404.html redirects to `share.html#/share/abc123token456`
4. share.html extracts the token from the hash

### 3. Validate Deep Links

**iOS Universal Links:**
- https://branch.io/resources/aasa-validator/
- Enter: `https://share.milado.app`

**Android App Links:**
- https://developers.google.com/digital-asset-links/tools/generator
- Enter your domain and package name

## ⚠️ Important Notes

### DNS Propagation
- DNS changes can take 5 minutes to 48 hours to propagate
- Check status: https://www.whatsmydns.net/
- GitHub's DNS check may take 10-15 minutes

### HTTPS Certificate
- GitHub Pages automatically provisions SSL certificates
- This can take up to 24 hours for custom domains
- Deep links ONLY work with HTTPS

### URL Routing
- GitHub Pages doesn't support server-side routing
- We use a 404.html redirect hack for clean URLs
- Tokens are extracted from either path or hash

## 🔧 Troubleshooting

| Issue | Solution |
|-------|----------|
| DNS check failing | Wait 15 minutes, ensure CNAME points to `milado-ride-sharing.github.io` |
| Certificate not issuing | Disable/re-enable custom domain in settings |
| 404 errors | Ensure all files are committed and pushed to main branch |
| Deep links not working | Verify HTTPS is enabled and certificate is issued |
| .well-known files not accessible | Check they're in `.well-known/` folder (with the dot) |

## 📝 Configuration Checklist

- [ ] Enable GitHub Pages in repository settings
- [ ] Configure custom domain DNS (CNAME record)
- [ ] Add custom domain in GitHub Pages settings
- [ ] Wait for DNS check to pass
- [ ] Enable "Enforce HTTPS"
- [ ] Verify certificate is issued (padlock in browser)
- [ ] Test `.well-known/apple-app-site-association` is accessible
- [ ] Test `.well-known/assetlinks.json` is accessible
- [ ] Update backend `app.share.base-url`
- [ ] Update mobile app deep link domain
- [ ] Update iOS Universal Links domain
- [ ] Update Android App Links domain
- [ ] Update API_BASE_URL in share.html
- [ ] Test share link: `https://your-domain.com/share/test`
- [ ] Validate with Branch.io AASA validator
- [ ] Validate with Google Asset Links validator

## 🎯 What Happens After Setup

1. Backend generates share URL: `https://share.milado.app/share/{token}`
2. User shares this link via WhatsApp/SMS
3. Recipient clicks link
4. If app installed: Opens directly in app
5. If no app: Shows web page with "Download App" buttons
6. All association files are validated automatically by iOS/Android

## 💡 Alternative: Using Vercel/Netlify

If you prefer better routing support, consider:
- **Vercel**: Supports `vercel.json` for redirects
- **Netlify**: Supports `_redirects` file
- Both offer automatic HTTPS and better performance

Let me know if you need help with those platforms!
