---
name: Search the Jefferson Health Plans provider directory
description: >-
  Find a practitioner, organization, location, service or insurance plan in the
  Jefferson Health Plans Da Vinci Plan-Net directory. This is the only Jefferson
  Health API surface that is callable with no credentials, which makes it the
  right starting point for any agent working with Jefferson data.
api: openapi/_original/jefferson-health-jhp-provider-directory-fhir-api-openapi.yml
base_url: https://providerfhirapi.healthpartnersplans.com
auth: none
operations:
  - getMetadata
  - searchPractitioner
  - readPractitioner
  - searchPractitionerRole
  - searchOrganization
  - readOrganization
  - searchLocation
  - searchHealthcareService
  - searchInsurancePlan
  - searchEndpoint
generated: '2026-08-15'
method: generated
source: >-
  Grounded in the operationIds in openapi/_original/ and the live
  CapabilityStatement in
  conformance/jefferson-health-jhp-provider-directory-capabilitystatement.json.
---

# Search the Jefferson Health Plans provider directory

## What this is

A public HL7 FHIR R4 provider directory conforming to Da Vinci PDEX Plan-Net
1.2.0, served by Smile CDR at `https://providerfhirapi.healthpartnersplans.com`.
It is published under the CMS Interoperability and Patient Access Final Rule,
which requires payer provider directories to be **anonymously readable** — do not
attempt to authenticate.

## Before you start

1. Call `getMetadata` (`GET /metadata`) once and cache it. It tells you exactly
   which resources and search parameters this server honours. Only eight
   resource types exist here: `InsurancePlan`, `Location`, `Organization`,
   `OrganizationAffiliation`, `Practitioner`, `HealthcareService`,
   `PractitionerRole`, `Endpoint`.
2. Always send `Accept: application/fhir+json`.

## Steps

### Find a clinician by name

1. `searchPractitioner` — `GET /Practitioner?family=<surname>&given=<forename>`.
   Supported parameters are only `_id`, `_lastUpdated`, `name`, `family`,
   `given`. There is no specialty parameter on `Practitioner`.
2. Read `Bundle.entry[].fullUrl` for each match; the logical id is the last path
   segment. **Never construct a FHIR id.**
3. `readPractitioner` — `GET /Practitioner/{id}` to fetch one record in full.

### Answer "is this clinician in my network, and where do they practice?"

`searchPractitionerRole` is the join entity and the highest-value call on this
server. One request with `_include` returns the whole picture:

```
GET /PractitionerRole?practitioner={practitionerId}
    &_include=PractitionerRole:organization
    &_include=PractitionerRole:location
    &_include=PractitionerRole:network
    &_include=PractitionerRole:service
```

`network` references an `Organization` playing the Plan-Net network role — that
reference is what tells you whether the clinician participates in a given
Jefferson Health Plans product.

### Find services and places

- `searchHealthcareService` — `GET /HealthcareService?specialty=<code>&location={id}`
- `searchLocation` — `GET /Location?address-city=Philadelphia&address-state=PA`
- `searchOrganization` / `readOrganization` — organizations, with `partof` for
  the parent-organization chain.

### Find the plans themselves

`searchInsurancePlan` — `GET /InsurancePlan?_count=25`, with
`_include=InsurancePlan:coverage-area` to get the geography and
`_include=InsurancePlan:administered-by` for the administering organization.

## Rules you must follow

- **Pagination.** Responses are `searchset` Bundles. Follow
  `Bundle.link[relation=next]` until it is absent. `Bundle.total` is **not**
  returned, so you cannot know how many pages remain — never guess, and never
  build your own offset URLs. The `next` link carries an opaque `_getpages`
  token.
- **Errors.** Failures are FHIR `OperationOutcome` resources with a HAPI code in
  `issue[].diagnostics`:
  - `HAPI-0323` (400) — unknown search parameter. The diagnostics string lists
    every valid parameter for that resource, so recover by re-reading it and
    retrying.
  - `HAPI-2001` (404) — that logical id does not exist.
  - `HAPI-0302` (404) — that resource type is not served here.
  See `errors/jefferson-health-problem-types.yml`.
- **Correlation.** Every response carries `X-Request-ID`. Log it.
- **No caching.** The server returns `Cache-Control: no-cache, no-store`.
  Respect it rather than holding directory data indefinitely.
- **No writes.** Every resource here is read/search only. There is no create,
  update or delete, and no idempotency mechanism anywhere on Jefferson Health's
  APIs — see `conventions/jefferson-health-conventions.yml`.

## What this surface cannot tell you

This directory is a Jefferson Health **Plans** (payer) artifact. It shares no
identifiers with the Thomas Jefferson University Hospital clinical FHIR server,
so you cannot join a `MedicationRequest` from the hospital to a `Practitioner`
here by id. Join on NPI or name, and say so when you do.
