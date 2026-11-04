# Changelog

Wijzigingen in de Aanlever API. Opmaak volgens [Keep a Changelog](https://keepachangelog.com/nl/1.1.0/).

Tijdens de alpha kunnen breaking changes zonder deprecatietermijn voorkomen. Ze staan hier onder
**Breaking**, zodat je voor het bijwerken van een integratie ziet wat je moet aanpassen.

## [0.18.0] - 2026-11-03

### Breaking

- `dctType` is een lijst van URI's.
- `provQualifiedAttribution.provHadRole` accepteert alleen waarden uit `iso_role_code`.

### Toegevoegd

- `dcatContactPoint.vcardOrganizationName`.
- `foafAgent` bij `dctCreator` en `dctPublisher`: URI van de organisatie.

## [0.17.0] - 2026-10-27

### Breaking

- Velden met een waardelijst accepteren alleen waarden uit die lijst (`VALUE_NOT_IN_VALUE_LIST`). De
  veldbeschrijving noemt de lijst. `dcatTheme` gebruikt nu TOOI-thema's in plaats van EU-datathema's.
- `admsIdentifier` moet een URL zijn; opgeslagen waarden die dat niet zijn, zijn verwijderd.
- Strengere grenzen voor lengte, dubbele waarden (`DUPLICATE_VALUE`) en getallen groter dan 0. Zie de
  velden in de specificatie.
- Schrijven van datasets en distributies geeft `503` (`VALUE_LISTS_UNAVAILABLE`) als de waardelijsten
  niet beschikbaar zijn.

## [0.16.0] - 2026-10-20

### Toegevoegd

- Waardelijsten via `GET /api/v1/value-lists` (alle lijsten, zonder waarden),
  `GET /api/v1/value-lists/{id}` (één lijst met haar waarden) en `GET /api/v1/value-lists/document`
  (alle lijsten met hun waarden). Per waarde staan de URI die je in het DCAT-veld invult, labels per
  taal, een eventuele `parent` en een eventuele `endDate`. Met `activeOnly=true` krijg je alleen
  waarden zonder `endDate` of met een `endDate` vanaf vandaag. Lezen kan zonder in te loggen.
- Foutcodes `VALUE_LIST_NOT_FOUND` (`404`) en `VALUE_LISTS_UNAVAILABLE` (`503`) in
  `GET /api/v1/error-codes`.
- `GET /api/v1/datasets/content-hashes`: de id en SHA-256 content-hash van elke gepubliceerde
  dataset, zonder in te loggen. Daarmee zie je welke datasets sinds de vorige keer zijn gewijzigd.

## [0.14.0] - 2026-09-25

### Breaking

- Een distributie vereist `dcatAccessUrl` en `dctLicense`. Aanleveren, vervangen of bijwerken zonder
  een van beide geeft een validatiefout.

### Toegevoegd

- De overige DCAT-AP-NL-velden bij een distributie: `admsStatus`, `dcatAccessService`,
  `dcatByteSize`, `dcatCompressFormat`, `dcatMediaType`, `dcatPackageFormat`,
  `dcatSpatialResolutionInMeters`, `dcatTemporalResolution`, `dcatapApplicableLegislation`,
  `dcatapAvailability`, `dctConformsTo`, `dctIssued`, `dctLanguage`, `dctLicense`, `dctModified`,
  `dctRights`, `foafPage`, `odrlHasPolicy` en `spdxChecksum` (met `spdxAlgorithm` en
  `spdxChecksumValue`).


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
