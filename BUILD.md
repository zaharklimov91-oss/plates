# Plates Game - Build Instructions

## Android (APK)
Works out of the box with GitHub Actions. The `build.yml` workflow automatically builds debug APK on manual dispatch.

```bash
npm install
npx cap add android
npx cap sync android
cd android && ./gradlew assembleDebug
```

## iOS (IPA)
Requires one-time local setup to generate the `ios/` folder, which must be committed to the repository.

### Local Setup (macOS only)
1. Install dependencies:
```bash
npm install
```

2. Add iOS platform to Capacitor (generates `ios/` folder):
```bash
npx cap add ios
```

3. Sync web assets:
```bash
npx cap sync ios
```

4. Install CocoaPods dependencies:
```bash
cd ios/App
pod install
cd ../..
```

5. Commit the `ios/` folder:
```bash
git add ios/
git commit -m "Add iOS project files"
git push
```

### GitHub Actions iOS Build
Once `ios/` is committed, the `build-ipa.yml` workflow becomes available for manual dispatch.

**Required secrets:**
- `P12_BASE64` - Development certificate (base64 encoded)
- `P12_PASSWORD` - Certificate password
- `MOBILEPROVISION_BASE64` - Provisioning profile (base64 encoded)
- `KEYCHAIN_PASSWORD` - Keychain password for CI environment

The workflow will:
1. Sync the web directory to iOS project
2. Import the development certificate and provisioning profile
3. Build an unsigned IPA for development distribution
4. Upload as an artifact

### Certificates & Provisioning

To generate the required secrets:

1. **Export certificate:**
```bash
# From Keychain (macOS)
# Export "Apple Development" certificate as .p12 file
cat cert.p12 | base64 | pbcopy
```

2. **Export provisioning profile:**
```bash
# From Xcode / Apple Developer portal
# Download .mobileprovision file
cat profile.mobileprovision | base64 | pbcopy
```

3. **Add to GitHub:**
   - Go to repo Settings → Secrets and variables → Actions
   - Add the base64-encoded values as secrets

---

**Note:** The iOS setup is only needed once. After committing the `ios/` folder, all CI builds will use it.
