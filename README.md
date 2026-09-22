# BLA VPN

A modern **Android Xray client** with Material 3 UI. Supports the same core features used by [v2rayN](https://github.com/2dust/v2rayN) / [v2rayNG](https://github.com/2dust/v2rayNG).

| Protocol | Transports | Security |
|----------|------------|----------|
| **VLESS** | TCP, WS, gRPC, HTTPUpgrade, **XHTTP**, KCP, QUIC, H2 | none, TLS, **REALITY** |
| **VMess** | same | TLS / REALITY |
| **Trojan** | same | TLS / REALITY |
| **Shadowsocks** | TCP | AEAD methods |
| Custom JSON | full Xray config | — |

### Features

- **VLESS + REALITY + Vision** (`flow: xtls-rprx-vision`)
- **XHTTP** transport, **uTLS** fingerprints
- **Mux**, domain sniffing, DoH DNS (through tunnel), optional FakeDNS
- Routing: Global · Bypass LAN · custom **direct domains** (wildcards)
- TLS fragment (anti-DPI)
- Quick Setup wizard, multi-select servers, subscriptions (traffic / expiry when provided)
- Session limits, keep-alive, optional **LAN proxy** for other devices
- Dual-accent **glass UI** (primary + secondary colors, compact custom HSV picker)
- Shield connect control; connected only after public IP + country resolve
- **In-app updates** from [GitHub Releases](https://github.com/davoodtaba-glitch/Bla-Vpn-pulse-/releases) + dashboard **NEW** badge
- Persian (فارسی) default UI + English
- Share-link import: `vless://`, `vmess://`, `trojan://`, `ss://`, subscription URLs

---

## Screenshots

<p align="center">
  <img src="photo_2026-09-22_11-30-31.jpg" width="180" alt="Home (FA)" />
  <img src="photo_2026-09-22_11-30-29.jpg" width="180" alt="Servers (FA)" />
  <img src="photo_2026-09-22_11-30-29 (2).jpg" width="180" alt="Import (FA)" />
</p>


### Screens (overview)

- **Home** — shield connect, speeds, IP/country, update badge, subscription card  
- **Servers** — search, tabs per subscription, multi-select, latency  
- **Import** — links / multi-line paste, QR, manual VLESS  
- **Subscriptions** — refresh, rename, traffic / expire  
- **Settings** — routing, DNS presets, fragment, dual colors, language, About (update)  
- **Quick setup** — guided import / color / limits / language  

### Latest release

Install from **[Releases](https://github.com/davoodtaba-glitch/Bla-Vpn-pulse-/releases/latest)** (current: **v1.38+**).  
Inside the app: **Settings → About → Check for updates**, or use the home **NEW** badge when a newer build is published.

---

## Project structure

```
app/src/main/java/com/xraypulse/app/
  core/
    config/XrayConfigBuilder.kt
    parser/ShareLinkParser.kt
    parser/ShareLinkExporter.kt
    xray/XrayController.kt
    vpn/HevTunnel.kt
  service/XrayVpnService.kt
  data/
  ui/
```

---

## Build

### Requirements

- JDK 17+
- Android SDK (API 35 recommended)
- Gradle (wrapper included)

Create `local.properties` (not committed):

```properties
sdk.dir=/path/to/Android/Sdk
```

### Debug APK

```bash
./gradlew :app:assembleDebug
```

Output:

```
app/build/outputs/apk/debug/app-debug.apk
```

On Windows you can also use:

```powershell
.\scripts\build-debug.ps1
```

### Link Xray-core (required for real traffic)

Place `libv2ray.aar` from [AndroidLibXrayLite](https://github.com/2dust/AndroidLibXrayLite) into `app/libs/`.

**Linux / macOS / WSL**

```bash
chmod +x scripts/build-libxray.sh
./scripts/build-libxray.sh
```

**Manual**

```bash
git clone https://github.com/2dust/AndroidLibXrayLite.git
cd AndroidLibXrayLite
gomobile init
go mod tidy
gomobile bind -v -androidapi 24 -trimpath -ldflags='-s -w -buildid= -checklinkname=0' ./
cp libv2ray.aar /path/to/this-project/app/libs/
```

Optional geo databases:

```bash
# Windows PowerShell
./scripts/download-geo.ps1
```

### Install

```bash
./gradlew :app:assembleDebug
adb install -r app/build/outputs/apk/debug/app-debug.apk
```

Release APKs are published under [Releases](https://github.com/davoodtaba-glitch/Bla-Vpn-pulse-/releases).

---

## Usage

1. **Import** a share link or subscription URL (or use **Quick setup**).  
2. Select a server under **Servers**.  
3. On **Home**, tap connect and grant VPN permission.  
4. Configure **Settings** as needed (routing, fragment, ports, language).

Example VLESS + REALITY link:

```
vless://UUID@host:443?encryption=none&flow=xtls-rprx-vision&security=reality&sni=www.example.com&fp=chrome&pbk=PUBLIC_KEY&sid=SHORT_ID&type=tcp#MyServer
```

---

## Architecture notes

- Config generation follows current Xray JSON (`realitySettings`, `xhttpSettings`, Vision, hybrid domain matcher).  
- **XrayController** uses reflection so the project compiles without the AAR; with `libv2ray.aar` the real core is used.  
- **VPN path**: `VpnService` TUN + **hev-socks5-tunnel** (JNI) to local SOCKS.  
- Share formats align with **v2rayN / v2rayNG**.

---

## License

App code: MIT (this repository).  
Xray-core / AndroidLibXrayLite / hev-socks5-tunnel: their respective licenses.  
Use only with servers and networks you are authorized to access.

---

