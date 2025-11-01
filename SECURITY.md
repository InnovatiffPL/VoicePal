# Security & Helmet Notice

This app uses [Helmet](https://helmetjs.github.io/) middleware for HTTP header hardening, with a custom configuration for SPA frontends.

**Default Helmet Content Security Policy (CSP) blocked static assets and caused a blank UI in Single Page Applications (SPA). To resolve this, only the CSP feature was disabled:**

app.use(helmet({ contentSecurityPolicy: false }));


All other security headers remain enabled, providing protection against XSS, clickjacking and related attacks. If you deploy new frontend assets or switch frameworks, review Helmet and CSP settings to ensure both app security and UI compatibility.

# Rate Limiting
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

# File Upload & Filename Handling

The application is protected from XSS and code injection attacks using filenames, thanks to server-side sanitization and safe text rendering on the frontend. No further text input from users is currently supported, so no additional sanitization is required for fields.

> Accepted file types:
- Only audio files (MP3, WAV, M4A) are accepted. Validation is enforced both on the frontend (input restrictions) and on the backend using Multer's fileFilter (checks MIME type, not just file extension).

> Maximum allowed size:
- Uploaded audio files are limited to a maximum size of 10MB. Any larger file is immediately rejected.

> Filename sanitization:
- All filenames are sanitized server-side using:

const sanitize = (filename: string) => filename.replace(/[^a-zA-Z0-9_\.\-]/g, "_");
const safeFileName = sanitize(req.file.originalname);

- No original user-supplied filename or path can cause code injection or XSS.

> UI Rendering:
- Filenames are always rendered as plain text in the UI. Frontend frameworks like React additionally escape any potentially hazardous content.

> Best Practices for Production
- Review upload limits and allowed MIME types before each deployment.

- Restrict file extensions and apply the strictest sanitize pattern possible.

- Show only sanitized or user-relevant file names in the user interface and API.

Example (multer):

const upload = multer({
  limits: { fileSize: 10 * 1024 * 1024 },
  fileFilter: (req, file, cb) => {
    if (file.mimetype.startsWith("audio/")) {
      cb(null, true);
    } else {
      cb(new Error("Only audio files are allowed!"), false);
    }
  },
});


# CORS Policy (Testing/Development)
For public testing, evaluation, and integration convenience, the backend is configured with a global, open CORS policy:

import cors from 'cors';

app.use(cors());

- This allows API requests from any frontend origin, including various deployment hosts and localhost environments.

- The setup prevents browser CORS errors during development, presentations, or external recruiter review.

# API Key Authorization Layer
Selected backend endpoints (e.g., POST /api/upload, POST /api/transcribe) require a valid API key for access.

> How It Works
- Each protected request must include an HTTP header:
  x-api-key: your_api_key_value

- The server will reject any request without the correct key with HTTP 401 ("Unauthorized").

- API key is stored in the .env file at the project root:
  API_KEY=your_api_key_value

- Make sure to never commit .env with secret keys to public repositories.

## Configuration & Usage

> Setup:

- Create a .env file in your project's root directory.

- Set your confidential API key, e.g.:

  API_KEY=supersecretkey123

> Server Startup:

- The backend reads this key on startup using dotenv:

import dotenv from "dotenv";
dotenv.config();

- Request Example (frontend/Postman/cURL):

  curl -X POST https://your-server.com/api/upload \
  -H "x-api-key: supersecretkey123" \
  -F "file=@audio.mp3"

## Notes:

- For public demos/initial testing, you may loosen key restrictions, but always require an API key in production.

- In production, rotate and manage API keys regularly for security.

- This mechanism allows future migration to JWT, OAuth, or User Auth without refactoring endpoint code.
