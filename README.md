# PowerShield (Version 0.0.2-BETA)

> **Advanced, All-in-One Android System Optimization, Battery Protection, Junk Cleaning & Security Suite**  
> **Target Platform:** Android 11+ (API Level 30 to 34)  
> **Package:** `com.ajtechs.powershield.android`  
> **Stack:** Kotlin 1.9+ | Android Jetpack | Material Components 3 | Coroutines & Flow | ViewBinding  

---

## 🌟 Overview

**PowerShield** is an advanced, non-root Android system management, optimization, and security platform. Designed with an ultra-responsive Material 3 glassmorphic UI, it equips users with real-time hardware telemetry, automated junk and cache purging, intelligent background app hibernation, deep battery health analytics with audible charge protection, bandwidth monitoring, and malware vulnerability analysis.

At the core of PowerShield is an **Accessibility Automation Engine** capable of reliably navigating diverse OEM Android skins (Samsung OneUI, Xiaomi MIUI/HyperOS, Oppo/OnePlus ColorOS/OxygenOS, Vivo FuntouchOS, and Stock AOSP) without requiring root permissions.

---

## 🚀 Key Features & Capabilities

### 1. 🔋 Battery Manager & Battery Health Guard
- **80% Battery Charge Protection Alarm**:
  - Monitors battery charge state in real time via `BatteryAlarmService`.
  - Fires high-priority heads-up notification with audible alarm ringtone (`AudioAttributes.USAGE_ALARM`) and pulsing vibration that pierces Silent/Vibrate/DND modes once threshold is reached.
  - User-configurable charge threshold (50%–100%, default 80%) with automatic silence timeout limit (30s / 60s) to prevent continuous ringing.
  - Fully compatible across Android 10 through 14+ (`FOREGROUND_SERVICE_TYPE_SPECIAL_USE` gated for API 34+).
- **Live Wattage & Charging Speed Telemetry**:
  - Accurately computes live charging/discharging wattage:
    $$\text{Power (Watts)} = \frac{I_{\text{mA}} \times V_{\text{mV}}}{1{,}000{,}000}$$
  - Full support for standard and ultra-fast charging protocols (SuperVOOC, HyperCharge, Dash/Warp up to 12A / 120W+) through normalized µA/mA threshold resolution.
  - Charging tiers: *Ultra-Fast* ($\ge 33\text{W}$), *Fast* ($15\text{W}–33\text{W}$), *Standard* ($4.5\text{W}–15\text{W}$), *Trickle* ($< 4.5\text{W}$), plus discharge drain severity tiers.
  - Dual Battery Capacity Resolver: Design capacity via `PowerProfile` reflection alongside actual available capacity via `BatteryManager.BATTERY_PROPERTY_CHARGE_COUNTER`.
- **Deep Sleep & Screen-Off Wakelock Analytics**:
  - Measures true kernel sleep ratio ($\text{elapsedRealtime}$ vs $\text{uptimeMillis}$) across screen-off cycles.
  - Screen-off state persists across task-killer process death.
  - Accurate rogue wake-lock flagging (identifies apps keeping the CPU awake when deep sleep ratio $< 80\%$) without false positives during active daytime usage.
- **Subsystem Drain Modeling**:
  - Categorizes app battery drain using weighted foreground/background durations and category coefficients (Gaming, GPS, Video, Social, VoIP).

### 2. ⚡ Accessibility Automation Engine
- **Cross-OEM Event-Driven UI Navigation**:
  - Operates hands-free on OEM Settings pages without pixel coordinates or root access.
  - Dynamic accessibility modes: Low-power passive filtering when idle; active state machine during optimizations.
- **Robust Multi-Action State Machine**:
  - **Cache Clear**: Traverses app settings $\rightarrow$ clicks *"Storage & cache"* $\rightarrow$ clears cache $\rightarrow$ verifies 0 B / disabled state $\rightarrow$ advances to next app.
  - **App Hibernation**: Navigates to app settings $\rightarrow$ invokes *"Force stop"* $\rightarrow$ confirms dialog $\rightarrow$ advances.
  - **Background Data Restriction**: Navigates to app settings $\rightarrow$ opens *"Mobile data & Wi-Fi"* $\rightarrow$ toggles background data OFF.
- **Cross-OEM Stability & Safeguards**:
  - Residual screen rejection preventing stale clicks between app transitions.
  - Self-termination guard protecting system components (`android`, `SystemUI`) and PowerShield itself.
  - Extended multilingual label dictionaries covering English, Spanish, French, German, Italian, Russian, Chinese (Simplified/Traditional), Japanese, and Hindi.
  - Floating pill overlay displaying progress with 1-tap emergency stop abort.

### 3. 🧹 Cache & Junk Cleaner
- **StorageStats Cache Extraction**:
  - Queries exact app caches via Android `StorageStatsManager` with parallel IO coroutines.
  - Offers both 1-Tap Automated Accessibility cleaning and targeted Manual cleaning.
- **Two-Pass Deduplication Engine**:
  - Fast size-bracket grouping followed by partial/full MD5 hash verification to safely identify duplicate files without false positives.
- **Specialized App Cleaners**:
  - **WhatsApp Cleaner**: Safely purges sent media duplicates, aged voice notes ($> 14\text{ days}$), stickers, and historical database backups (`msgstore-*.crypt*`).
  - **Telegram Cleaner**: Scans and cleans cached video notes, media files, and downloaded documents.
- **CorpseFinder (Orphaned App Remnants)**:
  - Scans `Android/data`, `Android/media`, and storage roots to discover folders left behind by uninstalled packages.
- **Large & Old File Hunter**:
  - Detects standalone files $\ge 100\text{ MB}$ and unaccessed files older than 30 days.

### 4. ❄️ App Hibernation & Process Manager (CPU Cooler)
- **Hybrid Memory & Process Detection**:
  - Direct PSS memory footprint analysis via batch `ActivityManager.getProcessMemoryInfo(pids)`.
  - Intelligent memory estimation fallback for Android 11+ SELinux sandbox restrictions.
  - Cross-references 24-hour `UsageStatsManager` activity to pinpoint active background consumers.
- **Safe Process Termination**:
  - Integrates `am.killBackgroundProcesses()` and Accessibility-driven Force Stop automation.
  - Protects system services and customizable user ignore lists.

### 5. 🌐 Network Manager & Integrated Speed Test
- **Comprehensive Network Dashboard**:
  - Live link speed, Wi-Fi SSID / BSSID, carrier info, IP address, signal dBm, and 5-day history graphs.
  - Per-app data usage breakdown ranked separately for **Mobile Data** and **Wi-Fi** (Download vs Upload).
- **Speed Test Engine**:
  - Zero-disk-allocation streaming download test using 32 KB chunk buffers.
  - 64 KB batch socket upload test.
  - Low-overhead HTTP socket round-trip ping and jitter telemetry.
- **Network & DNS Cache Flush**:
  - Clears `InetAddress` address cache, closes `HttpResponseCache`, flushes WebKit caches, and triggers soft network re-bind.

### 6. 🛡️ Security Manager & VirusTotal Scanner
- **Permission Risk Analysis**:
  - Evaluates apps based on high-risk capability clusters (Accessibility bind, Device Admin, SMS intercept, System Overlay, Location, Camera/Mic).
- **VirusTotal API Integration**:
  - High-performance SHA-256 APK checksum computation queried against VirusTotal v3 malware databases.
  - Secure API key storage backed by `EncryptedSharedPreferences` (`MasterKey.AES256_GCM`).
- **Installer & Certificate Audit**:
  - Identifies sideloaded APKs versus trusted app store installations via `PackageManager.getInstallSourceInfo()`.

### 7. 📁 Storage Manager & Document Reader
- **Storage Category Breakdown**:
  - Aggregates media files, APK packages, documents, and archives via fast MediaStore queries.
- **Integrated File Explorer**:
  - Flexible List, Grid, and Tile density layouts with hidden-file toggles and multi-action batch support.
- **Built-in Document & PDF Viewer**:
  - Hardware-accelerated `PdfRenderer` and responsive UTF-8 text/code reader.

### 8. 📱 Responsive UI & Mobile Display Modes
- **Display Style Scaling**:
  - Supports **Small**, **Standard**, and **Large** display scale modes with responsive DPI and font factor adjustments (`BaseActivity.kt`).
  - Standardized dimension tokens across `sw320dp`, `sw360dp`, and `sw400dp` screens for optimal readability.
- **Adaptive Dashboards**:
  - Responsive gauge sizing, auto-sizing text elements, and glassmorphic Material Design 3 cards.

---

## 🏗️ Architecture & Project Structure

PowerShield follows clean architectural principles with strict separation of concerns, zero-poll reactive flows, and IPC batching:

```text
com.ajtechs.powershield.android
│
├── core/
│   ├── models/            # Domain models (AppInfo, TelemetryData, BatteryUsageApp, etc.)
│   ├── services/          # AppAccessibilityService, SystemEventReceiver, AutostartBlockerService
│   │   └── automation/    # ActionStrategy state machines (CacheCleanerStrategy, HibernateStrategy, etc.)
│   ├── ui/                # BaseActivity, CircularGaugeView, FeatureCardAdapter
│   └── utils/             # BatteryTelemetry, DeepSleepTracker, ProcessManager,
│                          # BatteryCapacityResolver, PreferencesManager, AppLogger, AppIconCache...
├── home/                  # MainActivity (Dashboard & Live Telemetry)
├── batterymanager/        # Battery health, live wattage, charge alarm, deep sleep tracker
├── cachecleaner/          # Cache wipe automation, deep junk scanner, WhatsApp/Telegram cleaners
├── hibernateapps/         # Process manager, RAM PSS analysis, automated Force Stop
├── networkmanager/        # NetworkStats aggregation, top consumers, speed test engine, BG data restrictor
├── securitymanager/       # Permission risk analyzer, VirusTotal scanner, app auditing
├── storagemanager/        # Category storage aggregator, File Explorer, PDF viewer
├── appmanager/            # Batch app inspector, export APK, launch & uninstall manager
├── autostart/             # Autostart blocker, boot receivers, background app restrictions
└── settings/              # Preferences, ignore lists, automation delay tuning
```

---

## 🛠️ Tech Stack & Dependencies

- **Language**: Kotlin 1.9+
- **Minimum SDK**: API 30 (Android 11)
- **Target SDK**: API 34 (Android 14)
- **Architecture**: MVVM with Kotlin Coroutines & Flow
- **UI & Layout**: Android Jetpack, Material Design 3, ViewBinding, ConstraintLayout
- **Security**: AndroidX Security Crypto (`EncryptedSharedPreferences`)
- **Networking**: OkHttp 4 / Retrofit
- **Testing**: JUnit 4, Kotlin Test, AndroidX Test Runner

---

## 🔧 Building & Verification

### Prerequisites
- Android Studio Hedgehog / Iguana / Jellyfish or later
- JDK 17 (recommended for Gradle 8.x)
- Android SDK Platforms 30 through 34

### Fast Compilation Checks
```bash
# Verify source compilation
./gradlew compileDebugSources

# Verify unit test source compilation
./gradlew compileDebugUnitTestSources
```

### Running Unit Tests
```bash
# Run full JVM unit test suite (58+ automated tests)
./gradlew testDebugUnitTest
```

### Assembling Release APK
```bash
# Build release APK with ProGuard/R8 shrinking and keystore signing
./gradlew assembleRelease
```
*Built APK location:* `build/app/outputs/apk/release/powershield-beta-release-0.0.2-BETA.apk`

---

## 📄 License & Attribution

Developed with ❤️ by **AjTechs**.  
All rights reserved. Designed for optimal Android system health, battery longevity, and user privacy.
