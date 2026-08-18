# Aanlever API - specificatie

De Aanlever API neemt DCAT-metadata over datasets in ontvangst, valideert die en bewaart die in de
datastore van data.overheid.nl. Deze repository bevat de specificatie, niet de implementatie.

- **Swagger UI**: https://dataoverheid.github.io/AanleverAPI-Specificatie/
- **OpenAPI**: [`openapi.yaml`](openapi.yaml) of [`openapi.json`](openapi.json)
- **Wijzigingen**: [`CHANGELOG.md`](CHANGELOG.md)

## Status: alpha

Deze API is in ontwikkeling en kan wijzigen op manieren die bestaande integraties breken. Lees het
[changelog](CHANGELOG.md) voordat je een integratie bijwerkt, en stem af voordat je een koppeling in
productie neemt.

## Versionering

Het pad bevat de major versie (`/api/v1`). Elke response bevat een `API-Version`-header met de
volledige semver-versie, bijvoorbeeld `0.9.0`. De API volgt de
[NLGov REST API Design Rules 2.1.0](https://gitdocumentatie.logius.nl/publicatie/api/adr/2.1.0/).

## Authenticatie

Systeemkoppelingen wisselen hun API-sleutel om voor een `accessToken`:

```
POST /api/v1/auth/token
Content-Type: application/json

{ "clientId": "<client-id>", "apiKey": "<api-sleutel>" }
```

Stuur het token mee als `Authorization: Bearer <accessToken>`; vraag een nieuw token zodra een aanroep
`401` geeft. Voor de browser zet `GET /api/v1/auth/login` via CAM een `DATASTORE_SESSION`-cookie.

| Wie | Leest | Wijzigt |
| --- | --- | --- |
| Niet ingelogd | Alleen datasets met status `PUBLISHED` | Niets (`401`) |
| Rol User | Plus die van de eigen organisatie | Alleen die van de eigen organisatie |
| Rol Admin | Alle datasets | Alle datasets |

Wat je niet mag zien geeft `404`. Wijzigen van een gepubliceerde dataset van een andere organisatie
geeft `403` met code `DCAT_DATASET_NOT_OWNED`. Distributies en publicatiestatus volgen hun dataset.

## Publicatiestatus

Een aangeleverde dataset is `DRAFT` en staat niet in `GET /api/v1/datasets`. Publiceer met
`?publicationStatus=PUBLISHED` bij de `POST`, of later met
`PUT /api/v1/datasets/{datasetId}/publication-status`.

Toegestane overgangen zijn `DRAFT` -> `PUBLISHED`, `PUBLISHED` -> `WITHDRAWN` en `WITHDRAWN` ->
`PUBLISHED`; elke andere geeft `409`. `WITHDRAWN` is een beheeractie, geen stap in het
aanleverproces. `publishedAt` houdt het tijdstip van de eerste publicatie vast. Per catalog-record
staat in `allowedPublicationStatuses` wat op dat moment mogelijk is.

## Fouten

Foutresponses volgen [RFC 9457](https://www.rfc-editor.org/rfc/rfc9457) (`application/problem+json`)
met een extra veld `code`:

```json
{
  "type": "urn:datastore:problem:dcat-dataset-not-found",
  "title": "DCAT-dataset niet gevonden",
  "status": 404,
  "detail": "Er bestaat geen dataset met id 5aa4d627-2b5a-4b69-b8e9-7af31d1288ee",
  "instance": "/api/v1/datasets/5aa4d627-2b5a-4b69-b8e9-7af31d1288ee",
  "code": "DCAT_DATASET_NOT_FOUND"
}
```

Programmeer tegen `code`; `title` en `detail` zijn om te tonen en kunnen veranderen. Validatiefouten
bevatten daarnaast een `errors`-lijst met per fout een `code` en het `path` in de payload.
`GET /api/v1/error-codes` geeft alle codes.

## Inhoud van deze repository

| Bestand | Inhoud |
| --- | --- |
| `openapi.yaml` | De OpenAPI 3.1-specificatie, prettig leesbaar op GitHub |
| `openapi.json` | Dezelfde specificatie in JSON, voor tooling |
| `index.html` | Swagger UI, gepubliceerd via GitHub Pages |
| `CHANGELOG.md` | Wijzigingen per versie |

Beide specificatiebestanden zijn onbewerkte kopieën van wat de backend publiceert. 