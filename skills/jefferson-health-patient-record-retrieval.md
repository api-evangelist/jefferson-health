---
name: Retrieve a patient's Jefferson Health record with SMART on FHIR
description: >-
  Authorize against the Thomas Jefferson University Hospital Epic FHIR R4
  endpoint using SMART App Launch, then read the authorized patient's
  demographics, labs, problems, encounters, medications, allergies and clinical
  documents. Requires a patient-authorized OAuth 2.0 token — there is no
  anonymous access to any clinical resource.
api: openapi/_original/jefferson-health-tjuh-fhir-r4-api-openapi.yml
base_url: https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4
auth: oauth2
operations:
  - getSmartConfiguration
  - getMetadata
  - readPatient
  - searchPatient
  - searchObservation
  - searchCondition
  - searchEncounter
  - searchMedicationRequest
  - searchAllergyIntolerance
  - searchDocumentReference
generated: '2026-08-15'
method: generated
source: >-
  Grounded in the operationIds in openapi/_original/, the live SMART
  configuration in well-known/jefferson-health-tjuh-smart-configuration.json and
  the live CapabilityStatement in conformance/.
---

# Retrieve a patient's Jefferson Health record with SMART on FHIR

## What this is

Thomas Jefferson University Hospital's Epic FHIR R4 endpoint, published under the
CMS Interoperability and Patient Access Final Rule and certified against US Core
6.1.0. It is the surface a patient-access app uses to pull a patient's own
record with that patient's consent.

**Every clinical resource requires a token.** An unauthenticated request returns
HTTP `401` with an **empty body** — no `OperationOutcome`, no
`WWW-Authenticate` payload. Branch on the status code; do not try to parse it.

## Prerequisites

1. Register the application at <https://fhir.epic.com/Developer/Apps> and obtain
   Jefferson Health's approval for production. There is no self-service path to
   live patient data.
2. Read the discovery document first — `getSmartConfiguration`
   (`GET /.well-known/smart-configuration`). It is anonymous and returns 200.

## Steps

### 1. Authorize

From the discovery document (verbatim, fetched 2026-08-15):

- `authorization_endpoint`: `https://fhir.jefferson.edu/FHIRProxy/oauth2/authorize`
- `token_endpoint`: `https://fhir.jefferson.edu/FHIRProxy/oauth2/token`
- `issuer`: `https://fhir.jefferson.edu/FHIRProxy/oauth2`
- `code_challenge_methods_supported`: `S256` — **PKCE is required**
- `token_endpoint_auth_methods_supported`: `client_secret_post`,
  `client_secret_basic`, `private_key_jwt`

Run the SMART App Launch authorization-code flow with PKCE S256. Request
`launch/patient` or `launch` plus the patient-scoped read scopes you actually
need, `openid fhirUser` for identity and `offline_access` if you need a refresh
token. The server advertises `permission-v1` and `permission-v2`, so both
`patient/Observation.read` and `patient/Observation.rs` scope syntaxes are
accepted. See `scopes/jefferson-health-scopes.yml`.

Request the narrowest scope set that answers the question. These are clinical
records.

### 2. Establish patient context

The token response carries the `patient` context id. Then:

- `readPatient` — `GET /Patient/{id}` for demographics
  (`us-core-patient|6.1.0`).
- `searchPatient` — `GET /Patient?...` within the authorized context.

### 3. Read the clinical record

All of the following are `GET /{Resource}?patient={patientId}`:

| Question | Operation | Path |
|---|---|---|
| Labs and vitals | `searchObservation` | `/Observation` |
| Problems and diagnoses | `searchCondition` | `/Condition` |
| Visits | `searchEncounter` | `/Encounter` |
| Prescriptions | `searchMedicationRequest` | `/MedicationRequest` |
| Allergies | `searchAllergyIntolerance` | `/AllergyIntolerance` |
| Notes and C-CDA documents | `searchDocumentReference` | `/DocumentReference` |

`Observation` is profiled 22 different ways (labs, blood pressure, BMI, smoking
status, pulse oximetry, pregnancy status and more). Filter with the
`category` and `code` search parameters rather than pulling everything and
sorting it client-side.

Every one of these resources supports `_revinclude=Provenance:target`, which is
how you get the attribution and source system for a record. Use it when a
clinical assertion needs a provenance trail.

## Rules you must follow

- **Send `Accept: application/fhir+json`.** The Epic proxy defaults to **XML** —
  `GET /metadata` with no Accept header returns a `<CapabilityStatement>` XML
  document.
- **Pagination.** Follow `Bundle.link[relation=next]`. Do not build offsets.
- **Do not retry writes.** The CapabilityStatement declares
  `conditionalCreate: false`, `conditionalUpdate: false` and
  `updateCreate: false` on all 59 resources — there is no idempotency mechanism
  on this server, so a retried create can duplicate a clinical record. Confirm
  with a search before retrying.
- **No rate-limit headers.** Nothing tells you how close to a throttle you are;
  back off exponentially on failure.
- **404 on an unknown resource type** is answered by the web server as an HTML
  page, not a FHIR `OperationOutcome`. Check `Content-Type` before parsing.

## Scope of the record

The CapabilityStatement exposes 59 resource types — including `Immunization`,
`Procedure`, `DiagnosticReport`, `CarePlan`, `CareTeam`, `Coverage` and
`ExplanationOfBenefit` — while the contract in `openapi/` describes 10. Call
`getMetadata` to discover the rest rather than assuming this list is complete.
