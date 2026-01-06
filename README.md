# Milado Ride Sharing - Mobile Association Files

This repository contains the association files and web landing page required for deep linking and universal links in the Milado ride sharing mobile app.

## 📁 Files

### 1. `.well-known/apple-app-site-association`
iOS Universal Links configuration for seamless app opening from web links.

**⚠️ REQUIRED CONFIGURATION:**
- Replace `TEAM_ID` with your Apple Developer Team ID
- Find your Team ID at: https://developer.apple.com/account/#/membership/

### 2. `.well-known/assetlinks.json`
Android App Links configuration for deep linking.

**⚠️ REQUIRED CONFIGURATION:**
- Replace `YOUR_SHA256_FINGERPRINT_HERE` with your app's signing key fingerprint

**Get your SHA256 fingerprint:**

For debug builds:
```bash
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

For release builds:
```bash
keytool -list -v -keystore /path/to/your-release-key.keystore -alias your-key-alias
```

### 3. `share.html`
Web fallback page for ride sharing links. Shows ride details and prompts users to open/download the app.

**⚠️ REQUIRED CONFIGURATION:**
- Update `API_BASE_URL` (line 274) with your actual API domain
- Update App Store ID in meta tag (line 7): `app-id=123456789`
- Update App Store link (line 258)
- Update Play Store link (line 263)

## 🚀 Deployment

These files are hosted on GitHub Pages at: `https://milado-ride-sharing.github.io/mobile-ssociation/`

### Required URL Structure:

```
https://your-domain.com/
├── .well-known/
│   ├── apple-app-site-association     (no .json extension!)
│   └── assetlinks.json
└── share/{token}                       (served by share.html)
```

### Deployment Options:

#### Option 1: Static Hosting (Recommended)
- Netlify, Vercel, GitHub Pages, AWS S3 + CloudFront

#### Option 2: Web Server
- Nginx, Apache, or any web server

#### Option 3: CDN
- Cloudflare, Fastly

### Important Notes:

1. **apple-app-site-association** must:
   - Have NO file extension
   - Be served with `Content-Type: application/json`
   - Be accessible at `https://your-domain.com/.well-known/apple-app-site-association`
   - Return 200 status code

2. **assetlinks.json** must:
   - Be served with `Content-Type: application/json`
   - Be accessible at `https://your-domain.com/.well-known/assetlinks.json`

3. **HTTPS is required** - Deep links will not work over HTTP

## 🧪 Testing

### Test iOS Universal Links:
```bash
# Validate your association file
curl -v https://your-domain.com/.well-known/apple-app-site-association

# Test on iOS device
xcrun simctl openurl booted "https://your-domain.com/share/test-token"
```

### Test Android App Links:
```bash
# Validate your association file
curl -v https://your-domain.com/.well-known/assetlinks.json

# Test on Android device
adb shell am start -a android.intent.action.VIEW -d "https://your-domain.com/share/test-token"
```

### Validation Tools:
- iOS: https://branch.io/resources/aasa-validator/
- Android: https://developers.google.com/digital-asset-links/tools/generator

## 📝 Configuration Checklist

- [ ] Update `TEAM_ID` in apple-app-site-association
- [ ] Update `sha256_cert_fingerprints` in assetlinks.json
- [ ] Update `API_BASE_URL` in share.html
- [ ] Update App Store ID in share.html
- [ ] Update App Store link in share.html
- [ ] Update Play Store link in share.html
- [ ] Deploy files to your domain
- [ ] Verify HTTPS is working
- [ ] Test iOS Universal Links
- [ ] Test Android App Links
- [ ] Verify share.html loads correctly

## 🔗 Related Documentation

See the main implementation guide: [RIDE_SHARING_IMPLEMENTATION_GUIDE.md](../RIDE_SHARING_IMPLEMENTATION_GUIDE.md)

## 📞 Support

For issues with:
- iOS Universal Links: Check Apple Developer documentation
- Android App Links: Check Google Digital Asset Links documentation
- Web page: Check browser console for errors

## 🔒 Security Notes

- Share tokens expire after 24 hours
- All association files are public (this is by design)
- Ensure your API validates tokens on the backend
- Use HTTPS everywhere