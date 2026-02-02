HIT_006 - Garden Plants Backend + Real Image Display

Project: Pais de Planta
HIT: 006
Objective:
Implement backend support for saving plants in the user garden and display real images uploaded by users.

DEBRIFING SUMMARY

- Implemented a new backend endpoint for creating garden plant records, without adding new UI screens.
- Extended existing plant detail endpoint to return associated image data.
- Configured static file serving for user-uploaded images.
- No scope drift, no new features beyond the approved objective.

TECHNICAL SUMMARY

Backend Changes
- Created POST /garden-plants endpoint to save plants to the user garden.
- Validates existence of User and Plant records.
- Writes new records to garden_plants table via SQLAlchemy ORM.
- Extended GET /plants/{id} response with image_url field.
- Queries plant_images table for most recent image.
- Provides null fallback when no image exists.

Infrastructure
- Configured FastAPI StaticFiles to serve backend/uploads/ at /uploads/.

FRONTEND CHANGES
- Extended PlantDetail page to display real user-uploaded images.
- Replaced placeholder images with real backend image URL.
- Implemented fallback to placeholder when image_url is null.

VALIDATION

- Manual validation of image upload, identification, and display flow.
- End-to-end flow confirmed via Swagger UI and frontend manual testing.
- No server warnings or runtime errors.

WHAT WAS NOT CHANGED
- No new garden UI screens were created.
- No authentication or user flow changes.
- No end-to-end automated tests.
- No changes to unrelated tables or features.

NEW RULES FOR THE SHIP
1. Backend endpoints may be prepared for future UI features without forcing frontend implementation.

2. Real user-uploaded media should be surfaced in existing read endpoints when semantically appropriate.

CONCLUSION
HIT_006 completed successfully. The system now supports persisting plants in the user garden and displaying real user-supplied images end-to-end, without expanding MVP scope.