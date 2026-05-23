# Jefferson Health

Jefferson Health is a multi-state nonprofit academic health system based in Philadelphia, Pennsylvania, operating more than 30 hospitals and over 700 care sites across eastern Pennsylvania and southern New Jersey. It is the clinical arm of the broader Jefferson enterprise, which also includes Thomas Jefferson University (the academic side, founded 1825 as the Infirmary of Jefferson Medical College) and Jefferson Health Plans (formerly Health Partners Plans, the insurance subsidiary).

This repository profiles Jefferson Health's externally accessible API surface. That surface is dominated by HL7 FHIR endpoints required under the CMS Interoperability and Patient Access Final Rule (CMS-9115-F) and the 21st Century Cures Act — not by a traditional commercial developer program. The hospital system's electronic health record runs on Epic and is presented to patients as MyJeffersonHealth / MyChart.

## Documented APIs

| API | Type | Base URL | Notes |
|---|---|---|---|
| Thomas Jefferson University Hospital FHIR R4 | Clinical / Patient Access | `https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4` | Epic-powered FHIR R4 (4.0.1), August 2025 release, US Core 6.1.0, SMART on FHIR, Bulk Data Group export. |
| Thomas Jefferson University Hospital FHIR DSTU2 | Clinical (legacy) | `https://fhir.jefferson.edu/FHIRProxy/api/FHIR/DSTU2/` | Listed in Epic's public R4 endpoint directory; legacy SMART-on-FHIR support. |
| Jefferson Health Plans Patient Access FHIR | Payer / Patient Access | `https://smilercdr.healthpartnersplans.com/` | Smile CDR FHIR R4, CARIN BB aligned, OAuth 2.0 / OIDC. |
| Jefferson Health Plans Provider Directory FHIR | Payer / Plan-Net | `https://providerfhirapi.healthpartnersplans.com` | Public, unauthenticated. Smile CDR 2025.02.R04. Da Vinci PDEX Plan-Net resources. |
| MyJeffersonHealth (MyChart) | Patient Portal | `https://my.jeffersonhealth.org/` | Epic MyChart deployment; also reachable at `mychart.jefferson.edu`. Participates in MyChart Central. |

## Authentication

The Thomas Jefferson University Hospital FHIR R4 endpoint exposes a standard SMART App Launch configuration:

- Authorization endpoint: `https://fhir.jefferson.edu/FHIRProxy/oauth2/authorize`
- Token endpoint: `https://fhir.jefferson.edu/FHIRProxy/oauth2/token`
- Discovery: `https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4/.well-known/smart-configuration`
- Capability statement: `https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4/metadata`
- Supported scopes include `launch`, `openid`, `profile`, `fhirUser`, `offline_access`, plus the standard `patient/*.read`, `user/*.read`, and `system/*.read` SMART scope families.
- App registration is handled through Epic on FHIR at `https://fhir.epic.com/Developer/Apps`.

The Jefferson Health Plans Patient Access API uses OAuth 2.0 / OpenID Connect via Smile CDR's `smartauth` service backed by Azure AD B2C; app developer registration is via `https://appgallery.healthpartnersplans.com/app-gallery/portal/#/login`.

## Regulatory Context

Jefferson's published APIs exist primarily to comply with:

- **CMS-9115-F** — CMS Interoperability and Patient Access Final Rule (Patient Access API and Provider Directory API).
- **21st Century Cures Act / ONC Final Rule** — information-blocking provisions requiring patient access to electronic health information via standard FHIR APIs.

There is no public commercial developer marketplace, no per-call pricing, and no published quantitative rate-limit table. Access is governed by app onboarding agreements with Epic (for the hospital endpoint) and Jefferson Health Plans (for the payer endpoints).

## Repository Layout

| Path | Contents |
|---|---|
| `apis.yml` | apis.json/apis.yml index of every API surface and supporting artifact. |
| `openapi/` | OpenAPI 3.0.3 specs for the TJUH FHIR R4 API and the JHP Provider Directory FHIR API. |
| `capabilities/` | Naftiko capability YAMLs (one per FHIR resource surface) ready to drive sandboxes and MCP/REST adapters. |
| `rules/` | Spectral rulesets enforcing Jefferson's conventions (canonical URLs, FHIR version, USCDI/Plan-Net resource coverage, Title Case summaries). |
| `examples/` | Representative request/response payloads for each FHIR resource search. |
| `json-schema/` | JSON Schema subsets for Patient, Observation, and Practitioner. |
| `json-structure/` | JSON Structure shape for the Encounter resource. |
| `json-ld/` | JSON-LD context aligning Jefferson's FHIR surface with schema.org, HL7 FHIR, US Core, Plan-Net, CARIN BB, LOINC, SNOMED, RxNorm, and UCUM. |
| `vocabulary/` | Domain vocabulary spanning organizations, standards, regulations, FHIR resources, and endpoints. |
| `plans/` | API Commons Plans 0.1 disclosure (all free under CMS mandate). |
| `rate-limits/` | API Commons Rate Limits 0.1 disclosure (limits not publicly published). |
| `finops/` | FinOps Framework / FOCUS disclosure (no consumer-facing billing surface). |

## Notable Absences

- No public GitHub organization for Jefferson Health.
- No published quantitative rate limits on the FHIR endpoints.
- No public commercial developer portal or pricing tiers; access is regulatory-mandated and free.
- Hospital price transparency files (CMS MRF) are referenced on the Charge Description and Price Estimator pages but are not directly indexed here.

## Sources

- Thomas Jefferson University Hospital FHIR CapabilityStatement (live): `https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4/metadata`
- TJUH SMART configuration (live): `https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4/.well-known/smart-configuration`
- Jefferson Health Plans Provider Directory CapabilityStatement (live): `https://providerfhirapi.healthpartnersplans.com/metadata`
- Jefferson Health Plans API Tools page: `https://www.jeffersonhealthplans.com/home/about-us/interoperability/api-tools/`
- Epic public endpoint registry (Jefferson DSTU2 entry): `https://open.epic.com/MyApps/EndpointsJson`
