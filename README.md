# VoicePal - Meeting Follow-up Assistant

VoicePal is a production-ready tool for audio meeting recording, automatic transcription, and follow-up analysis designed for teams, consultants, and business experts. The system enables easy audio upload, instant transcript and summary generation, and secure, scalable data management.

## Features:

> Audio Upload: Supports MP3, WAV, M4A files up to 25MB, with file validation and security filtering.

> Automated Transcription & Analysis: Generates meeting transcripts, action summaries, and status updates.

> Persistent Meeting Record: Stores files and metadata in a production database for real-time access and history.

> Robust REST API: The /api/meetings/process endpoint allows uploading meeting recordings from any client, integration, or tool.

> Production Database Ready: Secure, autoscaling backend (Replit DB) for live user data management and storage.​

## How It Works:

> The user uploads an audio file (MP3/WAV/M4A) through the web UI or REST API.

> File is validated, uploaded to the uploads/ directory, and registered in the production database.

> The system transcribes the audio, analyzes the meeting, and archives results and metadata.

> Transcript, summary, and meeting details are returned via the frontend or API.

## Using the API (Postman Example)

Endpoint:
POST /api/meetings/process

Request Body:

Type: form-data

Key: audio (type: File, value: MP3/WAV/M4A, max 25MB)

Response:

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

## Security:

Multer middleware handles validation and file limits.

Database operations are secure and production-ready.

Production-Ready Infrastructure

## Database: Connected to production Replit DB (autoscale, secure; see blue banner for confirmation).​

> Hosting: Public endpoint (https://voice-pal-ai-coretexcopy.replit.app), autoscaling (4 vCPU / 8 GiB RAM).

> Error Handling: Every endpoint returns meaningful error codes and messages for API clients.

## Sample Workflow:

- Upload an audio file via the VoicePal web interface or API.

- The backend saves, processes, and analyzes the content.

- Get structured JSON output with full metadata and transcript for further integration, reporting, or analysis.

## Target Audience:

Business teams, consultants, HR, educators, and professionals needing reliable, automated meeting documentation.

B2B integrators and technical partners (Zapier, CRM tools, workflow automation platforms).

## Technical Overview:

> Backend: Node.js/Express (TypeScript)

> File upload: Multer, with custom file validation and size limits

> Database: Production-ready, autoscaling (Replit DB)

> API: RESTful, JSON responses, form-data file upload

> Secure, scalable, modern architecture for business use

## Author
Mateusz Gredes
Lead developer, solution architect, system integrator

Ready for demos, business pilots, integrations, and production deployment. Feel free to reach out, fork, and contribute or connect for feedback and partnerships!
