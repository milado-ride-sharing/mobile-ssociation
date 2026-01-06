# 📝 CONFIGURATION VALUES - WHERE TO SET WHAT

## ✅ ALL DYNAMIC VALUES ARE NOW FROM CONFIGURATION

Nothing is hardcoded anymore. All URLs, IDs, and domains come from config files.

---

## 1️⃣ MOBILE APP (.env file)

**File:** `milado-mobile/milado-rider-mobile/.env`

```dotenv
# Backend URLs
API_BASE_URL=http://localhost:8080
WEBSOCKET_BASE_URL=ws://localhost:8080

# Deep Link Configuration (GitHub Pages)
DEEP_LINK_DOMAIN=milado-ride-sharing.github.io
DEEP_LINK_BASE_PATH=/mobile-ssociation

# App Store Links
APP_STORE_ID=123456789
PLAY_STORE_URL=https://play.google.com/store/apps/details?id=com.milado.rider

# Google Maps
GOOGLE_MAPS_API_KEY=your_key_here
```

**What gets auto-replaced when you run `npm run android` or `npm run ios`:**
- `__DEEP_LINK_DOMAIN__` in AndroidManifest.xml → `DEEP_LINK_DOMAIN` value
- `__DEEP_LINK_BASE_PATH__` in AndroidManifest.xml → `DEEP_LINK_BASE_PATH` value
- `__DEEP_LINK_DOMAIN__` in MiladoMobile.entitlements → `DEEP_LINK_DOMAIN` value
- `__GOOGLE_MAPS_API_KEY__` in Info.plist → `GOOGLE_MAPS_API_KEY` value

---

## 2️⃣ WEB PAGE (share.html)

**File:** `mobile-ssociation/share.html`

**Lines 357-369 (Configuration Section):**

```javascript
// Your API Gateway URL
const API_BASE_URL = 'http://localhost:8080';

// Deep link scheme
const DEEP_LINK_SCHEME = 'miladorider';

// App Store Configuration
const APP_STORE_ID = '123456789';
const APP_STORE_URL = `https://apps.apple.com/app/milado-rider/id${APP_STORE_ID}`;

// Play Store Configuration
const PLAY_STORE_PACKAGE = 'com.milado.rider';
const PLAY_STORE_URL = `https://play.google.com/store/apps/details?id=${PLAY_STORE_PACKAGE}`;
```

**What you need to update:**
- `API_BASE_URL` → Your production API URL
- `APP_STORE_ID` → Your Apple App Store ID
- `PLAY_STORE_PACKAGE` → Your Android package name

---

## 3️⃣ ASSOCIATION FILES (mobile-ssociation)

### File: `.well-known/apple-app-site-association` (line 6)

```json
"appID": "TEAM_ID.com.milado.rider"
```

**Change `TEAM_ID` to:** Your Apple Developer Team ID
- Get it from: https://developer.apple.com/account/#/membership/

### File: `.well-known/assetlinks.json` (line 8)

```json
"sha256_cert_fingerprints": ["YOUR_SHA256_FINGERPRINT_HERE"]
```

**Change to:** Your Android signing certificate fingerprint

**Get it by running:**
```bash
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android | grep SHA256
```

---

## 🎯 QUICK CHECKLIST

### For Development (Local Testing):
- [ ] Created `.env` in mobile app from `.env.example`
- [ ] Set `API_BASE_URL=http://localhost:8080` in `.env`
- [ ] Set `GOOGLE_MAPS_API_KEY` in `.env`
- [ ] Updated `share.html` → `API_BASE_URL` to `http://localhost:8080`

### For Production:
- [ ] Updated `.env` → `API_BASE_URL` to production URL
- [ ] Updated `.env` → `APP_STORE_ID` (after publishing to App Store)
- [ ] Updated `.env` → `PLAY_STORE_URL` (after publishing to Play Store)
- [ ] Updated `share.html` → `API_BASE_URL` to production URL
- [ ] Updated `share.html` → `APP_STORE_ID` to match mobile app
- [ ] Updated `share.html` → `PLAY_STORE_PACKAGE` to match mobile app
- [ ] Updated `apple-app-site-association` → `TEAM_ID`
- [ ] Updated `assetlinks.json` → SHA256 fingerprint

---

## 🔄 SYNCHRONIZATION GUIDE

These values must match across mobile and web:

| Value | Mobile (.env) | Web (share.html) |
|-------|---------------|------------------|
| API URL | `API_BASE_URL` | `API_BASE_URL` |
| App Store ID | `APP_STORE_ID` | `APP_STORE_ID` |
| Package Name | In `PLAY_STORE_URL` | `PLAY_STORE_PACKAGE` |

**Example:**
```
Mobile .env:
  APP_STORE_ID=987654321
  PLAY_STORE_URL=https://play.google.com/store/apps/details?id=com.myapp.rider

Web share.html:
  APP_STORE_ID = '987654321'
  PLAY_STORE_PACKAGE = 'com.myapp.rider'
```

---

## 🚨 IMPORTANT NOTES

1. **Nothing is hardcoded anymore** - All values come from config
2. **Build scripts automatically replace placeholders** - No manual editing of AndroidManifest.xml or iOS files needed
3. **Keep mobile and web configs in sync** - Same IDs and URLs in both places
4. **Commit `.env.example` but NOT `.env`** - `.env` contains your actual keys and is in `.gitignore`

---

## 📖 MORE INFO

See [CONFIGURATION_GUIDE.md](CONFIGURATION_GUIDE.md) for detailed step-by-step instructions.
