# VoicePal - Meeting Follow-up Assistant

VoicePal is a production-ready solution for audio meeting recording, automatic transcription, and follow-up analysis. It is designed for business teams, consultants, and professionals who require secure, scalable meeting documentation.

## Features

- Audio Upload: Supports MP3, WAV, and M4A files up to 25MB, with validation and security filtering.

- Automated Transcription & Analysis: Generates meeting transcripts, action summaries, and status updates.

- Persistent Meeting Records: Stores files and metadata in a production database, enabling real-time access and full meeting history.

- Robust REST API: /api/meetings/process allows uploading meeting recordings from any client or integration.

- Production Database Ready: Secure, autoscaling backend (Replit DB) for live user data management and storage.

## How It Works

1. The user uploads an audio file (MP3/WAV/M4A) via the web UI or REST API.

2. The file is validated, uploaded to the server, and registered in the production database.

3. The system transcribes the audio, analyzes the meeting, and archives results and metadata.

4. The transcript, actionable summary, and meeting details are returned by the frontend or API.

## Using the API (Postman Example)

Endpoint:
POST /api/meetings/process

Request Body:

Type: form-data

Key: audio (type: File, accepts MP3/WAV/M4A, max 25MB)

Sample Response:

### JSON
{
  "meeting": {
    "id": "string",
    "fileName": "string",
    "fileSize": "integer",
    "uploadedAt": "ISO8601 datetime",
    "status": "completed",
    "transcription": "string"
  }
}

## Security

> File validation and size limits are handled by Multer middleware.

> All database operations are secure and production-ready.

> Each endpoint returns clear error codes and messages for API clients.

## Known Issues & Workarounds

> Blank page after installing helmet package:
The limitation was caused by Helmet's default Content Security Policy (CSP), which automatically blocks loading static files—such as index.html, CSS, or script files—in Single Page Applications (SPA) like my frontend (React or Vite, for example). Because of this, instead of the intended UI, users were presented with a blank white page.

- I disabled only the strictest policy (CSP) as shown below:
app.use(
 helmet({
    contentSecurityPolicy: false
   })
  );

- I kept all other Helmet security defaults in place (such as XSS protection, X-Frame-Options headers, and similar), but with CSP disabled, the frontend now loads properly without being blocked.

> Rate Limiting
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

## Note on Testing

If you are running the project on Replit or other cloud sandbox environments, you might not see the expected HTTP 429 responses when stress-testing the API, due to reverse proxy/load balancer architecture, dynamic agent assignment, or session isolation.
For reliable verification, run the server locally or on a production-like VPS.

## Infrastructure

- Database: Connected to production Replit DB (autoscale, secure; see status for confirmation).

- Hosting: Public endpoint (https://voice-pal-ai-coretexcopy.replit.app), autoscaling (4 vCPU / 8 GiB RAM).

## Sample Workflow

- Upload an audio file via the VoicePal web interface or API

- The backend saves, processes, and analyzes the content

- Receive structured JSON output with full metadata and transcript, ready for further integration

## Target Audience

> Business teams, consultants, HR, educators, and professionals needing streamlined, automated meeting documentation

> B2B integrators, CRM and workflow tool partners, and automation platforms

## Technical Overview

- Backend: Node.js/Express (TypeScript)

- File upload: Multer with custom file validation and size limits

- Database: Production-ready, autoscaling (Replit DB)

- API: RESTful, JSON responses, form-data file upload

- Modern, secure, and scalable architecture

## Author

Mateusz Gredes
Lead Developer · Solution Architect · System Integrator

Ready for demos, pilots, integrations, and production deployment. Feel free to reach out, fork, contribute, or connect for feedback and partnerships!
