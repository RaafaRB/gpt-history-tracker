PROJECT BACKLOG

This file records planned work and schema items for the Pais de Planta project.
It is a decision and memory artifact, NOT production code.

1
. CURRENT HITs IMPLEMENTED
- HIT_000: Bootstrap and governance baseline
- HIT_001: Data modeling (User, Plant, GardenPlant)


2. PLANNED DATABASE ENTITIES (NOT IMPLEMENTED YET)
- PlantHealthAnalysis
- PlantDisease
- DiseaseTreatment
- PlantEvent (watering, pruning, etc)


3. PLANNED RELATIONSHIPS
User -1> GardenPlant
Plant -1> GardenPlant
GardenPlant -1> PlantHealthAnalysis
Plant -N-N> PlantDisease
PlantHealthAnalysis -N-N> PlantDisease


4. PRODUCT FEATURE BACLOG
- Image upload and storage
- AI plant identification
- Plant health analysis
- User garden history
- Basic user profile
- Database migration to PostgreSQL

