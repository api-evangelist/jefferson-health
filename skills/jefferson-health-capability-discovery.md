---
name: Discover what a Jefferson Health FHIR endpoint can actually do
description: >-
  Read a Jefferson Health FHIR server's own CapabilityStatement and SMART/OIDC
  discovery documents before calling anything else, so an agent binds to what the
  server really serves rather than to a stale description of it. Both discovery
  paths are anonymous and return 200.
api: openapi/_original/jefferson-health-tjuh-fhir-r4-api-openapi.yml
base_url: https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4
auth: none
operations:
  - getMetadata
  - getSmartConfiguration
generated: '2026-08-15'
method: generated
source: >-
  Grounded in the getMetadata / getSmartConfiguration operationIds in
  openapi/_original/ and the documents saved in conformance/ and well-known/.
---

# Discover what a Jefferson Health FHIR endpoint can actually do

## Why this comes first

The written contracts in `openapi/` describe 10 paths on the hospital endpoint
and 7 on the payer directory. The **servers** expose 59 and 8 resource types
respectively. Any agent that plans from the contract alone will both miss
capability and, occasionally, ask for something that has moved. The
CapabilityStatement is the server's own answer, it is anonymous, and it is
cheap.

## The three anonymous discovery calls

| What | Call | URL |
|---|---|---|
| Hospital capabilities | `getMetadata` | `https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4/metadata` |
| Hospital SMART config | `getSmartConfiguration` | `https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4/.well-known/smart-configuration` |
| Payer directory capabilities | `getMetadata` | `https://providerfhirapi.healthpartnersplans.com/metadata` |

A fourth, `https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4/.well-known/openid-configuration`,
returns the OIDC discovery document.

## Steps

1. **Always send `Accept: application/fhir+json`.** Without it the Epic proxy
   returns an XML `<CapabilityStatement>`. This is the single most common
   integration mistake against this endpoint.

2. **Read the version block.** `software.name`, `software.version`,
   `software.releaseDate` and `implementation.description` are the only dated
   change signals Jefferson publishes — there is no changelog and no status
   page. As of 2026-08-15: Epic "February 2026" (released 2026-05-28) on the
   hospital endpoint, Smile CDR 2026.02.R02 / HAPI FHIR 8.8.1 on the payer
   directory. Snapshot these and diff them on a schedule if you need to detect
   platform change.

3. **Read `instantiates`.** The hospital endpoint declares
   `us-core-server|6.1.0` and the Bulk Data CapabilityStatement — that is the
   machine-readable claim of US Core 6.1.0 and Bulk Data conformance.

4. **Enumerate `rest[0].resource[]`.** For each resource you care about, check:
   - `interaction[].code` — `read`, `search-type`, `create`, `update`. Most
     hospital resources are read/search only; a few (`Observation`, `Condition`,
     `AllergyIntolerance`, `DocumentReference`, `Patient`) also allow writes.
   - `searchParam[].name` — the parameters the server will honour. Do not use a
     parameter that is not listed.
   - `supportedProfile` — the exact US Core or Plan-Net profile version, e.g.
     `us-core-observation-lab|6.1.0`.
   - `searchInclude` / `searchRevInclude` — the traversals the server will
     perform. On the payer directory these are an explicit whitelist per
     resource; treat them as the reference graph
     (`data-model/jefferson-health-data-model.yml`).

5. **Read the security block.** `rest[0].security.service` names `OAuth` and
   `SMART-on-FHIR` on the hospital endpoint, and the
   `http://fhir-registry.smarthealthit.org/StructureDefinition/oauth-uris`
   extension carries the authorize and token URLs. The payer directory's
   extension additionally publishes `manage`, `introspect` and `revoke` URLs on
   `https://appgallery.healthpartnersplans.com/smartauth-fhir/`.

6. **Read the SMART configuration** for `capabilities[]`, `grant_types_supported`
   and `code_challenge_methods_supported` before designing the auth flow, rather
   than assuming a standard SMART deployment.

## What discovery will not give you

- No `/.well-known/security.txt` exists on any Jefferson host. The 200 at
  `https://www.jeffersonhealth.org/.well-known/security.txt` is an Adobe
  Experience Manager HTML shell, not a security.txt.
- No `/.well-known/api-catalog`, no agent card, no MCP server.
- `https://providerfhirapi.healthpartnersplans.com/.well-known/smart-configuration`
  returns `{}` — an empty object, not a discovery document.

See `well-known/jefferson-health-well-known.yml` for the full probe record.
