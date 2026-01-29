HIT_003 - Plant Identification (OpenAI Vision API
Project: Pais de Planta
HIT: 003
Date: 28-29/01/2026

OBJECTIVE
Implement automatic plant identification using OpenAI Vision API, based on uploaded images, creating or reusing canonical Plant records and linking them to PlantImage.

----
DEBRIEFING SUMMARY

- Manual intervention was required during testing to remove temporary test files and SQLite .db artifacts created by agent tests.
- The Pilot interrupted agent execution when the agent struggled to reset the database and perform end-to-end HTTP image tests, and assumed validation via Swagger UI.
- These interventions were operational cleanup and validation actions, not architectural corrections.
- No functional duplication or code dead code were detected in production code.
- No scope extrapolation or architectural hallucination occurred.

---
TECHNICAL SUMMARY

Core Changes Implemented
- Added python-dotenv dependency and configured .env loading at backend startup.
 - OPENAI_API_KEY loaded once and accessed via os.getenv(...)
- Created POST /ai/identify-plant endpoint.
- Implemented direct HTTP call to OpenAI Vision API (no SDR).
 - Parsed full plant data set mapping directly to Plant table columns.
- Deduplication enforced by scientific_name.
- Updated PlantImage.plant_id to link physical image to canonical plant record.

OpenAI Integration Details
- Model: gpt-4o (Vision)
- Method: HTTP POST /v1/chat/completions
- Images transmitted as base64 data URLs
- Prompt forces strict JSON output mapping to Plant schema
- Error handling for HTTP errors, Network errors, and JSON decoding

Database State
- Plants created on-demand when absent
- PlantImage.plant_id column added (nullable)
- Atomic transactions using db.flush() and commit()

Validation
- End-to-end manual testing via Swagger UI successfully completed
- Confirmed reuse of existing Plant records on repeated calls
- No server warnings or runtime errors detected

---
NEW RULES FOR THE SHIP

1. Agents must stop automated database resets and binary tests when operational friction is detected and defer to Plot intervention.
2. Image ID pipelines are considered auditable system inputs and must be persisted prior to IE
3. OpenAI calls must be fully deterministic in pipeline behavior and not embedded in data ingestion

---
CONCLUSION

HIT_003 completed successfully. The system now supports full end-to-end image-based plant identification with canonical data persistence, ready for garden and plant-health HITs.