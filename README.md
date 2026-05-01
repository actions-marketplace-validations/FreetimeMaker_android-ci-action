# Android Build, Sign & Release

A reproducible, CI‑friendly GitHub Action that builds Android apps using Gradle, detects the version from your Gradle files, automatically creates git tags, signs APK/AAB artifacts using either a Base64 keystore or a repository keystore, and outputs final release‑ready files.  
Runs on all GitHub‑hosted runners without Node or Docker — pure composite action.

---

## 🚀 Features

- 🔧 Build Android apps using any Gradle task (default: `assembleRelease`)
- 🔐 Sign APK/AAB artifacts using `apksigner`
- 🗂️ Supports Base64 keystores **and** repo‑based keystores
- 🏷️ Automatic version detection (`versionName` + `versionCode`)
- 🏷️ Automatic git tag creation (`v<versionName>`)
- 📦 Outputs final signed artifacts
- 🏁 Fully reproducible, no external dependencies
- 🌐 Works on all GitHub‑hosted runners

---

## 📥 Inputs

| Name | Required | Description |
|------|----------|-------------|
| `keystore` | no | Base64‑encoded keystore (ignored if `use_repo_keystore=true`) |
| `keystore_password` | yes | Password for the keystore |
| `key_alias` | yes | Alias of the signing key |
| `key_password` | yes | Password for the signing key |
| `gradle_task` | no | Gradle task to run (default: `assembleRelease`) |
| `use_repo_keystore` | no | Use a keystore stored in the repository (`true` / `false`) |
| `repo_keystore_path` | no | Path to the repo keystore (default: `release-key.jks`) |
| `auto_tag` | no | Automatically create git tag based on versionName (`true` / `false`) |

---

## 📤 Outputs

| Name | Description |
|------|-------------|
| `signed_apk` | Path to the final signed APK |
| `version_name` | Detected versionName |
| `version_code` | Detected versionCode |

---

## 🧪 Example Workflow (Base64 keystore + auto‑tagging)

```yaml
name: Build Android App

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: FreetimeMaker/android-build-sign-release@v1.1.0
        id: android
        with:
          keystore: ${{ secrets.KEYSTORE_BASE64 }}
          keystore_password: ${{ secrets.KEYSTORE_PASSWORD }}
          key_alias: ${{ secrets.KEY_ALIAS }}
          key_password: ${{ secrets.KEY_PASSWORD }}
          auto_tag: true

      - uses: actions/upload-artifact@v4
        with:
          name: Signed APK
          path: ${{ steps.android.outputs.signed_apk }}

      - uses: softprops/action-gh-release@v2
        with:
          tag_name: v${{ steps.android.outputs.version_name }}
          files: ${{ steps.android.outputs.signed_apk }}
```

## 🧪 Example Workflow (Keystore from repository)

```yaml
steps:
  - uses: actions/checkout@v4
    with:
      fetch-depth: 0

  - uses: FreetimeMaker/android-build-sign-release@v1.1.0
    id: android
    with:
      use_repo_keystore: true
      repo_keystore_path: "keystore/release.jks"
      keystore_password: ${{ secrets.KEYSTORE_PASSWORD }}
      key_alias: ${{ secrets.KEY_ALIAS }}
      key_password: ${{ secrets.KEY_PASSWORD }}
      auto_tag: true

  - uses: actions/upload-artifact@v4
    with:
      name: Signed APK
      path: ${{ steps.android.outputs.signed_apk }}
```

### 🔐 Keystore Options
1. Base64 Keystore (recommended)

Just run this command: base64 -w 0 my-release-key.jks > keystore.b64

## 2. Repository Keystore
Place your keystore anywhere in the repo:
keystore/release.jks

### 🏷️ Version Detection
Extracts:
  versionName
  versionCode

from:
  app/build.gradle
  app/build.gradle.kts

## 🏷️ Automatic Tag Creation
If auto_tag: true, the Action creates:
  v<versionName>
and pushes it automatically.

# 📘 License
MIT
