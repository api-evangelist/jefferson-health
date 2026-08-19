---
name: Run a Jefferson Health group-level bulk data export
description: >-
  Kick off and poll an asynchronous HL7 FHIR Bulk Data Access (Flat FHIR)
  group-level export against the Thomas Jefferson University Hospital FHIR R4
  endpoint, then download the resulting NDJSON files. Used for population-level
  extracts by authorized backend services, not by patient-facing apps.
api: openapi/_original/jefferson-health-tjuh-fhir-r4-api-openapi.yml
base_url: https://fhir.jefferson.edu/FHIRProxy/api/FHIR/R4
auth: oauth2
operations:
  - getSmartConfiguration
  - getMetadata
  - bulkExportGroup
generated: '2026-08-15'
method: generated
source: >-
  Grounded in the bulkExportGroup operationId in openapi/_original/ and the live
  CapabilityStatement, which declares
  instantiates http://hl7.org/fhir/uv/bulkdata/CapabilityStatement/bulk-data and
  the group-export operation on the Group resource.
---

# Run a Jefferson Health group-level bulk data export

## What this is

The Thomas Jefferson University Hospital FHIR server declares conformance to the
HL7 FHIR Bulk Data Access implementation guide in its CapabilityStatement
(`instantiates: http://hl7.org/fhir/uv/bulkdata/CapabilityStatement/bulk-data`),
and the `Group` resource declares the `group-export` operation. This is the
asynchronous, population-scale path — the only long-running operation on the
whole Jefferson API surface.

## Prerequisites

1. A backend-services client registered and approved at
   <https://fhir.epic.com/Developer/Apps>. Bulk export is a system-level
   capability, not something a patient-facing SMART app gets.
2. Asymmetric client authentication. The token endpoint advertises
   `private_key_jwt` and the `urn:ietf:params:oauth:grant-type:jwt-bearer` grant
   — that is the SMART Backend Services shape. Publish your public key and
   obtain a token from
   `https://fhir.jefferson.edu/FHIRProxy/oauth2/token` with `system/*.read`
   scopes as granted by Jefferson.
3. A `Group` id issued by Jefferson identifying the cohort you are permitted to
   export. You cannot enumerate or invent one.

## Steps

1. **Confirm the capability is still live.** `getMetadata` — check that
   `rest.resource[type=Group].operation` still contains `group-export`.

2. **Kick off the export.** `bulkExportGroup`:

   ```
   GET /Group/{id}/$export
   Accept: application/fhir+json
   Prefer: respond-async
   Authorization: Bearer <token>
   ```

   Optional parameters per the IG: `_type` (comma-separated resource types),
   `_since` (an instant), `_outputFormat` (`application/fhir+ndjson`). Restrict
   `_type` — an unbounded export of an Epic cohort is enormous.

3. **Handle the 202.** A successful kickoff returns HTTP `202 Accepted` with the
   status-polling URL in the **`Content-Location`** response header. This is the
   only non-200 success on this API; treat it as success, not as an error.

4. **Poll.** `GET <Content-Location>` with the same bearer token.
   - `202` with an `X-Progress` header — still running. Honour `Retry-After` if
     present; otherwise back off, starting around 60 seconds. There are no
     rate-limit headers on this API to guide you.
   - `200` with a JSON manifest — complete. The manifest contains
     `transactionTime`, `request`, `requiresAccessToken` and an `output[]` array
     of `{type, url}` NDJSON file references, plus `error[]`.
   - `4xx`/`5xx` — an `OperationOutcome`, or on this server possibly an empty
     `401` body. See `errors/jefferson-health-problem-types.yml`.

5. **Download.** Fetch each `output[].url`, sending the bearer token when the
   manifest sets `requiresAccessToken: true`. Each file is newline-delimited
   FHIR JSON — one resource per line. Stream it; do not buffer whole files.

6. **Clean up.** Issue `DELETE <Content-Location>` when finished to release the
   server-side export.

## Rules you must follow

- **This is PHI at scale.** Every exported line is a clinical record. Handle it
  under the terms of your Jefferson data agreement and HIPAA.
- **Do not re-kick a running export on timeout.** There is no idempotency
  mechanism on this server — the CapabilityStatement declares
  `conditionalCreate: false` and `conditionalUpdate: false` on every resource —
  so a duplicate kickoff is a second full export, not a no-op. Poll the
  `Content-Location` you already hold.
- **Send `Accept: application/fhir+json`.** The Epic proxy defaults to XML.
- **No SLA, no status page.** Jefferson publishes neither
  (`lifecycle/jefferson-health-lifecycle.yml`), so build in your own timeout and
  alerting rather than expecting a health signal.
