---
name: capture-to-findings
description: Create a visit at a Navigate location, upload a field capture video, process it, and read the resulting findings.
api: NavigateAI API
base_url: https://api.navigateai.co
auth: http-bearer
operations:
  - api_external_api_visit_create_visit
  - api_external_api_video_upload_request_create_video_upload_request
  - api_external_api_video_upload_request_confirm_video_upload_request
  - api_external_api_visit_process_visit
  - api_external_api_visit_list_video_upload_requests
  - api_external_api_visit_list_findings
---

# Capture to findings

Turn a field video capture into Navigate AI findings for a location. Every request
carries `Authorization: Bearer <api_key>`.

## Steps

1. **Create a visit** — `POST /v1/visits` (`api_external_api_visit_create_visit`) with
   `location_id` and a `visit_type_slug` (list valid slugs via
   `GET /v1/visit_types`). This visit will accept one or more video uploads.
2. **Start a video upload** — `POST /v1/video_upload_requests`
   (`api_external_api_video_upload_request_create_video_upload_request`) with the
   `visit_id`, `file_name`, and `file_size_bytes`. The response returns an upload URL.
3. **Upload the bytes**, then **confirm** — `POST /v1/video_upload_requests/{upload_id}/confirm`
   (`api_external_api_video_upload_request_confirm_video_upload_request`) to mark the
   upload complete. Use `GET /v1/video_upload_requests/{upload_id}` to check status.
4. **Process the visit** — `POST /v1/visits/{visit_id}/process`
   (`api_external_api_visit_process_visit`) to run AI capture/analysis over the uploads.
5. **Read findings** — `GET /v1/visits/{visit_id}/findings`
   (`api_external_api_visit_list_findings`, Beta). Optionally list the uploads with
   `GET /v1/visits/{visit_id}/video_upload_requests`.

## Rules

- Cursor pagination: pass `limit` and `cursor`, follow `next_cursor` until null.
- Tag your records with `reference_key` on creates to correlate with your own system.
- The findings endpoint is Beta; treat its shape as subject to change.
- To abandon a visit or upload, use `.../cancel` (`process_visit` is not reversible once complete).
