# Changelog

Wijzigingen in de Aanlever API. Opmaak volgens [Keep a Changelog](https://keepachangelog.com/nl/1.1.0/).

Tijdens de alpha kunnen breaking changes zonder deprecatietermijn voorkomen. Ze staan hier onder
**Breaking**, zodat je voor het bijwerken van een integratie ziet wat je moet aanpassen.

## [0.12.0] - 2026-09-09

### Toegevoegd

- Zoeken in de eigen catalog-records via `POST /api/v1/me/catalog-records/search`. Filtert op vrije
  tekst, soort resource, publicatiestatus en een `dctModified`-periode. Paginering en sortering als
  bij `GET /api/v1/me/catalog-records`.

## [0.11.0] - 2026-09-08

### Breaking

- `PUT /api/v1/datasets/{datasetId}` en `PATCH /api/v1/datasets/{datasetId}` vereisen de
  queryparameter `version` met de waarde uit `meta.version` van de opgehaalde dataset. Ontbreekt die,
  dan volgt `428` met code `DCAT_DATASET_VERSION_REQUIRED`; wijkt die af van de opgeslagen versie,
  dan volgt `409` met code `DCAT_DATASET_VERSION_CONFLICT` en het veld `currentVersion`.
- `DELETE /api/v1/datasets/{datasetId}` en `PUT /api/v1/datasets/{datasetId}/publication-status`
  geven `409` met code `DCAT_DATASET_VERSION_CONFLICT` als de dataset intussen is gewijzigd.

### Toegevoegd

- `meta.version` bij elke dataset: het versienummer dat je meestuurt bij een volgende `PUT` of
  `PATCH`. Het loopt op bij elke wijziging.
- `version` in de response van aanleveren, vervangen en bijwerken: de versie na die bewerking.
- Foutcodes `DCAT_DATASET_VERSION_REQUIRED` en `DCAT_DATASET_VERSION_CONFLICT` in
  `GET /api/v1/error-codes`.

## [0.10.0] - 2026-09-03

### Toegevoegd

- Zoeken in de gepubliceerde datasets via `POST /api/v1/datasets/search`. Filtert op vrije tekst,
  dataset-id, `dctIdentifier`, publicerende en aanleverende organisatie, `dctModified`-periode en
  `dcatapApplicableLegislation` (opnemen of uitsluiten). Filters gelden tegelijk; binnen een lijst
  matcht een van de waarden. Paginering en sortering als bij `GET /api/v1/datasets`.

### Gewijzigd

- Voorbeelden staan in het Nederlands en tonen `dcatapApplicableLegislation` en `dcatapHvdCategory`.

## [0.9.0] - 2026-08-17

Eerste gepubliceerde specificatie voor de alpha.

### Toegevoegd

- Aanleveren, opvragen, vervangen, bijwerken en verwijderen van DCAT-datasets.
- Beheer van distributies bij een dataset.
- Publicatiestatus: een dataset gaat van `DRAFT` naar `PUBLISHED`. Terugtrekken naar `WITHDRAWN` en
  opnieuw publiceren zijn beheeracties, geen stap in het aanleverproces.
- Zichtbaarheid bij het lezen: zonder authenticatie alleen datasets met status `PUBLISHED`, met rol
  User daarnaast die van de eigen organisatie, met rol Admin alle datasets. Wat je niet mag zien
  geeft `404`, ook de distributies en de publicatiestatus ervan.
- Eigenaarschap bij het wijzigen: wijzigen en verwijderen van een dataset, haar distributies en haar
  publicatiestatus mag alleen met rol Admin of als de dataset bij je eigen organisatie hoort.
- Machine-to-machine authenticatie via `POST /api/v1/auth/token`.
- Catalog-records van de ingelogde gebruiker via `GET /api/v1/me/catalog-records`.
- Machineleesbare foutcodes via `GET /api/v1/error-codes`.
