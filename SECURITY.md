# Security Policy

## Supported versions

| Version | Supported |
|---------|-----------|
| 1.1.x   | Yes       |
| < 1.1   | No        |

## Reporting a vulnerability

If you discover a security issue, **do not open a public issue.** Instead:

1. Email the maintainer directly (check the GitHub profile for contact info)
2. Include a description of the vulnerability and steps to reproduce
3. Allow reasonable time for a fix before public disclosure

## Security considerations

This tool handles sensitive data:

- **Sudo password** — held in memory only, never written to disk, discarded on quit
- **WiFi data** — SSID, BSSID, signal strength stored in `data/survey.json` (local only, gitignored)
- **Remote agent** — listens on `0.0.0.0:5555` with no authentication. Only run on trusted networks.
- **Flask server** — development server, not hardened for production. Bind to `127.0.0.1` if you don't need remote/phone access.

## Scope

This is a local development tool, not a production service. It is not designed to be exposed to the public internet.
