# Signing & Store Distribution

## Current H3 status

The app review branch contains optional Gradle release-signing configuration. It reads `android/keystore.properties` only when the file exists. The committed `android/keystore.properties.example` contains field names and placeholders only; it is not a real secret file. No real keystore or release artifact is claimed yet.

For a controlled academic packaging demonstration, the signing material must be generated and used on the developer workstation. Since GitHub Actions is blocked before steps start by the account billing lock, this project does not claim CI signing or cloud-based release distribution. Do not pay for Actions or enter billing information merely to proceed with local signing.

The release endpoint remains a reserved placeholder HTTPS URL. A signed artifact using that placeholder can demonstrate build/signature mechanics but is not suitable for public distribution. Public release remains NO-GO until a real HTTPS backend and authentication/data-isolation decisions are completed.

## Signing

- Create one Android release keystore locally; do not commit it or passwords.
- Copy `android/keystore.properties.example` to `android/keystore.properties` and fill real values locally.
- The expected Android keystore path can be `android/app/pantrypal-release.jks`, with `storeFile=pantrypal-release.jks` in the properties file. The Gradle project resolves the store file relative to `android/app`.
- Keep the properties file and keystore excluded by Git ignore rules. Verify with `git check-ignore -v android/keystore.properties android/app/pantrypal-release.jks`.
- After producing release artifacts, verify the AAB with `jarsigner -verify` and the APK with `apksigner verify --print-certs`. Save outputs that show successful verification and certificate identity; never capture or publish passwords.
- Preserve a secure backup of the signing key and password in two safe locations. Losing a distributed app's signing identity may prevent future updates.

## Versioning

Bump SemVer version and Android build number before every store upload. The current review branch has versionName 1.0.0 and versionCode 1; do not reuse a versionCode on a later upload.

## Store rollout

- Use a staged rollout only after a release is approved and the real API is ready. A sample plan is 5% → 20% → 50% → 100%.
- Halt the rollout on a crash/ANR spike or critical bug report.
- Keep store metadata (screenshots, changelog, privacy labels) versioned with the release.
- If Play Console or Apple Developer access is unavailable, describe the submission path as a simulation and clearly state that no store upload occurred.

## Forced update

For a future multi-user deployment, define a minimum-supported-version policy. If a release has breaking API changes or critical fixes, decide how clients below the supported version are handled. The current MVP does not implement a remote minimum-version flag.

## Rollback

For a real service, keep the previous build available, define rollback triggers and an owner, and establish a way to revert a breaking API deployment. These operational controls have not yet been exercised on PantryPal because there is no public deployment.
