# Configuring Intune Autopilot for Device Enrolment

## 1. Name

Intune Autopilot Device Enrolment Configuration

## 2. Purpose

Streamline and standardise device enrolment via Microsoft Intune using Windows Autopilot, ensuring secure and consistent deployment across the organisation. This guide covers creating dynamic device groups, configuring Intune policies and applications, setting up the Enrolment Status Page (ESP), and creating a Self-Deploying deployment profile.

## 3. Scope

- Creation of Entra ID dynamic device groups for Autopilot targeting
- Configuration of Intune device policies (security baselines, compliance, endpoint protection)
- Application deployment via Intune (LoB, Win32, Microsoft Store apps)
- Enrolment Status Page (ESP) profile creation and settings
- Windows Autopilot deployment profile with OOBE customisation
- Device naming template configuration
- Hardware ID (HWID) registration for Autopilot enrolment

**Tool:** Microsoft Intune Portal

---

## 4. Configuration Details

### Step 1 — Create a Device Group

Use a **Device Dynamic Group** to target all Autopilot devices in the organisation. Name the group **"Sec - All Laptop/Desktops"**.

Dynamic membership rule syntax:

```
(device.devicePhysicalIDs -any (_ -contains "[ZTDId]"))
```

This rule automatically captures all devices registered with a Zero-Touch Deployment ID (ZTDId), which is the identifier assigned when a device’s hardware hash is uploaded to the Autopilot service.

---

### Step 2 — Configure Intune Policies and Apps

#### Configure Device Policies

Set up device policies to ensure devices meet organisational standards:

- **Security baselines** — Apply recommended security configurations for Windows endpoints.
- **Configuration profiles** — Define Wi-Fi, VPN, certificates, and device restriction settings.
- **Compliance policies** — Enforce requirements such as BitLocker encryption, minimum OS version, and antivirus status.
- **Endpoint protection** — Configure Microsoft Defender for Endpoint integration.

#### Deploy Applications

Configure and deploy applications based on organisational needs:

- **Line-of-Business (LoB) apps** — Custom internal applications.
- **Win32 apps** — Packaged desktop applications deployed via `.intunewin` format.
- **Microsoft Store apps** — Apps distributed through the Microsoft Store for Business.

> **Note:** In most cases, the deployment of applications has already been implemented during the initial infrastructure setup. Ensure all apps and policies are assigned to the group: **"Sec - All Laptop/Desktops"**.

---

### Step 3 — Configure Enrolment Status Page

The Enrolment Status Page (ESP) ensures that all required applications, settings, and configurations are installed and applied before the user can access the desktop during Windows Autopilot deployment. It plays a critical role in ensuring compliance, security, and a smooth user experience.

**Navigation Path:**

```
Devices > Windows > Windows enrolment > Enrolment Status Page
```

Create a new Enrolment Status Profile with the name **"Enrolment Status Profile"** and description *"Apps, settings, and configurations requirements"*.

#### Recommended Settings

| Setting | Value | Rationale |
|---|---|---|
| Show app and profile configuration progress | Yes | Users can see deployment progress |
| Show an error when installation takes longer than specified minutes | 60 | Allows time for large app installs |
| Show custom message when time limit or error occurs | Yes | Provide helpdesk contact info |
| Turn on log collection and diagnostics page for end users | Yes | Aids troubleshooting |
| Only show page to devices provisioned by OOBE | Yes | Targets Autopilot devices only |
| Block device use until all apps and profiles are installed | Yes | Ensures full compliance before access |
| Allow users to reset device if installation error occurs | Yes | Enables self-service recovery |
| Allow users to use device if installation error occurs | Yes* | Balance between UX and compliance |
| Block device use until required apps are installed | Selected | Critical apps must be present |

> **Note:** Enrolment errors can occur due to network glitches. I always enable *"Allow users to reset device if installation error occurs"*. For stricter environments, choose **No** for *"Allow users to use device if installation error occurs"* — the device will sync and complete enrolment in the background.

Assign the profile to the group **"Sec - All Laptop/Desktops"**. Review and create.

---

### Step 4 — Create Deployment Profile

**Navigation Path:**

```
Devices > Windows > Windows enrolment > Deployment Profiles
```

Click **+ Create profile** > Select **Windows PC**.

Enter a name (e.g., *"Autopilot Config"*) and description (e.g., *"Windows Autopilot Configuration for device enrollment"*).

#### Out-of-Box Experience (OOBE) Settings

| Setting | Value | Notes |
|---|---|---|
| Deployment mode | Self-Deploying | Minimizes user interaction |
| Join to Microsoft Entra ID as | Microsoft Entra joined | Auto-handled in self-deploying mode |
| Microsoft Software License Terms | Hide | Streamlines the OOBE experience |
| Privacy settings | Hide | Reduces prompts during setup |
| Hide change account options | Hide | Prevents account changes |
| User account type | Standard | Best practice for security |
| Language (Region) | Operating system default | Supports localised preferences |
| Automatically configure keyboard | Yes | Matches keyboard to region |
| Apply device name template | Yes | Consistent naming convention |

#### Device Naming Template

Use macros to maintain consistent naming across the organisation:

- **%SERIAL%** — Uses the device’s hardware serial number.
- **%RAND:x%** — Generates a random string of x digits.

**Example:** EFAutoPilot%RAND:3% produces device names like **EFAUTOPILOT195**.

#### Assign and Review

Assign the deployment profile to the group **"Sec - All Laptop/Desktops"**.

---

### Hardware ID Registration

Before enrolling a device via Autopilot, you must upload its **Hardware ID (HWID)**.

- **Bulk purchases:** OEMs can upload the hardware hashes directly to your Autopilot service.
- **Individual devices:** You will need to extract and upload them manually using PowerShell.

For detailed steps on collecting hardware IDs, see: [Collecting the device hardware ID (HWID) for Windows Autopilot enrolment](https://learn.microsoft.com/en-us/autopilot/add-devices)

---

## 5. Dependencies

- Microsoft Intune licence (included in Microsoft 365 Business Premium, E3, E5, or standalone)
- Microsoft Entra ID (Azure AD) P1 or P2 for dynamic group membership
- Windows 10/11 Pro, Enterprise, or Education (Autopilot requires these editions)
- Network connectivity for device registration and policy sync
- Hardware vendor support for HWID/OEM registration (for bulk enrolment)

## 6. Impact

- All new devices enrolled via Autopilot will automatically receive the configured policies, applications, and settings
- Users will experience a consistent, standardised OOBE with minimal interaction required
- Devices are Entra ID-joined and Intune-managed from first boot
- IT administrators gain centralised visibility and control over the device fleet

## 7. Risks / Considerations

- **Network dependency** — Autopilot enrolment requires a stable internet connection; network glitches may cause ESP timeouts
- **ESP timeout** — Large application deployments may exceed the configured timeout value; monitor and adjust as needed
- **Self-Deploying mode limitations** — Requires a TPM 2.0 chip; not supported on virtual machines or devices without TPM
- **Hardware hash accuracy** — Ensure HWID CSV files are not corrupted before importing into Intune
- **Group assignment conflicts** — Verify that policy and app assignments do not conflict between multiple device groups

## 8. Notes

- The dynamic group rule may take up to 24 hours to fully populate after hardware hashes are imported
- For stricter compliance environments, disable *"Allow users to use device if installation error occurs"* in the ESP settings
- Device naming templates are limited to 15 characters; plan naming conventions accordingly
- Consider creating a separate Autopilot profile for shared/kiosk devices if deployment requirements differ

---

## Author

Prepared by **Olu Adelokiki**
Lead EUC Engineer | UserCompute LTD
[www.usercompute.com](https://www.usercompute.com)

---

*Generated: 2026-03-28*
