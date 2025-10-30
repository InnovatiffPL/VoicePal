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

During development and testing, the express-rate-limit middleware sometimes did not activate after large batches (e.g. 120+ iterations in Postman Runner). However, after a short time or a couple of additional manual requests, the limiter consistently started returning HTTP 429 ("Too many requests") as expected.

This suggests that rate limiting is active and effective, but the enforcement window may behave slightly differently due to the way Replit proxies/networking and IP/session handling work. If you observe irregular activation, try waiting briefly or sending a few more requests to trigger the expected rate limiting behavior.  

In production or on a local server, rate limiting should act strictly according to configuration.

For reliable verification, run the server locally or on a production-like VPS.

This setup meets typical security and anti-abuse requirements for production Express apps. For advanced scenarios or distributed deployments, consider using persistent stores (like Redis) for rate limiter state.
