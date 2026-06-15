# Jefferson Health (jefferson-health)

Jefferson Health is a multi-state nonprofit academic health system based in Philadelphia, Pennsylvania, operating more than 30 hospitals and over 700 care sites across eastern Pennsylvania and southern New Jersey as the clinical arm of the broader Jefferson enterprise that also includes Thomas Jefferson University and Jefferson Health Plans (formerly Health Partners Plans). Its patient-facing electronic health record runs on Epic and is branded as MyJeffersonHealth / MyChart, with a CMS-mandated HL7 FHIR R4 API published at fhir.jefferson.edu/FHIRProxy/api/FHIR/R4 that exposes USCDI-aligned clinical resources to third-party patient-access applications via SMART on FHIR and OAuth 2.0. Jefferson Health Plans separately exposes CARIN-aligned Patient Access and Da Vinci Plan-Net Provider Directory FHIR APIs powered by Smile CDR for its insurance members and the public.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/jefferson-health/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/jefferson-health/refs/heads/main/apis.yml)

## Scope

- **Type:** Index
- **Position:** Consumer
- **Access:** 3rd-Party

## Tags

- Academic Medical Center
- CARIN Blue Button
- CMS Interoperability
- Cures Act
- Da Vinci Plan-Net
- Epic
- FHIR
- HL7
- Healthcare
- Hospital System
- MyChart
- OAuth 2.0
- Patient Access
- Provider Directory
- SMART on FHIR
- US Core
- USCDI

## Timestamps

- **Created:** 2026-05-23
- **Modified:** 2026-05-23

## APIs

### Thomas Jefferson University Hospital FHIR R4 API

The Thomas Jefferson University Hospital FHIR R4 API is Jefferson Health's CMS Interoperability and Patient Access (CMS-9115-F) compliant HL7 FHIR Release 4.0.1 endpoint powered by Epic's August 2025 release. It exposes 80+ USCDI-aligned resource types — including Patient, Observation, Condition, Encounter, MedicationRequest, AllergyIntolerance, DiagnosticReport, DocumentReference, and Procedure — to third-party patient-access applications using SMART on FHIR and OAuth 2.0 with standalone-patient and EHR launch contexts, supports US Core 6.1.0 profiles, returns XML or JSON, and offers Bulk FHIR Group export for population-level access.

- **Human URL:** [https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4](https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4)
- **Base URL:** `https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4`

#### Tags

- CMS Interoperability
- Cures Act
- Epic
- FHIR
- HL7
- Patient Access
- R4
- SMART on FHIR
- US Core
- USCDI

#### Properties

- [Capability Statement](https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4/metadata)
- [Smart Configuration](https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4/.well-known/smart-configuration)
- [Authorization](https://fhir.jefferson.edu/FHIRProxy/oauth2/authorize)
- [Token](https://fhir.jefferson.edu/FHIRProxy/oauth2/token)
- [Documentation](https://fhir.epic.com/Documentation)
- [App Registration](https://fhir.epic.com/Developer/Apps)
- [Developer Portal](https://fhir.epic.com/)
- [Endpoint Directory](https://open.epic.com/Endpoints/R4)
- [Implementation Guide](https://hl7.org/fhir/us/core/STU6.1/)
- [Implementation Guide](https://hl7.org/fhir/smart-app-launch/)
- [Implementation Guide](https://hl7.org/fhir/uv/bulkdata/)
- [OpenAPI](openapi/jefferson-health-tjuh-fhir-r4-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/jefferson-health-tjuh-fhir-r4-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/jefferson-health-tjuh-fhir-r4-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Spectral Ruleset](rules/jefferson-health-tjuh-fhir-r4-rules.yml)

### Thomas Jefferson University Hospital FHIR DSTU2 API

The legacy Thomas Jefferson University Hospital DSTU2 FHIR endpoint listed in Epic's public R4 endpoint registry under the organization "Jefferson Health". It remains available for backward compatibility with older SMART on FHIR apps written against the FHIR DSTU2 baseline that pre-dates the CMS Interoperability and Patient Access mandate; new integrations should target the R4 endpoint instead.

- **Human URL:** [https://fhir.jefferson.edu/FHIRProxy/api/FHIR/DSTU2/](https://fhir.jefferson.edu/FHIRProxy/api/FHIR/DSTU2/)
- **Base URL:** `https://fhir.jefferson.edu/FHIRProxy/api/FHIR/DSTU2`

#### Tags

- DSTU2
- Epic
- FHIR
- Legacy
- SMART on FHIR

#### Properties

- [Documentation](https://fhir.epic.com/Specifications)
- [Endpoint Directory](https://open.epic.com/MyApps/EndpointsJson)
- [Postman Collection](collections/jefferson-health-jhp-provider-directory-fhir-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/jefferson-health-jhp-provider-directory-fhir-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/jefferson-health-tjuh-fhir-r4-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/jefferson-health-tjuh-fhir-r4-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Jefferson Health Plans Patient Access FHIR API

The Jefferson Health Plans (formerly Health Partners Plans) Patient Access FHIR API exposes adjudicated claims, encounter data from providers, formulary data, and certain clinical data to JHP members via third-party apps under the CMS Interoperability and Patient Access Final Rule (CMS-9115-F). The repository conforms to HL7 FHIR Release 4.0.1, is powered by Smile CDR, and uses OAuth 2.0 / OpenID Connect for member authentication and authorization, with developer registration handled through the JHP SMART on FHIR App Developer Portal.

- **Human URL:** [https://smilercdr.healthpartnersplans.com/fhir-request-apps/swagger-ui/](https://smilercdr.healthpartnersplans.com/fhir-request-apps/swagger-ui/)
- **Base URL:** `https://smilercdr.healthpartnersplans.com/`

#### Tags

- CARIN Blue Button
- CMS Interoperability
- FHIR
- Patient Access
- Payer
- R4
- SMART on FHIR
- Smile CDR

#### Properties

- [Documentation](https://www.jeffersonhealthplans.com/home/about-us/interoperability/api-tools/)
- [Developer Resources](https://www.jeffersonhealthplans.com/home/about-us/interoperability/developer-resources/)
- [Authentication Endpoints](https://www.jeffersonhealthplans.com/home/about-us/interoperability/authentication-and-data-endpoints/)
- [App Registration](https://appgallery.healthpartnersplans.com/app-gallery/portal/#/login)
- [API Reference](https://smilercdr.healthpartnersplans.com/fhir-request-apps/swagger-ui/)
- [Smart Configuration](https://smilercdr.healthpartnersplans.com/smartauth/well-known/openid-configuration)
- [Implementation Guide](https://build.fhir.org/ig/HL7/carin-bb/)
- [Compliance](https://www.cms.gov/Regulations-and-Guidance/Guidance/Interoperability/index)
- [Terms of Service](https://www.jeffersonhealthplans.com/home/about-us/interoperability/terms-of-service.html)
- [Postman Collection](collections/jefferson-health-jhp-provider-directory-fhir-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/jefferson-health-jhp-provider-directory-fhir-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/jefferson-health-tjuh-fhir-r4-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/jefferson-health-tjuh-fhir-r4-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

### Jefferson Health Plans Provider Directory FHIR API

The Jefferson Health Plans Provider Directory FHIR API is a public, unauthenticated HL7 FHIR R4 endpoint powered by Smile CDR 2025.02.R04 that surfaces JHP's network of in-network practitioners, organizations, locations, healthcare services, and insurance plans for the CMS Interoperability and Patient Access Provider Directory requirement. Supported resources are Practitioner, PractitionerRole, Organization, Location, HealthcareService, InsurancePlan, and Endpoint, all aligned with the HL7 Da Vinci Payer Data Exchange Plan-Net implementation guide.

- **Human URL:** [https://providerfhirapi.healthpartnersplans.com/](https://providerfhirapi.healthpartnersplans.com/)
- **Base URL:** `https://providerfhirapi.healthpartnersplans.com`

#### Tags

- CMS Interoperability
- Da Vinci Plan-Net
- FHIR
- Payer
- Plan-Net
- Provider Directory
- Public
- R4
- Smile CDR

#### Properties

- [Capability Statement](https://providerfhirapi.healthpartnersplans.com/metadata)
- [Documentation](https://www.jeffersonhealthplans.com/home/about-us/interoperability/api-tools/)
- [Implementation Guide](https://build.fhir.org/ig/HL7/davinci-pdex-plan-net/)
- [OpenAPI](openapi/jefferson-health-jhp-provider-directory-fhir-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/jefferson-health-jhp-provider-directory-fhir-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/jefferson-health-jhp-provider-directory-fhir-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Spectral Ruleset](rules/jefferson-health-jhp-provider-directory-fhir-rules.yml)

### MyJeffersonHealth MyChart Patient Portal

MyJeffersonHealth is the patient-facing Epic MyChart deployment used by Jefferson Health patients to view test results, message providers, request prescription renewals, schedule appointments, pay bills, and grant third-party patient-access apps consent to retrieve their data through Jefferson's underlying FHIR R4 API. It also participates in Epic's MyChart Central, allowing single-login access across participating health systems.

- **Human URL:** [https://my.jeffersonhealth.org/](https://my.jeffersonhealth.org/)
- **Base URL:** `https://my.jeffersonhealth.org/`

#### Tags

- Epic
- MyChart
- Patient Portal

#### Properties

- [Patient Portal](https://my.jeffersonhealth.org/mychart/Authentication/Login)
- [Documentation](https://my.jeffersonhealth.org/mychart/community/faq)
- [My Chart Central](https://www.jeffersonhealth.org/your-health/my-jefferson-health/mychart-central)
- [Patient Portal](https://mychart.jefferson.edu/mychart/Authentication/Login)
- [Overview](https://www.jeffersonhealth.org/your-health/my-jefferson-health)
- [Postman Collection](collections/jefferson-health-jhp-provider-directory-fhir-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/jefferson-health-jhp-provider-directory-fhir-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [Postman Collection](collections/jefferson-health-tjuh-fhir-r4-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/jefferson-health-tjuh-fhir-r4-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)

## Common Properties

- [Website](https://www.jeffersonhealth.org/)
- [Patient Portal](https://my.jeffersonhealth.org/)
- [Patient Portal](https://mychart.jefferson.edu/)
- [My Chart Central](https://www.jeffersonhealth.org/your-health/my-jefferson-health/mychart-central)
- [Locations](https://www.jeffersonhealth.org/locations)
- [Price Transparency](https://www.jeffersonhealth.org/pay-my-bill/charge-description)
- [Price Estimator](https://www.jeffersonhealth.org/pay-my-bill/price-estimator)
- [Privacy Policy](https://www.jeffersonhealth.org/about-us/notice-of-privacy-practices)
- [Terms of Service](https://www.jeffersonhealth.org/terms-of-use)
- [University](https://www.jefferson.edu/)
- [Health Plans](https://www.jeffersonhealthplans.com/)
- [Compliance](https://www.cms.gov/Regulations-and-Guidance/Guidance/Interoperability/index)
- [Compliance](https://www.healthit.gov/curesrule/)
- [JSON-LD](json-ld/jefferson-health-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [Vocabulary](vocabulary/jefferson-health-vocabulary.yml)
- [JSON Schema](json-schema/jefferson-health-patient-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/jefferson-health-observation-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/jefferson-health-practitioner-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Structure](json-structure/jefferson-health-fhir-encounter-structure.json)
- [Plans](plans/jefferson-health-plans-pricing.yml)
- [Rate Limits](rate-limits/jefferson-health-rate-limits.yml)
- [Fin Ops](finops/jefferson-health-finops.yml)
