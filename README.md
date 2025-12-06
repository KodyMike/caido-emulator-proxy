# Setting Up Caido with Android Emulator

Intercept Android traffic using Caido proxy with an Android emulator.

## Prerequisites

- Linux machine with snap
- Caido installed and running on `127.0.0.1:8080`

## Step 1: Install Android Tools

```bash
sudo snap install androidsdk

androidsdk "platform-tools" "emulator" "platforms;android-34" "system-images;android-34;google_apis;x86_64"
# If you need Play Store (not rootable): "system-images;android-34;google_apis_playstore;x86_64"
```

## Step 2: Create and Launch Emulator

```bash
avdmanager create avd -n caido_test -k "system-images;android-34;google_apis;x86_64"

emulator -avd caido_test &

adb devices
# Expected: List of devices attached
#           emulator-5554   device
```

## Step 3: Configure Proxy

```bash
adb reverse tcp:8080 tcp:8080

adb shell settings put global http_proxy 127.0.0.1:8080
adb shell settings put global https_proxy 127.0.0.1:8080

adb shell settings get global http_proxy
# Expected: 127.0.0.1:8080
```

## Step 4: Install CA Certificate

1. In emulator browser, go to `http://127.0.0.1:8080/ca.crt`
2. Install via **Settings > Security and privacy > Encryption and Credentials > Install a certificate > CA certificate > INSTALL ANYWAY > Select cert from Recent**

## Step 5: Test

Open any HTTPS site in emulator browser — traffic should appear in Caido's HTTP History.

## Disable Proxy

```bash
adb shell settings put global http_proxy :0

adb reverse --remove-all
```

## Quick Reference

```bash
adb reverse tcp:8080 tcp:8080                            # Enable tunnel
adb shell settings put global http_proxy 127.0.0.1:8080  # Enable proxy
adb shell settings get global http_proxy                 # Check status (expect: 127.0.0.1:8080)
adb shell settings put global http_proxy :0              # Disable proxy (expect: null)
```

## References

- [Caido Android Setup (GUI method)](https://docs.caido.io/tutorials/android_configuration) — For physical devices or GUI-based configuration
- [System Certificate Installation](https://docs.caido.io/tutorials/add_certificate.html) — For apps with certificate pinning
