# Android Build, Sign & Release

A reproducible, CI‑friendly GitHub Action that builds Android apps using Gradle, signs APK/AAB artifacts with a provided keystore or a keystore stored inside the repository, and outputs the final release‑ready files.  
Works on all GitHub‑hosted runners without Node or Docker — pure composite action.

---

## 🚀 Features

- 🔧 Build Android apps using any Gradle task (default: `assembleRelease`)
- 🔐 Sign APK/AAB artifacts using `apksigner`
- 🗂️ Supports Base64 keystores **and** repo‑based keystores
- 📦 Output final signed artifacts
- 🏁 Fully reproducible, no external dependencies
- 🌐 Works on all GitHub‑hosted runners
- 🧩 Ideal for CI/CD pipelines and automated releases

---

## 📥 Inputs

| Name | Required | Description |
|------|----------|-------------|
| `keystore` | no | Base64‑encoded keystore file (ignored if `use_repo_keystore=true`) |
| `keystore_password` | yes | Password for the keystore |
| `key_alias` | yes | Alias of the signing key |
| `key_password` | yes | Password for the signing key |
| `gradle_task` | no | Gradle task to run (default: `assembleRelease`) |
| `use_repo_keystore` | no | Use a keystore stored in the repository (`true` / `false`) |
| `repo_keystore_path` | no | Path to the repo keystore (default: `release-key.jks`) |

---

## 📤 Outputs

| Name | Description |
|------|-------------|
| `signed_apk` | Path to the final signed APK |
| `signed_aab` | Path to the final signed AAB (if built) |

---

## 🧪 Example Workflow (Base64 keystore)

```yaml
name: Build Android App

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v6

      - uses: FreetimeMaker/android-build-sign-release@v1.0.1
        id: android
        with:
          keystore: ${{ secrets.KEYSTORE_BASE64 }}
          keystore_password: ${{ secrets.KEYSTORE_PASSWORD }}
          key_alias: ${{ secrets.KEY_ALIAS }}
          key_password: ${{ secrets.KEY_PASSWORD }}
          gradle_task: assembleRelease

      - name: Upload signed APK
        uses: actions/upload-artifact@v7
        with:
          name: Signed APK
          path: ${{ steps.android.outputs.signed_apk }}
