## Security & Helmet Notice

This app uses [Helmet](https://helmetjs.github.io/) middleware for HTTP header hardening, with a custom configuration for SPA frontends.

**Default Helmet Content Security Policy (CSP) blocked static assets and caused a blank UI in Single Page Applications (SPA). To resolve this, only the CSP feature was disabled:**

app.use(helmet({ contentSecurityPolicy: false }));


All other security headers remain enabled, providing protection against XSS, clickjacking and related attacks. If you deploy new frontend assets or switch frameworks, review Helmet and CSP settings to ensure both app security and UI compatibility.
