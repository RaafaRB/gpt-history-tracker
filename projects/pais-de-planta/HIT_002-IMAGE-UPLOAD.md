HIT_002 - Image Upload Infrastructure (DEBRIEFING)

Project: Pais de Planta
HIT: 002
Objective: Implement the infrastructure for image upload without ID, IA, or product logic.

---
DEBRIFING SUMMARY

- Minor friction occurred during manual testing because image upload is hard to perform from terminal-only environments.
- The agent correctly recognized this limitation and explicitly requested permission to apply a bug fix outside the original checklist.
- Permission was granted by the Pilot, and the correction was applied immediately and validated.
- No scope drift occurred; the fix was strictly limited to infrastructure correctness.

---
TECHNICAL SUMMARY


Since Last Summary

- PlantImage model added to backend/models.py
 * Fields: id,  user_id, garden_plant_id, file_path, original_filename, mime_type, size_bytes, created_at
- Uploads infrastructure created: backend/uploads/ with .gitkeep
- .gitignore configured to exclude uploaded files
- UPLOADS_DIR definition and idempotent directory creation added to main.py

----
EPENDPOINT

FILE: backend/routers/images.py
ENDPOINT: POST /images/upload

Request:
multipart/form-data
- file (required)
- user_id (required)
- garden_plant_id (optional)

Validations:
- MIME type must be image/*
- Max file size: 10 MB

Response:
{ "image_id": 1, "file_path": ".../uploads/<uuid>.jpg" }

---
BUG FIX

- Issue: FileNotFoundError due to incorrect uploads path resolution from within router module
- Resolution: Changed UQLOAD_DIR from Path._parent to Path._parent.parent
- Validated with successful upload


---
VALIDATION

- HTTP 200 response confirmed
- File saved to backend/uploads/ with UUID filename
- PlantImage record created in the database
- No SQLAlchemy warnings or server errors

---
CONCLUSION

HIT_002 completed successfully with controlled agent decision-making and a clear infrastructure boundary. The system now has a reliable, BP-friendly image ingestion pipeline ready for ID stages.

New Rules for the Ship

1. Image upload endpoints may modify infrastructure only if explicitly approved by the Pilot.
2. Terminal-only validation of file flows is allowed when no UID exists.
