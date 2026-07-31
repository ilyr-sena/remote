# wda-builder-free

Build WebDriverAgent IPA using GitHub Actions macOS runners (free) with a **FREE Apple ID** (no paid Developer Program required).

## Prerequisites (You Need to Provide)

| Item | Where to Get |
|------|--------------|
| **Apple ID** | Your Apple ID email (free) |
| **App-Specific Password** | https://appleid.apple.com → Security → App-Specific Passwords |
| **Bundle ID Prefix** | Any unique string (e.g., `com.yourname` or `com.github.yourusername`) |
| **Device UDID** | Run: `pymobiledevice3 usbmux list` on Linux |

**No paid Developer Program needed!** No Team ID required.

## Quick Setup (5 minutes)

### 1. Get Your Device UDID
```bash
pymobiledevice3 usbmux list
# Output: MuxDevice(devid=1, serial='00008110-000C694914F3801E', connection_type='USB')
# Copy the serial value → this is your UDID
```

### 2. Create GitHub Repository
```bash
cd /home/sooku/Documents/Dev/wda-builder
git init && git add . && git commit -m "WDA builder (free)"
# Create repo on GitHub (private recommended), then:
git remote add origin https://github.com/YOUR_USERNAME/wda-builder.git
git push -u origin main
```

### 3. Add Secrets in GitHub
Go to: **Repository → Settings → Secrets and variables → Actions → New repository secret**

| Secret Name | Value | Example |
|-------------|-------|---------|
| `APPLE_ID` | Your Apple ID email | `john@gmail.com` |
| `APPLE_PASSWORD` | **App-Specific Password** (not main password!) | `abcd-efgh-ijkl-mnop` |
| `BUNDLE_ID_PREFIX` | Unique prefix for bundle ID | `com.john` or `com.github.john` |
| `DEVICE_UDID` | Your iPhone UDID (40 hex chars) | `00008110-000C694914F3801E` |

**To create App-Specific Password:**
1. Go to https://appleid.apple.com
2. Sign in → Security → **App-Specific Passwords** → **+** Generate
3. Label: "GitHub Actions WDA"
4. Copy the generated password (format: `abcd-efgh-ijkl-mnop`)
4. Paste as `APPLE_PASSWORD` secret

### 4. Run the Workflow
1. Go to your repo → **Actions** tab
2. Select **"Build WebDriverAgent (Free Apple ID)"** workflow
3. Click **"Run workflow"** → **Run workflow**
4. Wait ~10-15 minutes

### 5. Download & Install
```bash
# On your Linux machine
# 1. Download IPA from GitHub Actions artifacts
gh run download -R YOUR_USERNAME/wda-builder -n WebDriverAgentRunner

# 2. Install on device
pymobiledevice3 apps install WebDriverAgentRunner.ipa

# 3. Start WDA on device (note the .xctrunner suffix)
pymobiledevice3 developer dvt xcuitest com.yourname.wda.WebDriverAgentRunner.xctrunner

# 4. Forward port
pymobiledevice3 forward 8100 8100

# 5. Use full WDA from Linux
python3 -c "
import wda
c = wda.Client('http://localhost:8100')
print('Status:', c.status())
print('Window:', c.window_size())
c.screenshot('screen.png')
print('Screenshot saved!')
"
```

## How It Works (Free Account)

| Aspect | Free Apple ID | Paid Developer Program |
|--------|---------------|------------------------|
| **Cost** | $0 | $99/year |
| **Team ID** | Not needed | Required |
| **Bundle ID** | Auto-generated unique | Your choice |
| **Cert Validity** | 7 days | 1 year |
| **Devices** | Limited (registered) | 100 devices |
| **Workflow** | Weekly rebuild | Yearly rebuild |

The workflow:
1. Generates unique Bundle ID: `com.yourname.wda.WebDriverAgentRunner`
2. Uses Xcode's **Automatic Signing** with your Apple ID
3. Creates a **7-day provisioning profile** (free account limit)
3. Runs **weekly** to refresh the expired certificate

## Automatic Weekly Rebuild

The workflow runs every Sunday (cron: `0 0 * * 0`) to rebuild with a fresh 7-day certificate.

## Install & Use on Linux

```bash
# Download IPA from GitHub Actions
gh run download -R YOUR_USERNAME/wda-builder -n WebDriverAgentRunner

# Install
pymobiledevice3 apps install WebDriverAgentRunner.ipa

# Start WDA (use YOUR bundle ID from workflow logs)
pymobiledevice3 developer dvt xcuitest com.yourname.wda.WebDriverAgentRunner.xctrunner

# Forward port
pymobiledevice3 forward 8100 8100
```

## Troubleshooting

| Error | Fix |
|-------|-----|
| `Invalid username/password` | Use **App-Specific Password**, not main Apple ID password |
| `Bundle ID already in use` | Change `BUNDLE_ID_PREFIX` to something more unique |
| `Device not found` | Ensure UDID is correct, device is trusted |
| `Code signing failed` | Verify all 4 secrets are set correctly |
| `Provisioning profile expired` | Workflow rebuilds weekly automatically |
| `No signing certificate found` | Xcode creates one automatically on first run |

## One-liner to Get UDID

```bash
pymobiledevice3 usbmux list
# Copy the 'serial' value
```

## Files

```
wda-builder/
├── .github/workflows/build-wda-free.yml  # Free Apple ID workflow
└── README.md                              # This file
```

---

**Next step:** Add the 4 secrets to your GitHub repo and run the workflow. I'll wait for confirmation.