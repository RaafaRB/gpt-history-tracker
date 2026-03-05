# All HITs — Pais de Planta

This file consolidates all HIT records for the Pais de Planta project in chronological order.

---

## HIT_000-BASELINE.md

HIT_000 - Initialization and Bootstrap of the Pais de Planta Project

Project: Pais de Planta
Date: 27 January 2026
Duration: approximately 3 hours
HIT Type: Foundational / Technical Bootstrap
Objective: Full initialization of the project with governance, full-stack scaffolding and cross-platform automation, establishing a stable technical baseline for future HITs.

1. Context and Objective of the HIT
This HIT had the goal of creating the fundational technical and governance layers of the Pais de Planta project, before any product feature development.

The focus of this HIT was not to deliver features, but to:
- Build a solid and scalable structure
- Avoid common pitfalls of IA-first projects
- Ensure full Windows compatibility
- Define explicit rules for future AI operation
- Validate the frontend - backend loop from day one

This HIT is treated as an immutable baseline on top of which all future HITs will be built.

6. Final Status and Validation
Backend and frontend are operational.
Governance is active.
The technical baseline is congeled for future HITs.
---

## HIT_001-DATA-MODELS.md

HIT_001 - Data Modeling (MVP)

Project: Pais de Planta
HIT: 001
Objective: Define the core SQLAlchemy ORM models for the MVP.

Execution Summary
This HIT implemented the minimal data schema required to support the Pais de Planta MVP. The focus was on vocabulary freezing, domain clarity, and preparation for future features.

Models Created
User - Users table
- id (PK)
- created_at
- last_active_at (nullable)
- locale (nullable)
- notes (nullable)

Plant - Plants table
- id (PJ)
- scientific_name (unique, non-null)
- popular_names
- family
- description
- curiosities
- light
- watering
- humidity
- ideal_temperature
- soil
- fertilization
- flowering
- seasonality
- growth
- toxic_to_pets
- toxicity_notes
- source
- confidence_level
- created_at
- updated_at

GardenPlant - Garden_plants table
- id (PJ)
- user_id (FK To User)
- plant_id (FK To Plant)
- nickname
- location
- sun_exposure
- last_watering
- last_pruning
- watering_notes
- registered_at
- created_at
- updated_at

Relationships
- User 1:N GardenPlant
- Plant 1:N GardenPlant
- Symmetric back_populates configured

Validation
Tbles created successfully without SQLAlchemy warnings or errors.
No manual corrections.
No hallucinations.

Conclusion
THIT_001 successfully completed. Data vocabulary is now congeled and ready for subsequent HITs.
---

## HIT_002-IMAGE-UPLOAD.md

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

---

## HIT_003-PLANT-IDENTIFICATION.md

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
---

## HIT_004-FRONTEND-UX-MVP.md

HIT_004 - Frontend UX MVP
Project: Pais de Planta
HIT: 004
Date: 29/01/2026

OBJECTIVE
Create a figurative, navigatable, and coherent Frontend UX MVP based on prototype references, prioritizing visual flow and product story over full feature implementation.

----
DEBRIEFING SUMMARY

- The HRT_004 executed smeoothly without operational friction.
 - No manual corrections outside the checklist were required.
- No duplicated functions, dead code, or temporary files were detected.
- No scope extrapolation or backend integration was attempted.
- The abelhas correctly flagged that visual differences remain due to missing Material Symbols and Tailwind custom colors configuration.

----
TECHNICAL SUMMARY

Frontend Changes Implemented
- Installed and configured React Router (react-router-dom)
- Created cree page structure with routes: /
  * /
  * /analyzing
  * /plant/:id
- Implemented 3 core MVP screens:
  * Home Page
  * Analyzing Page
  * Plant Detail Page
- Converted prototype.html structure into React TXX, ad!xering Tailwind utility classes and visual hierarchy
- Added navigation flow:
  * Home → Analyzing
  * Analyzing → Plant Detail (simulated via setTimeout)
- Used local mock data for Plant Detail (no backend calls)
- All STA logic kept local to components

Validation
- All 3 screens render correctly in dev server
- Navigation flow validated end-to-end
- No console warnings or errors
- Build remains clean

---
NEW RULES FOR THE SHIP

1. Prototype HTML files in references/ are design references, not source code.
2. FRONTEND HTTs must validate visual flow before backend integration.
3. Icon fonts and Tilwind custom themes are deliberately deferred to dedicated UX HITs.

---
CONCLUSION

HIT_004 completed successfully. The application now has a visually coherent and navigatable Frontend MVP that clearly communicates the product story and prepares the ground for HRT_005 frontend-backend integration.
---

## HIT_004.md

# HIT_004 - UX + Front衙Back Integração

Sumary:

- Infraestrutura visual concleta (Tailwind 4, fonts, icones)
- Fluxo end-to-end funcional: Upload ↓ Analyzing → AI ↓ PlantDetail
- Backend: GET /plants/{id} + seed idempotente de User(id=1)
- Frontend: remoção de mocks e fetch real de dados

Auditoria:

- Correção manual: appenas limpeza de arquivos de teste
- Sem duplicação
- Serm hallucination
Notas: 

- Detectado debito de dupla chamada HTTP no front (Analyzing e PlantDetail), planejado para próximo HIT

New Rules:

- Testes com imagens devem ser manuais
- Evitar effects de duplla execução
- CONTROL ROOM em caso de drift
---

## HIT_005.md

# HIT_005 - Guards de StrictMode + Localização pt-BR
Sumary:

- Resolução de chamadas dupladas em Analyzing.tsx usando guard com useRef
- Resolução de chamadas dupladas em PlantDetail.tsx com guard baseado em plant_id
- Ajuste do prompt da OpenAI Vision para forcar pt-BR e nomes populares doBrasil

Auditoria:

- Sem atrito
- Sem correação manual
- Sem testes

New Rules:

- Evitar effects duplos em React StrictMode com guard explícito
- Localização cultural de retorno da IA deve ser contrato de prompt
---

## HIT_006-GARDEN-PLANTS-AND-IMAGES.md

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
---

## HIT_007-GARDEN-UX-USER-PLANT-DETAIL.md

HIT_007 - Garden UX + User Plant Detail

Project: Pais de Planta
HIT: 007

Objective
Implement full Garden UX and user-specific plant detail, connecting the canonical plant flow to the user garden.

Execution Summary

THIT_007 delivered a complete end-to-end garden experience without expanding scope. Two backend GET endpoints were added to expose Garden Plant data, and the frontend was extended with three new surfaces: a Save to Garden action on the canonical PlantDetail, the My Garden listing, and a User Plant Detail page. The implementation used existing models and image infrastructure, and was guided by a Stitch-generated HTML prototype as a visual reference only.

Quality Audit

During validation, minor architectural corrections were required to improve controller cohesion and avoid namespace conflicts. No functional defects, duplicated logic, or hallucinations were detected in the final state. The root cause was identified as an execution-phrasing issue: a single overly-broad prompt increased initial code density, which was resolved through human coordination.

New Rules for the Ship

1. Multi-surface HITs must be executed with granular, step-level prompts to preserve code cohesion.
2. Prototype HTML files in references are visual guides, not source code.

Conclusion

This HIT_007 successfully established the garden as a first-class user domain concept, creating the correct semantic foundation for future diagnostic and care features.
---

## HIT_008-PLANT-HEALTH-DIAGNOSIS.md

HIT_008 - Plant Health Diagnosis

Project: Pais de Planta
HIT: 008

Objective
Implement a canonical, on-demand plant health diagnosis for user-specific GardenPlant entities, using a strictly controlled IA response model without persistence.

Execution Summary

HIT_008 successfully delivered an end-to-end diagnostic flow that is aligned with the domain model and product semantics. A canonical diagnostic model was formally defined, documented, and enforced across the prompt, backend, and frontend. The backend exposed a new POST /ai/diagnose-garden-plant endpoint that performs an onDemand OpenAI call, validates the response using a Pydantic schema, and returns the result without transformation or storage. The frontend integrated the endpoint into the GardenPlantDetail view, rendering the diagnosic output directly and preserving full semantic fidelity.

Audit and Quality Assessment

No manual corrections were required during the HIT_008 execution. No duplicated functions, dead code, or architectural drift were detected in the final state. The IA integration remained deterministic and followed the approved canonical diagnosic model without hallucination or scope expansion.

New Rules for the Ship

1. Semantic models must be explicitly defined and enforced before Iading any IA powered feature.
2. On-demand IA outputs should not be persisted until their domain meaning, lifecycle, and downstream uses are stable.

Summary

HIT_008 established a high-quality, domain-correct foundation for plant health diagnosis, ready to be extended into persisted events and historical analytics in future HITs.
---

## HIT_009-PERSIST-DIAGNOSTIC-EVENTS.md

HIT_009 - Persist Diagnostic Events

Project: Pais de Planta
HIT: 009

Objective
Introduce persistence of plant health diagnostics as immutable events associated with user-specific GardenPlant entities, without altering the canonical diagnostic contract or UX.

Execution Summary

HIT_009 extended the existing POST /ai/diagnose-garden-plant endpoint to persist every successful diagnostic response as a historical event. A database schema for garden_plant_diagnostics was defined exactly to mirror the canonical diagnostic model, and implemented using a manual, reversible migration approach compatible with the project's existing SQLAlchemy initialization strategy. An ORM model for GardenPlantDiagnostic was added, and the endpoint was modified to persist the diagnostic event only after successful Pydantic validation, ensuring atomicity and contract stability.

Quality Audit

No manual corrections were required during HIT_009. No duplicated functions, dead code, or scope drift were detected in the final state. All tests confirmed that each diagnostic call creates exactly one new database record with a unique created_at timestamp, preserving the full semantic content of the canonical diagnostic response.

New Rules for the Ship


1. Persistence of IAAoutputs must be event-based and immutable.
2. Existing endpoint contracts should be extended, not replaced, when adding persistence behavior.

Conclusion

HIT_009 successfully created a reliable, domain-correct foundation for diagnostic history, enabling future timeline, analytics, and knowledge base features without introducing technical debt.
---

## HIT_010-DISEASE-CATALOG-ISSUE-CODE.md

HIT_010 - Disease Catalog and Issue Code Integration

Project: Pais de Planta
HIT: 010

Objective
Expand the plant health diagnostic system with a canonical issue identifier and a normalized knowledge base, enabling deduplication, analytics, and future UX evolution without breaking existing contracts.

Execution Summary

HIT_010 introduced a canonical issue_code into the diagnostic contract, extending the OpenAI system prompt, Pydantic validation, and backend persistence flow in a coordinated manner. A backend-first disease catalog was implemented via new global entities (PlantDisease and DiseaseTreatment), and the diagostic event persistence capability from HIT_009 was evolved to associate each diagnostic with a catalog entry via a nullable foreign key.

A controlled migration strategy was employed to add issue_code and plant_disease_id to garden_plant_diagostics, while introducing normalized catalog tables with deduplication enforced by unique codes. The endpoint was extended to resolve or create catalog entries at runtime, maintaining full atomicity and precerving the original JSON response contract.

Quality Audit

No manual corrections, duplicated logic, or scope deviations were detected in the final state. The integration maintained consistent semantics across prompt, validation, persistence, and catalog resolution. Testing confirmed that catalog entries are created exactly once per issue_code and reused across multiple diagnostic events.

New Rules for the Ship

1. Canonical issue identifiers should be introduced before UK optimization.
2. Foreign keys may be nullable initially to preserve domain evolution, but should be enforced early to avoid semantic drift.
3. BACKEND-first catalog implementations should remain decoupled from UK concerns.

Conclusion

RIT_010 successfully established a normalized, extensible foundation for plant health Knowledge, allowing future UB layers, analytics, and catalog expansion to be built on a stable and integrity-enforced core.
---

## HIT_011-UX-DIAGNOSTIC-FLOW.md

HT_011 UX Diagnostic Flow Debriefing

HIT_011 focused on defining and implementing the full UX flow for diagnostics, diseases, and treatments without any backend integration. The core objective was to translate the stub_mp_development_v0.3 HTML stubs into mocked, fully navigable React pages that clearly communicate the cognitive flow for the user. This HIT covered multiple surfaces of the application but strictly avoided touching any domain logic, APIs, schemas, or persistence.

Major deliverables included a significant update to GardenPlantDetail to serve as the central hub of the flow. This screen now displays the latest diagnostic in distinct priority, provides multiple entry points into a glossary modal, and exposes a clear call to action to explore the full history. The glossary modal itself was implemented as a single, reusable component that can be opened from both explicit buttons and implicit icon interactions, aligned with the UX stubs.

The HIT_expanded flow also introduced three new mocked pages: a DiagnosticsHistory list view, a DiseaseDetail view, and a TreatmentDetail view. These pages are fully navigable and share a consistent card visual language, but all data is hardcoded and intentionally mocked. Empty states were explicitly implemented both in the GardenPlantDetail and in the diagnostic history view, ensuring UX robustness even in the absence of data.

A notable observation during this HIT was the addition of an external frontend library for icons. While the library is used only for presentational purposes in this HIT, it introduced a new dependency that must be monitored closely. Future HITs should enforce explicit verification of licensing, copyright, and long-term maintenance implications of any new dependencies added by agents. This was recorded as a new Ship rule to prevent unobserved licensing or compliance risks in future work.
---

## HIT_012-000.md

HIT_012 - Modelacao Canonica Total

ESTE HAT foi deliberadamente definido como um HIT de planejamento e modelacao, com o objetivo de estabelecer o estado-meta do backend do projeto Pais de Planta. Nenhuma implementacao funcional foi realizada, e todo o escopo foi rigidamente respeitado, sem endpoints, IAs, frontend ou migrations.

Durante o HIT_012, foram criados enums canonicos centralizados e modelos SQLAlchemy para todos os dominios creticos do sistema: plants, garden_plants, garden_plant_diagnostics, plant_diseases e disease_treatments. As entidades foram definidas com separacao clara entre texto explicativo e estado estruturado, enums estabelecidos e sem qualquer inferencia implicita.

Nao houve atrito operacional, correcoes manuais, duplicacao de logica ou deriva arquitetural. O conflito entre nomeacao singular e plural de modelos foi identificado e declasifecado como fora de escopo deste HAT, ser abordado em HITs futuros.

Enquanto resultado, o projeto passa a possuir uma base canonica estabel, determinista e completamente preparada para a evolucao incremental nos proximos HITs, specialmente Ia, eventos e exposicao de contratos via endpoints.
---

## HIT_013-000.md

HIT_013 - Manutencao Estrutural

Este HAT foi executado como um passo consciente de manutencao estrutural, com o objetivo de eliminar conflitos introduzidos no estado canonico definido no HIT_012. O HIT_013 nao ten como meta restaurar funcionalidade, mas andar deliberadamente em direcao a clareza estrutural e contratual.

Derante o HIT_013, os agentes identificaram o stado canonico como referencia unica de verdade e removeram archivos que conflitavam com essa estrutura, incluindo modelos duplicados e arquivos monoliticos. Em seguida, schemas Pydantic legados foram destruidos e todos os schemas foram recriados a partir exclusivamente dos modelos ORM canonicos.

Conforme planejado, o projeto encontra-se deliberadamente com quebras funcionais em routers e outros modulos, o que foi aceito como criterio de sucess deste HIT. O resultado e uma base estrutural limpa, coerente e alinhada com o estado canonico, preparada para um JIT_014 de correcao de regra de negocio e reconstrucao operacional.
---

## HIT_014-000.md

HIT_014 - Reconstrucao Funcional Controlada

Este HAT completou a reconstrucao funcional do projeto apos a reestruturacao canonica realizada nos HIT_012 e HIT_013. O objetivo fori restaurar a compilacao e o funcionamento basico da API, mantendo fidelidade aos modelos canonicos e aos contratos de dados.

Durante o HIT_014, o canonico foi completado com campos de produto que habiam sido omitidos, modelos faltantes foram reintroduzidos (ex: PlantImage) e relationships ORM pontuais foram readicionados onde o dominio e uso previo ja os exigiam. Em paralelo, schemas PyDantic de validacao da IA foram criados como contratos cognitivos, garantindo que os modelos gerados representam todo o dominio de negocio, excluindo apenas campos tecnicos.

Finalmente, contratos de request e response foram recreados sob a strategia de transparencia total, expondo todo o estado da base sem otimizacao prematura. Os routers foram reconectados ao novo estado, corrigindo imports, campos e fluxos, e preparando o projeto para validacao real e iterativa em HITs futuros.
---

## HIT_015-000.md

HIT_015 - Alinhamento Cognitivo de Prompts IA

Este HIT foi executado com o objetivo exclusivo de alinhar os prompts de IA ao estado canonico atual do projeto, apos a restruturacao de modelos e schemas realizada nos HIT_012 a 14. O escopo foi restrito a atualizacao do Texto de prompt, sem alterar logica de negocio, modelos, schemas ou fluxos de routers.

Durante o HIT_015, os prompts de Identificacao e Diagnostico foram reescritos e tornados explicitos, deterministas e alignados aos schemas canonicos AIPlantIdentification e AIPlantDiagnostic. A IA passa a ser obrigada a gerar todos os campos de dominio, usando null explicitamente quando nao houver informacao, e respeitando enums e contratos semanticos.

Conforme planejado, o backend atual persiste apenas o subset de campos ja mapeados, no entanto a parte cognitiva do sistema (lado IA) ja esta preparada para consumir e produzir o estado de dominio completo nos HITs futuros.
---

## HIT_016-000.md

HIT_016 - Organizacao Estrutural (routers → Services, Prompts → AI/)

Este HIT foi executado com o objetivo de reduzir acoplamento e melhorar a legibilidade do codigo sem alterar comportament funcional. O escopo foi restrito a movimento de codigo existente, sem refactoracao semantica ou criacao de features.

Durante o HIT_016, foi criada a camada backend/services/, e toda a regra de negocio foi extraida dos routers e copiada literalmente para services dedicados. Os routers foram reduzidos a controladores finos, responsaveis somente por interacao de request/response e encaminhamento para os services.

Paralelamente, os prompts de IA foram extraidos do codigo embedido e organizados em arquivos dedicados em backend/ai/prompts/, com versionamento explicito (_v1). O conteudo foi copiado literalmente, ser alteracao semantica, e os services passaram a carregar-os a partir desses archivos.

O resultado e uma base estrutural mais clarifecada: routers bobalhos, services con responsabilidade clara, e prompts de IA tratados como dados versionaveis. Esta reorganizacao prepara o projeto para hits de observabilidade, pruning e evolucao baseada em uso real.
---

## HIT_017-000.md

HIT_017 - Observabilidade Minima da IA

Este HIT foi executado com o objetivo de introduzir observabilidade minima da IA, sem alterar fluxo, comportamento ou contratos. Aintementacao foi definida como descartavel e instrumental, serving como base objetiva para analise de efetividade do dominio gerado pela IA.

Durante o HIT_017, foram adacionados logs estruturados nos services de IA e de persistencia, com geracao de um request_id unico por operacao. O payload bruto retornado pela IA foi registrado antes da persistencia, e o payload efetivament persistido foi logado com o mesmo request_id, permitindo comparacao direta em camadas externas.

Um problema de alcance foi identificado e corrigido: um log de persistencia estava invalida dee de return em um fluxo especifico. A reorganizacao garantiu que todos os logs sejam atingetivos sem interferencia na execucao.

Nao foram introduzidas dependencias externas, nao foram alterados retornos, e nao houve modificacao de regra de negocio. O resultado e como previsto: oprojeto passa to dispor de visibilidade basica embasada em dados reais, preparando o terreno para o HIT_018 de pruning orientado por uso real.
---

## HIT_018-000.md

HIT_018 - Conexao Real Backend ↓ Frontend (Desmocking Controlado)

Este HAT completou a substituicao dos mocks do frontend por dados reais do backend, concluindo a conexao end-to-end do sistema. O objetivo fori espelhar o dominio existente nas interfaces, sem alterar UX, layout ou regras fisuais, permitindo visualizar o estado real do projeto.

Durante o HIT_018, foram identificadas telas totalmente mockadas e uma hibrida, em que os datos foram substituidos por chamadas reais a novos endpoints GET Minimais no backend. Endpoints foram criados apenas quando nao existiam equivalentes, sempre com foco em espelhar os datos ja presentes no dominio, sem criar comportament novo.

No frontend, as telas DiagnosticsHistory, DiseaseDetail, TreatmentDetail e o card de “Útimo Diagnosticoâ em GardenPlantDetail foram conectados ao backend, com mocks removidos e dados reais expostos. O build do frontend (tsc + vite) foi validado com sucesso.

O resultado e a conclusao do ciclo de desenvolvimento: a aplicacao passa a exibir dados reais, as superficies de consumo sao espelhasas, e os problemas, excessos e contratos initinados tornam-se visídeis. Este estado prepara o terreno para HITs futuros de pruning, correcao de dominio e iteracao orientada a uso real.
---

## HIT_019-000.md

HIT_019 - Correcao de Persistencia de Campos Canonicos

Este HIT foi dedicado a investigar e solucionar a causa real dos campos canonicos que estavam sendo persistidos como NULL no banco de dados, apos a conexao real do frontend no HIT_018. O foco foi estritamente no backend, em particular na camada de services.

A analise detalhada demonstrou que os 15 campos enum do modelo Plant estavam corretamente gerados pela IA, validados pelos schemas Pydantic, contudo eram completamente ignorados no momento da criacao do objeto ORM,devido a um mapeamento manual incompleto no identify_plant_service.

Uma correcao canonica foi aplicada: o fluxo de identificacao de planta foi alinhado ao mesmo padrao do fluxo de diagnostico, passando a utilizar validacao com Pydantic e design baseado em model_dump() para construcao do objeto ORM correspondente. Com issoe, dos campos enum passaram a ser persistidos corretamente em novos registros.

Derante a verificacao manual, foi confirmado que o modelo Plant agora salva os campos canonicos como esperado. Outros campos que permaneceram NULL (tratamentos, catalogo de doencas, display_title) nao representaram erros de persistencia, mas sim dominios ainda nao implementados ou contratos de produto ainda definidos.

O HIT_019 foi encerrado com o problema raiz resolvido, com a cadeia IA - Service - ORM restaurada e simetrica. Os insights revelados servirao de base para HITs futuros de enriquecimento de dominio e definicao de regras de preenchimento.
---

## HIT_020-000.md

HIT_020 - Diagnostic AI as Single Source of Truth

This HIT consolidated the diagnostic AI as the single source of truth for disease and treatment domains. The scope was to expand the diagnostic contract to generate and persist all critical model fields, eliminating silent NULL states and turning diagnostics into a complete domain.

The Pydantic diagnostic schema was expanded to require fields such as display_title, severity, progression_speed, symptoms, causes, and an ordered list of treatments. Validators were added to prevent partial, shallow, or inconsistent responses, including non empty list checks, sequential treatment order validation, and non generic descriptions.

On the backend, the diagnostic service now uses the validated AIPlantDiagnostic payload as the single source of truth, fully persisting PlantDisease data (severity, progression_speed, symptoms, causes, description) and DiseaseTreatment data (step_order, action, materials, estimated_days). No additional AI calls were introduced and no silent defaults are used.

During verification, it was confirmed that healthy diagnostics do not create diseases, PlantDisease records are not duplicated by issue_code, and AI validation errors fail loudly and block persistence. It was observed that DiseaseTreatment records may be created more than once for the same disease, a behavior explicitly accepted in this HIT pending a future decision on versioning or deduplication.

To maintain response compatibility, JSON array deserialization was added at the Pydantic boundary without altering the database structure.

At the end of HIT_020, all critical fields are guaranteed for new diagnostics. The diagnostic domain is now complete and ready to be consumed by a frontend based on real data.
---

## HIT_021-DECISION.md

Header: HIT_021 - Decision Record

This document concludes the semantic definition of treatments in the Pais de Planta product. It explicitly defines that a treatment is a friendly care suggestion, rather than a medical authority or a user duty. Treatments exist to help users remember what they tried and to encourage a follow-up observation of plant responses.

Core decisions confirmed in this record include the clear separation between canonical treatments (DiseaseTreatment) and garden-specific treatment shots (GardenPlantTreatment). Canonical treatments are immutable, catalog-level entities created by the AI and reused across diagnostics. Garden plant treatments are created as snapshot clones of the canonical treatment at diagnostic time, and never re-synchronize with the catalog.

The record also confirms that treatment status is 100% derived from flow events, rather than user opinion. The status always initializes as pending, transitions to waiting_for_update when the user declares that the suggestion was tried, and completes as done only after a new plant photo is submitted. Status does not represent success or failure, only the current place in the care cycle.

Finally, this decision record affirms that diagnostics and treatments are immutable historical events. Improvements to the catalog or treatment knowledge only apply to future diagnostics, ensuring transparency, trust, and longitudinal consistency for users.
---

## HIT_021.md

This HIT completed the translation of treatment semantics into a fully operational backend and connected frontend flow. The core decision that treatments are friendly suggestions, rather than authoritative prescriptions, was enforced across the architecture.

Anew GardenPlantTreatment entity was introduced as a snapshot of the canonical DiseaseTreatment at diagnostic time, ensuring historical immutability and rastrability. The diagnostic flow was updated to resolve and reuse canonical treatments while creating exactly one treatment snapshot per diagnostic. A read-only endpoint was added to expose this snapshot and a minimal mutation endpoint was introduced to advance the status from pending to waiting_for_update.

On the frontend, treatment display was refactored to consume the new snapshot data, replacing previously mocked or canonic behavior. Real-time treatment status is now refflected consistently across the TreatmentDetail, GardenPlantDetail, and DiagnosticsHistory surfaces. The result is a coherent, event-driven treatment flow with no domain ambiguity, zero backend breakage, and a clear foundation for future iterations.
---

## HIT_022-EVENTS-NOTIFICATION-INFRA.md

HIT_022 implemented the foundational notification infrastructure for the Pais de Planta application, establishing a multi-user, canal-agnostic architecture based on Firebase Cloud Messaging and APSScheduler. New domain entities (User, UserDevice, Notification) were introduced to support device registration, channel-independent notification orchestration, and persistent scheduling. The architecture strictly separates channel (FirebaseService), orchestration (NotificationService), and trigger (SchedulerService) to prevent domain-infra coupling.

APSScheduler was integrated into the FastAPI lifecycle to periodically process pending notifications based on scheduled_for timestamps, while Firebase Admin SDK was initialized via environment variables for secure credential management. A manual test endpoint was added to validate end-to-end scheduling and status transitions (pending to sent / failed) without frontend integration. This HIT established the structural foundation for proactive care reminders and future event-driven features without introducing architectural drift.
---

## HIT_023-DEVICE-REGISTRATION-AND-PUSH-VALIDATION.md

HIT_023 validated the entire end-to-end push notification channel for the Pais de Planta application. This HIT introduced web device registration, FCM token generation in the browser, backend token persistence via UserDevice, and real notification delivery through Firebase Cloud Messaging and APScheduler. The architecture preserved strict separation of concerns: DeviceService handles registration, FirebaseService handles delivery, NotificationService orchestrates status transitions, and SchedulerService triggers processing. The system was validated end-to-end: token generation, persistence, pending to sent transition, and real browser push receipt were confirmed. This HIT established production grade multi user push infrastructure without introducing architectural drift.

Friction analysis revealed important infrastructure and development environment lessons. Critical issues resolved included securing the SQLite database path to avoid parallel or phantom databases, eliminating incorrect root cause assumptions, correcting Firebase configuration and VAPID key handling, and adapting to service worker compat limitations. Open structural considerations include the absence of Alembic for schema migration and service worker hot reload limitations in browser development. Overall, the push channel is fully validated, extensible, and ready for integration with care events and user value features in subsequent HITs.
---

## HIT_024-CARE-SCHEDULES-AND-PUSH-INTEGRATION.md

HIT_024 - Care Schedules and Push Integration

This HIT introduced the full care schedule domain and integrated it with the existing notification and Firebase FCM infrastructure. Each GardenPlant now creates three care schedules (watering, fertilizing, pruning) with derived intervals based on canonical plant attributes. Schedules generate persistent Notification records linked via reference_type and reference_id, allowing robust traceability and auditability.

Users can mark a care schedule as done, which atomically updates last_done_at, recalculates next_due_at, and creates a new scheduled notification for the next cycle. The scheduler processes due notifications and delivers real web push messages through FCM. This HIT completes the proactive care loop from plant understanding to user action and regenerative scheduling.
---

## HIT_025-CARE-TASKS-UX-POLISH.md

HIT_025 - Care Tasks UX Polish

This HIT polished the care tasks UX by unifying the care schedule representation into a single canonical surface. The duplicate "Cuidados" section was removed, and the existing "TAREFAS" area was connected to real GardenPlantCareSchedule data. The three tasks (watering, pruning, fertilizing) are now rendered in a fixed order and with consistent PT-BR labels (REGA, PODA, ADUBO).

The mocked text and static icons were replaced with dynamic icon selection and a polar ring progress visualization derived from the schedule state. Progress is calculated as elapsed/total interval and clamped to [0,1], supporting a consistent rugged overdue state. The state mapping is explicit and uniform: done when progress < 0.5, attention when 0.5 <= progress < 1.0, and overdue when progress >= 1.0. Clicking the task circle triggers the backend mark-done action and updates the ring and label without page reload.

Glossary integration was preserved and magde explicit: the existing category glossary mechanism remains the only source of truth for explaining levels and task meaning. An info trigger was added to the task card with event isolation to avoid click conflict with the mark-done action. The result is a single, domain-correct, and visually expressive care task section that translates time to due into icons and ring progress rather than mocked text.
---

## HIT_026-CANCELLED-STATUS-FOREGROUND-DEDUPE.md

HIT_026 - Cancelled Status and Foreground Dedupe

This HIT hardened the care notification cycle and improved the in-app experience when push messages arrive while the app tab is active. On the backend, NotificationStatusEnum was extended with a cancelled status to support cycle correctness. The care schedule mark-done flow now cancels any existing pending notifications for the same care_schedule before generating the next cycle notification, preventing false or stale reminders. A defensive guard also prevents duplicate pending notifications for the same schedule and due time, keeping the data model deterministic under race or repeated calls.

For foreground support, FirebaseService now attaches a minimal, string-only data payload to every push, including notification_id, reference_type, reference_id, care_type, title, and body. This enabled a client-side dedupe path without changing background behavior. On the frontend, a listen-only usePushNotifications hook was added to register a single onMessage listener and display a minimal in-app toast (PushToast) when messages arrive with the app in focus. A module-level Set`dedupes by notification_id, preventing double toasts in foreground. Manual validation confirmed cancellation, non-duplication of pending notifications, and exactly one toast per notification when force-due notifications were triggered with the app open.
---

## HIT_027-IMAGES-CANONICALIZATION.md

HIT_027 - Images canonicalization

This HIT unified all plant, disease, and treatment image rendering into a single canonical system on the frontend. A new AppImage base component was introduced to enforce consistent sizing, shape, aspect ratio, and fallback placeholders per variant (plant, disease, treatment). Small wrappers (PlantHero, PlantAvatar, DiseaseThumb, DiseaseHero) were added to standardize common contexts such as garden cards and detail headers.

The app was then refactored to use snapshot-first deterministic image priority for diagnostic-context surfaces: diagnostic event image first (diagnostic.image_url), then canonical disease cover (plant_disease.cover_image_url), then a disease placeholder. This single rule now applies consistently to GardenPlantDetail, the diagnostics history, DiseaseDetail, TreatmentDetail, and healthy-diagnostic surfaces. Ad-hoc backgroundImage and raw img usages were removed for entity images, reducing visual drift and eliminating broken layouts when src values are missing.
---

## HIT_028-UI-POLISH-PLANT-AND-DISEASE-DETAIL.md

HIT_028 - UI polish for Plant and Disease Detail

This HIT polished the current UP presentation by addressing multiple small but high-impact issues across coring screens. The canonical plant detail experience was stabilized by making the display name (apalido ) the primary H1 and demoting the scientific name to a secondary subheader, reducing cognitive noise and improving hierarchy. In the garden plant detail screen, the "Ultima foto ha X dias" line was corrected to derive from the best available source (garden plant image timestamp when present, else fallback to the last diagnostic timestamp only when a snapshot image exists), with local midnight normalization to avoid hourly flicker. The status badge was unstuck from the legacy "Vivo" concept and now displays a clear product semantic state derived from the last diagnostic: "Saudavel" when no disease is attached, and "Doente" when a last diagnostic has a non-null plant_disease_id. As a small affordance polish, the bioattributes settings icon was relocated to the tasks area, replacing a chevron and better aligning future settings actions with task-centric UX. 

On the disease detail screen, the disease name was cleaned to avoid exposing machine-code text (translating underscores and hiphens to spaces and applying Title Case) while honoring a strict priority cascade: diagnostic display_title," plant_disease.name, then humanized plant_disease.code (or issue_code). Debug visibility was removed by eliminating the id/machine-code row and version strings from the view, keeping the UX clean and focused on user-meaningful content. Manual smoke testing confirmed that all touched screens render correctly, no console errors occur, and the frontend build succeeded cleanly.
---

## HIT_029-AUTH-JWT-REFRESH-MULTIUSER.md

HIT_029 - Auth JWT with Refresh and Multi-User Ownership

This HIT implemented full email-and-password authentication with revocable refresh tokens and enforced multi-user ownership across the application. The User model was hardened with a non-null, unique email and a non-null password_hash, enabling secure credential storage without dimsturbing existing relationships (e.g. devices and notifications). JWT utilities were introduced for both access and refresh tokens (including type and expiration claims), and refresh tokens were made revocable via a new RefreshToken persistence model. Refresh tokens are stored as hashed values with jti tracking and expiration/ revocation metadata, supporting rotation and logout revocation without persisting plaintext secrets.

An auth router was added to provide register, login, refresh, me, and logout flows. Login issues an access token and a refresh token, persists the refresh token hash, and returns a standard bearer token response. Refresh validates the refresh token type, enforces revocation/expiration, rotates to a fresh jti, and issues new access and refresh tokens. A central get_current_user dependency was introduced to decode access tokens and fetch the authenticated user, and was plumbed into key routers. Ownership ennforcement was applied to garden plants, care schedules, diagnostics, and device registration, ensuring users cannot enumerate or access resources outside their tenant. The /devices/register endpoint was refactored to derive user_id from the access token and reject input-user_id payloads.

On the frontend, minimal login and register pages were added along with a simple route guard that redirects unauthenticated users to /login. Token management was centralized in a lightweight auth client that stores access and refresh tokens in localStorage, and an apiFetch wrapper was introduced to attach bearer tokens and automatically rotate on 401 responses. Smoke testing confirmed end-to-end session behavior, refresh rotation, session clearup on refresh failure, and multi-user isolation across resources. As part of cleanup, legacy MVP_USER_ID hardcoding was removed in Ai diagnostic flows to ensure all new domain events are attributed to the authenticated user.
---

## HIT_030-API-MIGRATION-AND-AUTH-COMPLIANCE-SWEEP.md

HIT_030 - apiFetch migration and auth compliance sweep

This HIT completed the end-to-end frontend migration to the auth-aware apiFetch wrapper with automatic access token attachment, 401-driven refresh, and single-retry recovery. The frontend base url handling was centralized via VITE_API_BASE_URL, and apiFetch was enhanced to prefix relative paths while avoiding double-prefixing absolute URLs. The core product screens were migrated one by one to use relative paths through apiFetch: MyGarden (garden list), GardenPlantDetail (including care schedule fetch and mark-done), DiagosticsHistory, DiseaseDetail, TreatmentDetail, and AI-driven flows (image upload and analysing/identify path). All hardcoded localhost url dependencies and the legacy Axios client module were removed after a codebase sweep, ensuring that all backend calls route through a single auth path.

A mandatory sweep across the codebase verified that no remaining frontend backend calls use direct fetch, legacy services/api, or hardcoded base URLs. An end-to-end smoke test was run through the auth and core flows: login/register, garden listing, care schedule updates, diagostics navigation, treatment snapshot retrieval, and AI analysis. Refresh behavior was verified by tampering access tokens (auto-refresh recovery) including the failure path when the refresh token is invalid (tokens cleared and redirect to /login). During testing, two backend/protocol mismatches were identified and corrected to keep the auth migration smooth: the garden plant create schema was made user_id optional since it is derived from the token, and the AI diagnose flow was hardened to return a clean 400 validation error when a garden plant lacks a required photo rather than failing with a 500. The outcome is a confident, auth-correct frontend that uses a consistent request path through apiFetch and is ready for the next user-visible features.
---

## HIT_031-PROFILE-LOGOUT-AND-WEB-PUSH-ACTIVATION.md

HIT_031 - Profile, Logout, and Web Push Activation

This HIT turned user management and web push notifications into first-class product behavior for authenticated users. On the backend, a new GET /devices/me endpoint was introduced to expose the current user's registered devices without revealing push_tokens. The response is strictly ownership-filtered by current_user and ordered for recency using last_seen_at with a created_at fallback. This gave the frontend a safe source of truth to display device status for testing and operational observability.

On the frontend, a new /profile screen was added under existing route guards. It fetches /auth/me for user context, exposes a best-effort logout button that calls /auth/logout with the stored refresh token, and always clears local tokens to ensure session termination. The HIT also added a minimal web push activation flow within Profile: it prompts browser permission, obtains an FCM token via getToken using the VAPID key, and registers the device via POST /devices/register under auth (no user_id in the body). After activation, the screen re-fetches /devices/me to show real state immediately. The outcome is a user-visible, auth-correct profile and push onramp that no longer requires devtools or hardcoded test flows.
---

## HIT_033-MOBILE-CAMERA-FIRST-AND-SAFE-AREA.md

HIT_033 - Mobile Camera-First Identify UX and Safe Area Fix

This HIT made the plant identification flow genuinely mobile-first and resolved a core Capacitor usability issue on Android where the app bottom navigation was being overlapped by the system gesture/navbar. On the identify flow, the Capacitor Camera plugin was introduced and synced into the Android project. The frontend was refactored so that all image inputs flow through a single shared entrypoint (useImageSelection) that accepts a File and reuses the existing upload and analyzing routing pipeline. The primary "Identificar Planta" button now opens the native camera on native platforms, converts the returned webPath to a Blob and then a File, and submits it through the same centralized handler. A new small secondary icon button was added to the right to provide the previous behavior as a gallery/file-picker action: on native it opens the photos source via CameraSource.Photos, and on the web it triggers the existing file picker. User cancel is treated as a no-op to avoid noisy errors. On mobile, both camera and gallery paths end in the same analyzing flow without UH regressions.

To make the app usable on devices with system gesture navigation, the bottom app nav was hardened to respect safe area insets. The Layout component was updated to replace rigid bottom padding and height classes with a canonical CSS solution based on env(safe-area-inset-bottom) with a defensive fallback gap. This ensures the bottom nav and its touch targets remain visible and clickable on Android and is ready for iOS safe area behavior.
---

## HIT_034-DELETE-GARDEN-PLANT-CASCADE.md

HIT_034 - Delete GardenPlant with full reverberation (no canonical impact)

This HIT implemented user-owned GardenPlant deletion with strict reverberation across the garden domain while preserving canonical catalog integrity. Database relationships were hardened to support safe deletion by adding ondelete="CASCADE" and SQLAlchemy cascade delete-orphan where required for garden_plant_diagnostics, garden_plant_treatments (including diagnostic_id), garden_plant_care_schedules, and plant_images. No cascade paths were introduced toward canonical tables such as plants, plant_diseases, or disease_treatments.

A delete_garden_plant_service was added to perform an atomic deletion in a single transaction. The service first validates ownership, then gathers all care schedule ids for the plant, deletes all Notification rows referencing those schedules (reference_type="care_schedule"), and finally deletes the GardenPlant row relying on cascades to remove dependent diagnostics, treatment snapshots, schedules, and images. A DELETE /garden-plants/{id} endpoint was added with authentication and ownership enforcement, returning 204 on success and 404 when not owned or missing.

On the frontend, GardenPlantDetail gained an "Excluir planta" destructive action with a minimal confirm modal. On confirmation it calls the DELETE endpoint via apiFetch and redirects back to the garden list, which re-fetches on mount and no longer shows the deleted plant. Smoke testing confirmed that the delete flow works without crashes, removes all associated notifications, leaves canonical data intact, and does not disrupt the scheduler loop.
---

## HIT_UI_01-000.md

HIT_UI_01 - Canonical UI levels and glossary

This HIT standardized the frontend representation of backend enums and domain data. A contextual glossary was added with category-scoped icons and level descriptions, decoupled from business logic. Icons and presentation were organized per category and per level, aligned 1-to-1 with backend enums.

Garden plant bio attributes and tasks now render data from the BFF, using the same visual components and a contextual glossary triggered by user interaction. Legacy behavior was removed, layout was preserved, and mocks were replaced with real data where available. Tasks remain stubs but are visually prepared for future backend events.
---

## HIT_UX_FIX_01.md

This HIT addressed critical UX friction related to healthy diagnostics after real backend data was exposed in the frontend. The scope focused on preventing invalid navigation paths and restoring semantic clarity without touching backend contracts or routes. A celebratory, mobile-first modal was introduced as the canonical surface for healthy diagnostics, replacing legacy disease navigation assumptions.

Defensive guards were added across all entry points to ensure that healthy diagnostics never render disease or treatment surfaces. The result is a fully resilient UX flow: healthy states are explicit, positive, and protected against deep-linking, while non-healthy diagnostics remain unchanged. This HIT significantly improves product readability and user trust with zero architectural debt.
---

