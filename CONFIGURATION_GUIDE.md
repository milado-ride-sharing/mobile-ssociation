# 🎯 EXACT CONFIGURATION CHECKLIST

This guide tells you **EXACTLY** where to change what. Follow step by step.

---

## PART 1: mobile-ssociation Repository (GitHub Pages)

### ✅ WHAT YOU NEED TO DO:

### File 1: `CNAME`
**Location:** `/mobile-ssociation/CNAME`

**Current:**
```
share.milado.app
```

**Change to:** Your actual subdomain (if you have a domain)
- If you have `milado.app` domain → use `share.milado.app`
- If you have `yourdomain.com` → use `share.yourdomain.com`
- If you DON'T have a domain → DELETE this file and use `milado-ride-sharing.github.io/mobile-ssociation`

---

### File 2: `.well-known/apple-app-site-association`
**Location:** `/mobile-ssociation/.well-known/apple-app-site-association`

**Find this line (line 6):**
```json
"appID": "TEAM_ID.com.milado.rider",
```

**Change `TEAM_ID` to:** Your Apple Developer Team ID
- Find it at: https://developer.apple.com/account/#/membership/
- Example: If your Team ID is `ABC123XYZ`, change to:
```json
"appID": "ABC123XYZ.com.milado.rider",
```

---

### File 3: `.well-known/assetlinks.json`
**Location:** `/mobile-ssociation/.well-known/assetlinks.json`

**Find this line (line 7):**
```json
"sha256_cert_fingerprints": [
  "YOUR_SHA256_FINGERPRINT_HERE"
]
```

**Change to:** Your Android signing key fingerprint

**How to get it:**
```bash
# Run this command:
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android

# Look for a line like:
# SHA256: 12:34:56:78:90:AB:CD:EF:12:34:56:78:90:AB:CD:EF:12:34:56:78:90:AB:CD:EF:12:34:56:78:90:AB:CD:EF

# Copy that value and paste it:
```
```json
"sha256_cert_fingerprints": [
  "12:34:56:78:90:AB:CD:EF:12:34:56:78:90:AB:CD:EF:12:34:56:78:90:AB:CD:EF:12:34:56:78:90:AB:CD:EF"
]
```

---

### File 4: `share.html`
**Location:** `/mobile-ssociation/share.html`

**Find line 368:**
```javascript
const API_BASE_URL = 'https://api.milado.app'; // Update with your API URL
```

**Change to:** Your actual API Gateway URL
- If running locally: `http://localhost:8080`
- If deployed: Your actual domain like `https://api.yourdomain.com`

**Find line 7:**
```html
<meta name="apple-itunes-app" content="app-id=123456789">
```

**Change `123456789` to:** Your actual App Store app ID (get this from Apple Developer Portal after publishing)

**Find line 258:**
```html
<a href="https://apps.apple.com/app/milado-rider/id123456789"
```

**Change to:** Your actual App Store link (after you publish the app)

**Find line 263:**
```html
<a href="https://play.google.com/store/apps/details?id=com.milado.rider"
```

**Change `com.milado.rider` to:** Your actual Android package name (if different)

---

### File 5: Enable GitHub Pages

1. Go to: https://github.com/milado-ride-sharing/mobile-ssociation/settings/pages
2. Under **Source**: Select `main` branch and `/ (root)` folder
3. Click **Save**
4. If you have a domain:
   - Enter your domain in "Custom domain" field
   - Click **Save**
   - Wait for DNS check
   - Enable "Enforce HTTPS"

---

## PART 2: riders-service Repository (Backend)

### File 6: `application.properties`
**Location:** `/riders-service/src/main/resources/application.properties`

**Add these lines** (or update if they exist):
```properties
# Ride Sharing Configuration
app.share.base-url=https://share.milado.app
app.share.deep-link-scheme=miladorider
app.share.expiry-hours=24
```

**Change `https://share.milado.app` to:**
- Your GitHub Pages URL (same as CNAME file)
- OR `https://milado-ride-sharing.github.io/mobile-ssociation` if no custom domain

---

## PART 3: milado-mobile Repository (Mobile App)

### File 7: Deep Link Service
**Location:** `/milado-mobile/milado-rider-mobile/src/services/deepLinkService.ts`

**Find this section (around line 10):**
```typescript
const DEEP_LINK_CONFIG = {
  scheme: 'miladorider',
  domain: 'ride.milado.app',  // ← CHANGE THIS
  paths: {
    sharedRide: '/share/',
    rideDetails: '/ride/',
  },
};
```

**Change `ride.milado.app` to:**
- Your GitHub Pages custom domain: `share.milado.app`
- OR `milado-ride-sharing.github.io` if no custom domain

---

### File 8: Android Manifest
**Location:** `/milado-mobile/milado-rider-mobile/android/app/src/main/AndroidManifest.xml`

**Find the intent-filter section:**
```xml
<intent-filter android:autoVerify="true">
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" android:host="ride.milado.app" />
    <!-- ↑ CHANGE THIS HOST -->
</intent-filter>
```

**Change `android:host="ride.milado.app"` to:**
```xml
<data android:scheme="https" android:host="share.milado.app" />
```

---

### File 9: iOS Info.plist
**Location:** `/milado-mobile/milado-rider-mobile/ios/MiladoMobile/Info.plist`

**Find:**
```xml
<key>com.apple.developer.associated-domains</key>
<array>
    <string>applinks:ride.milado.app</string>
    <!-- ↑ CHANGE THIS DOMAIN -->
</array>
```

**Change to:**
```xml
<key>com.apple.developer.associated-domains</key>
<array>
    <string>applinks:share.milado.app</string>
</array>
```

---

### File 10: iOS Entitlements
**Location:** `/milado-mobile/milado-rider-mobile/ios/MiladoMobile/MiladoMobile.entitlements`

**Find:**
```xml
<key>com.apple.developer.associated-domains</key>
<array>
    <string>applinks:ride.milado.app</string>
    <!-- ↑ CHANGE THIS DOMAIN -->
</array>
```

**Change to:**
```xml
<key>com.apple.developer.associated-domains</key>
<array>
    <string>applinks:share.milado.app</string>
</array>
```

---

## PART 4: DNS Configuration (If Using Custom Domain)

### Your Domain Provider (GoDaddy/Cloudflare/Namecheap)

Add this DNS record:

| Type | Name | Value | TTL |
|------|------|-------|-----|
| CNAME | share | milado-ride-sharing.github.io | 3600 |

This creates: `share.milado.app` → points to GitHub Pages

---

## 📝 QUICK SUMMARY - WHAT DOMAIN TO USE WHERE

**Pick ONE domain and use it everywhere:**

### Option A: Custom Domain (Recommended)
Use: `share.milado.app`

| Location | Value |
|----------|-------|
| mobile-ssociation/CNAME | `share.milado.app` |
| riders-service/application.properties | `app.share.base-url=https://share.milado.app` |
| deepLinkService.ts | `domain: 'share.milado.app'` |
| AndroidManifest.xml | `android:host="share.milado.app"` |
| Info.plist | `applinks:share.milado.app` |
| MiladoMobile.entitlements | `applinks:share.milado.app` |

### Option B: GitHub Pages Default (If No Domain)
Use: `milado-ride-sharing.github.io/mobile-ssociation`

| Location | Value |
|----------|-------|
| mobile-ssociation/CNAME | DELETE this file |
| riders-service/application.properties | `app.share.base-url=https://milado-ride-sharing.github.io/mobile-ssociation` |
| deepLinkService.ts | `domain: 'milado-ride-sharing.github.io'` |
| AndroidManifest.xml | `android:host="milado-ride-sharing.github.io"` |
| Info.plist | `applinks:milado-ride-sharing.github.io` |
| MiladoMobile.entitlements | `applinks:milado-ride-sharing.github.io` |

---

## 🔍 HOW TO KNOW IF YOU HAVE A DOMAIN

**You have a domain if:**
- You paid for something like `milado.app` from GoDaddy/Namecheap/Google Domains
- You can add DNS records
- You own `something.com` or `something.app`

**You DON'T have a domain if:**
- You've never bought one
- You're just using GitHub
- You're testing locally

**For now (testing):** Use Option B (GitHub Pages default URL)

**For production:** Buy a domain and use Option A

---

## ✅ STEP-BY-STEP ORDER

1. **First:** Update `mobile-ssociation` files (Files 1-4)
2. **Second:** Push to GitHub and enable GitHub Pages (File 5)
3. **Third:** Update `riders-service` backend (File 6)
4. **Fourth:** Update `milado-mobile` app (Files 7-10)
5. **Fifth:** If using custom domain, configure DNS
6. **Finally:** Test everything

---

## 🧪 TESTING

After all changes:

```bash
# Test association files are accessible
curl https://share.milado.app/.well-known/apple-app-site-association
curl https://share.milado.app/.well-known/assetlinks.json

# Should return JSON, not 404
```

Need help with a specific step? Tell me which file number and I'll guide you!
