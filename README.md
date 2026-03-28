#Intune Autopilot Device Enrolment Configuration

Enterprise documentation for configuring Windows Autopilot via Microsoft Intune to streamline and standardize device enrolment — ensuring secure and consistent deployment.

## Contents

| Document | Description |
|---|---|
| [Autopilot Enrolment Guide](docs/Autopilot-Enrolment-Guide.md) | Full configuration guide following 8-section enterprise documentation standard |
| [Autopilot Enrolment Guide (PDF)](Configuring-Windows-Autopilot-for-Device-Enrolment.pdf) | Downloadable PDF version for client delivery |

## Key Highlights

- **Dynamic device group** using ZTDId-based membership rules to automatically target all Autopilot-registered devices
- **Intune policy and app deployment** — security baselines, configuration profiles, compliance policies, and endpoint protection assigned to a unified device group
- **Enrolment Status Page (ESP)** configuration ensuring all required applications, settings, and configurations are applied before desktop access
- **Self-Deploying deployment profile** with OOBE customisation — hidden license terms, hidden privacy settings, standard user accounts, and automated keyboard configuration
- **Device naming templates** using `%SERIAL%` and `%RAND:x%` macros for consistent naming conventions across the organisation
- **Hardware ID registration** — OEM bulk upload and manual PowerShell-based HWID extraction procedures

## Documentation Standard

This project follows an 8-section enterprise documentation template:

1. Name
2. Purpose
3. Scope
4. Configuration Details
5. Dependencies
6. Impact
7. Risks / Considerations
8. Notes

## Author

Prepared by **Olu Adelokiki** — [LinkedIn](https://www.linkedin.com/in/olukunmi-adelokiki)

---

*Generated: 2026-03-28*
