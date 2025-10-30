## Security & Helmet Notice

This app uses [Helmet](https://helmetjs.github.io/) middleware for HTTP header hardening, with a custom configuration for SPA frontends.

**Default Helmet Content Security Policy (CSP) blocked static assets and caused a blank UI in Single Page Applications (SPA). To resolve this, only the CSP feature was disabled:**

app.use(helmet({ contentSecurityPolicy: false }));


All other security headers remain enabled, providing protection against XSS, clickjacking and related attacks. If you deploy new frontend assets or switch frameworks, review Helmet and CSP settings to ensure both app security and UI compatibility.

## Rate Limiting
The backend implements HTTP request rate limiting using the express-rate-limit middleware to protect against abuse (e.g. brute-force, DDoS, automated scraping).

- By default, the limiter is set as a global middleware:

const limiter = rateLimit({
  windowMs: 10 * 60 * 1000, // 10 minutes
  max: 100,                  // limit each IP to 100 requests per 10 minutes
  standardHeaders: true,
  legacyHeaders: false,
  message: { error: 'Too many requests, please try again later.' }
});
app.use(limiter);

- All endpoints are protected and will respond with HTTP 429 ("Too Many Requests") if the specified limit is exceeded.

**Note on Testing**

If you are running the project on Replit or other cloud sandbox environments, you might not see the expected HTTP 429 responses when stress-testing the API, due to reverse proxy/load balancer architecture, dynamic agent assignment, or session isolation.
For reliable verification, run the server locally or on a production-like VPS.

This setup meets typical security and anti-abuse requirements for production Express apps. For advanced scenarios or distributed deployments, consider using persistent stores (like Redis) for rate limiter state.
