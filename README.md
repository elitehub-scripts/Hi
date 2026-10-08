# ELITEHUB — Native Android Client

ELITEHUB is a high-performance native Android application built with Kotlin and Jetpack Compose, designed to connect directly to the existing EliteHub website and script backend (`app-api.php`, `publish.php`, `raw.php`, `config.php`).

The Android app and the website are peers of the same repository and share the same script database and storage.

---

## 1. Central Configuration (Base URL & App Key)

### Option A: Via AI Studio Secrets or Environment (`.env` / `.env.example`)
Open `.env` (or configure via the AI Studio Secrets panel):
```properties
# Backend Root URL (must end with trailing slash)
ELITEHUB_BASE_URL=https://YOUR-DOMAIN.com/

# Secret app key matching config.php on the server
# Attached automatically in HTTP header: X-EliteHub-App-Key
ELITEHUB_APP_KEY=elitehub_mobile_secure_key_2026
```

### Option B: Runtime / In-App Dev Configuration
1. Open the app and navigate to **Profile -> App Settings** (or **Profile -> Connection Diagnostics**).
2. Enter your live server Base URL (e.g. `https://my-elitehub-domain.com/`) and App Key.
3. Tap **Save Settings** or **Apply as Active App Configuration**.
4. The app immediately updates its OkHttp client and reconnects to your server.

---

## 2. Dedicated Mobile Gateway (`app-api.php`)

All Android client requests route through `app-api.php`. Every request attaches the required security header:
```http
X-EliteHub-App-Key: <ELITEHUB_APP_KEY>
```
Handled transparently by `com.example.data.api.EliteHubAppKeyInterceptor`.

### Authoritative Backend Contract:
- **List Scripts**: `GET app-api.php?action=list[&email=...]`
- **Get Owned Script**: `GET app-api.php?action=get&name=<NAME>&email=<EMAIL>`
- **Publish Script**: `POST app-api.php` (`action=publish`, `name`, `language`, `source`, `email`, `profile`)
- **Edit/Save Script**: `POST app-api.php` (`action=save`, `name`, `original_name`, `language`, `source`, `email`)
- **Delete Script**: `POST app-api.php` (`action=delete`, `name`, `email`)
- **Public Raw Source**: `GET raw.php?name=<NAME>` or direct `raw_url`

---

## 3. Security & Ownership Reality

- **Access Gating**: `X-EliteHub-App-Key` gates access to the mobile API gateway.
- **Authoritative Ownership**: The backend validates permissions for `edit`, `delete`, and `get` by comparing the submitted email hash (SHA-256) with the author's stored hash.
- **Client Security**: App keys and emails are not logged in Logcat in release builds.

---

## 4. Key Native Features

- **Full Jetpack Compose & Material 3**: Dark-first cyber-developer theme, accessible contrast, smooth transitions.
- **Interactive Code Viewer**:
  - Gutter with line numbers
  - Monospace typography
  - Horizontal & vertical scrolling
  - Real-time in-code text search with next/previous match jump & highlight
  - One-tap copy full code & raw URL
  - Line wrap toggle
- **Script Editor**:
  - Full editor supporting all allowed languages: `lua`, `els`, `txt`, `js`, `json`, `py`
  - 1 MiB size limiter with live byte counter
  - Quick syntax insertion bar (keywords, brackets, functions)
  - Rename preservation (`original_name`)
- **Local Persistence & Offline Cache**:
  - **Room Database**: Caches script feed and code offline with instant fallback and "Offline Mode" indicators
  - **DataStore Preferences**: Securely stores developer profile, owner email, favorite bookmarks, and view history
- **Connection Diagnostics**:
  - 6-point live verification tool (Base URL, HTTPS TLS, Reachability, App Key acceptance, List endpoint, Valid JSON)

---

## 5. Build & Test Commands

Run tests:
```bash
gradle :app:testDebugUnitTest
```

Build Debug APK:
```bash
gradle :app:assembleDebug
```

Build Release APK:
```bash
gradle :app:assembleRelease
```


## Connection Fix Included
- Runtime Base URL and App Key are now actually read from Android DataStore instead of hardcoded empty/locked flows.
- Saving App Settings now changes the Retrofit base URL and `X-EliteHub-App-Key` used by live requests.
- Connection Diagnostics tests the currently active configuration instead of always testing the old locked values.
- Blank custom settings safely fall back to the production defaults.
