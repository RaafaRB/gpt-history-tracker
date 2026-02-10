Header: HIT_021 - Decision Record

This document concludes the semantic definition of treatments in the Pais de Planta product. It explicitly defines that a treatment is a friendly care suggestion, rather than a medical authority or a user duty. Treatments exist to help users remember what they tried and to encourage a follow-up observation of plant responses.

Core decisions confirmed in this record include the clear separation between canonical treatments (DiseaseTreatment) and garden-specific treatment shots (GardenPlantTreatment). Canonical treatments are immutable, catalog-level entities created by the AI and reused across diagnostics. Garden plant treatments are created as snapshot clones of the canonical treatment at diagnostic time, and never re-synchronize with the catalog.

The record also confirms that treatment status is 100% derived from flow events, rather than user opinion. The status always initializes as pending, transitions to waiting_for_update when the user declares that the suggestion was tried, and completes as done only after a new plant photo is submitted. Status does not represent success or failure, only the current place in the care cycle.

Finally, this decision record affirms that diagnostics and treatments are immutable historical events. Improvements to the catalog or treatment knowledge only apply to future diagnostics, ensuring transparency, trust, and longitudinal consistency for users.