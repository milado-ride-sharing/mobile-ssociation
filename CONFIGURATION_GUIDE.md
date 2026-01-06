# 🎯 SIMPLE CONFIGURATION GUIDE

## 📋 YOU ONLY NEED TO UPDATE 2 FILES

Everything else is handled automatically by scripts!

---

## FILE 1: Mobile App `.env` File

**Location:** `milado-mobile/milado-rider-mobile/.env`

**Step 1:** Copy the example file:
```bash
cd milado-mobile/milado-rider-mobile
cp .env.example .env
```

**Step 2:** Edit `.env` with your values:

```dotenv
# ===========================================
# REQUIRED - Update these values
# ===========================================

# Your API Gateway URL
API_BASE_URL=http://localhost:8080

# Your WebSocket URL  
WEBSOCKET_BASE_URL=ws://localhost:8080

# Deep Link Domain (GitHub Pages - no changes needed for now)
DEEP_LINK_DOMAIN=milado-ride-sharing.github.io
DEEP_LINK_BASE_PATH=/mobile-ssociation

# Google Maps API Key
GOOGLE_MAPS_API_KEY=your_actual_google_maps_key

# ===========================================
# OPTIONAL - Update after app is published
# ===========================================

# App Store ID (get after publishing to Apple App Store)
APP_STORE_ID=123456789

# Play Store URL (get after publishing to Google Play)
PLAY_STORE_URL=https://play.google.com/store/apps/details?id=com.milado.rider
```

**That's it for mobile!** The build scripts will automatically:
- Replace `__DEEP_LINK_DOMAIN__` in Android and iOS configs
- Replace `__DEEP_LINK_BASE_PATH__` in Android config
- Replace `__GOOGLE_MAPS_API_KEY__` in iOS config

---

## FILE 2: mobile-ssociation Files

**Location:** `mobile-ssociation/` repository

### 2a. Update `share.html` (lines 357-369)

Find the configuration section at the top of the `<script>` tag:

```javascript
// CONFIGURATION - UPDATE ALL THESE VALUES
const API_BASE_URL = 'http://localhost:8080';
const DEEP_LINK_SCHEME = 'miladorider';
const APP_STORE_ID = '123456789';
const PLAY_STORE_PACKAGE = 'com.milado.rider';
```

**Update these values:**
- `API_BASE_URL` → Your API Gateway URL (e.g., `https://api.yourdomain.com`)
- `APP_STORE_ID` → Your Apple App Store ID (get after publishing)
- `PLAY_STORE_PACKAGE` → Your Android package name (usually `com.yourcompany.appname`)

### 2b. Update `.well-known/apple-app-site-association` (line 6)

Find:
```json
"appID": "TEAM_ID.com.milado.rider",
```

Replace `TEAM_ID` with your Apple Developer Team ID:
```json
"appID": "ABC123XYZ.com.milado.rider",
```

**How to find Team ID:** https://developer.apple.com/account/#/membership/

### 2c. Update `.well-known/assetlinks.json` (line 8)

Find:
```json
"sha256_cert_fingerprints": ["YOUR_SHA256_FINGERPRINT_HERE"]
```

Replace with your Android fingerprint:
```json
"sha256_cert_fingerprints": ["12:34:56:78:90:AB:CD:EF:..."]
```

**How to get fingerprint:**
```bash
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android | grep SHA256
```

---

## 🚀 DEPLOYMENT STEPS

### Step 1: Deploy mobile-ssociation to GitHub Pages

```bash
cd mobile-ssociation
git add .
git commit -m "Update configuration"
git push
```

Then go to: https://github.com/milado-ride-sharing/mobile-ssociation/settings/pages
- Source: `main` branch, `/ (root)` folder
- Click **Save**

### Step 2: Build Mobile App

```bash
cd milado-mobile/milado-rider-mobile

# Create .env file
cp .env.example .env
# Edit .env with your values

# Build Android
npm run android

# Build iOS (Mac only)
npm run ios
```

---

## 📊 SUMMARY TABLE

| What | Where | Value to Set |
|------|-------|--------------|
| API URL | `.env` → `API_BASE_URL` | `http://localhost:8080` or your production URL |
| WebSocket URL | `.env` → `WEBSOCKET_BASE_URL` | `ws://localhost:8080` or your production URL |
| Deep Link Domain | `.env` → `DEEP_LINK_DOMAIN` | `milado-ride-sharing.github.io` |
| Deep Link Path | `.env` → `DEEP_LINK_BASE_PATH` | `/mobile-ssociation` |
| Google Maps Key | `.env` → `GOOGLE_MAPS_API_KEY` | Your Google Maps API key |
| App Store ID | `.env` → `APP_STORE_ID` | Your Apple App Store ID |
| Play Store URL | `.env` → `PLAY_STORE_URL` | Your Play Store URL |
| Web API URL | `share.html` → `API_BASE_URL` | Your API Gateway URL |
| Web App Store ID | `share.html` → `APP_STORE_ID` | Your Apple App Store ID (same as mobile) |
| Web Play Store Package | `share.html` → `PLAY_STORE_PACKAGE` | Your Android package name |
| Apple Team ID | `apple-app-site-association` → line 6 | Your Apple Team ID |
| Android Fingerprint | `assetlinks.json` → line 8 | Your SHA256 fingerprint |

---

## ❓ FAQ

**Q: Do I need to edit AndroidManifest.xml or iOS entitlements?**
A: NO! The setup scripts automatically replace placeholders when you run `npm run android` or `npm run ios`.

**Q: Where do I put my API URL for production?**
A: Two places (they should match):
1. Mobile app: `.env` file → `API_BASE_URL`
2. Web page: `share.html` → `API_BASE_URL` variable

**Q: Where do I put App Store IDs?**
A: Two places (they should match):
1. Mobile app: `.env` file → `APP_STORE_ID` and `PLAY_STORE_URL`
2. Web page: `share.html` → `APP_STORE_ID` and `PLAY_STORE_PACKAGE` variables

**Q: What if I don't have Apple/Android developer accounts yet?**
A: You can leave `TEAM_ID` and `SHA256_FINGERPRINT` as placeholders for now. Update them when you're ready to publish.

---

## 🔧 HOW THE SCRIPTS WORK

When you run `npm run android`:
1. Script reads `DEEP_LINK_DOMAIN` from `.env`
2. Replaces `__DEEP_LINK_DOMAIN__` in `AndroidManifest.xml`
3. Replaces `__DEEP_LINK_BASE_PATH__` in `AndroidManifest.xml`
4. Builds the app

When you run `npm run ios`:
1. Script reads `DEEP_LINK_DOMAIN` from `.env`
2. Replaces `__DEEP_LINK_DOMAIN__` in `MiladoMobile.entitlements`
3. Replaces `__GOOGLE_MAPS_API_KEY__` in `Info.plist`
4. Builds the app

You never need to manually edit Android/iOS config files!
