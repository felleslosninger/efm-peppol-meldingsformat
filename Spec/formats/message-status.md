# Format "message-status"
Et felles `message-status` format kan benyttes for rapportering tilbake fra alle hjørner.  Dette kan basere seg på Peppol 
standard XML melding `ApplicatinResponse`, eller vi kan benytte en enklere JSON variant i eGovernment domene.  Forslaget som
beskrives her er eget JSON format (slik at det kan sende som JWT i `AdministrativeMessage` konvolutt som alt annet).

**En eller flere dokumenttyper - status kan også fungere som kvitteriger ?** : Hvis vi innførere noen obligatoriske statuser
som alltid skal rapporteres, så kan vi velge å ha disse som egne dokumenttyper (de kan fremdeles være samme format,
bare noen faste varianter).  De faste variantene er da obligatoriske kvitterings dokumenttyper som `message-status-delivered`,
mens valgfrie statuser kommer som  genrelle `message-status`.  Det gjør det mulig for avsender å registrere seg for de
obligatoriske kvitteringene, men ignorere de valgfrie.

| Navn | Varianter / tillatte verdier | Kort forklaring |
|---|---|---|
| `documentTypeId` | `urn:fdc:digdir.no:2026:egovernment::AdministrativeMessage##urn:fdc:digdir.no:2026:egovernment:message-status:1.0` | Identifiserer dokumenttypen og versjonen til statusmeldingen. |
| `messageId` | Unik ID, gjerne UUID | Identifiserer denne statusmeldingen. Brukes også til å oppdage duplikater. |
| `createdAt` | ISO 8601 med tidssone, f.eks. `2026-09-10T14:30:01+02:00` | Tidspunktet statusmeldingen ble opprettet. |
| `originalMessageId` | ID til originalmeldingen | Knytter statusen til den opprinnelige fagmeldingen. |
| `originalDocumentTypeId` | Full dokumenttype-ID for eksempelvis `record-message:1.0` eller `health-message:1.0` | Angir hvilken type originalmelding statusen gjelder. |
| `originalEnvelopeId` | ID til originalforsendelsens konvolutt | Identifiserer den konkrete forsendelsen. Brukes ved kobling mot MLS og leveringsforsøk. |
| `status` | `RECEIVED_C2`, `DELIVERED_C3`, `ARRIVED_C4`, `FAILED` | C2 har motatt, levert til C3, fremme hos C4, feilsituasjon |
| `occurredAt` | ISO 8601 med tidssone | Tidspunktet hendelsen inntraff, som kan være tidligere enn `createdAt`. |
| `reportedBy` | Objekt med `corner` og `systemId` | Identifiserer hvem som opprettet statusmeldingen. |
| `reportedBy.corner` | `C2`, `C3`, `C4` | Hjørnet som opprettet statusmeldingen. |
| `reportedBy.systemId` | Avtalt systemidentifikator | Identifiserer aksesspunktet eller fagsystemet som opprettet statusmeldingen. |
| `source` | `C2`, `AS4`, `MLS`, `C4` | Angir grunnlaget for statusen: egen hendelse hos C2, transportkvittering, MLS eller hendelse hos C4. |
| `sourceCorner` | `C2`, `C3`, `C4` | Hjørnet den underliggende opplysningen kommer fra. Nyttig når C2 oversetter en kvittering fra C3. |
| `stage` | `SUBMISSION`, `TRANSPORT`, `DELIVERY`, `IMPORT`, `PROCESSING` | Angir hvilket steg statusen gjelder. |
| `originalStatusCode` | For MLS: `AP`, `AB`, `RE`. Ellers kildeprotokollens kode | Bevarer statuskoden fra den underliggende kvitteringen. |
| `originalReasonCode` | Kildeprotokollens årsakskode | Bevarer eventuell opprinnelig feilkode eller årsakskode. |
| `reasonCode` | Avtalt kodeliste, f.eks. `INVALID_FORMAT`, `UNSUPPORTED_ATTACHMENT` | Maskinlesbar årsak til feilen eller avvisningen. |
| `description` | Fritekst | Kort, forståelig forklaring på hendelsen. |
| `retryable` | `true`, `false` | Om et nytt forsøk med uendret originalmelding kan lykkes. Er ikke i seg selv en instruks om å sende på nytt. |
| `retryResponsibility` | `C1`, `C2`, `C3`, `C4` | Angir hvem som har ansvar for et eventuelt nytt forsøk. Utelates når det ikke er aktuelt. |
| `sequenceNumber` | Positivt heltall: `1`, `2`, `3`, … | Rekkefølge innen samme statusutsteders hendelser for originalmeldingen. Ikke en global teller på tvers av hjørnene. |
| `processingResult` | Avtalt faglig kode, f.eks. `JOURNALFOERT` | Beskriver resultatet når status er `PROCESSED`. Kan variere mellom fagmeldingstyper. |
| `final` | `true`, `false` | Om utfallet er endelig for det aktuelle steget, ikke nødvendigvis for hele meldingsforløpet. |


## Status varianter (random forslag)

| Navn | Varianter / tillatte verdier | Kort forklaring |
|---|---|---|
| `status` | `SUBMITTED` | C2 har lagret meldingen og overtatt ansvaret for sending. |
| `status` | `RECEIVED_BY_C3` | Mottakers aksesspunkt har bekreftet teknisk mottak. |
| `status` | `RETRYING` | Et nytt leveringsforsøk er planlagt eller pågår. |
| `status` | `CONFIRMATION_MISSING` | Forventet kvittering mangler; utfallet er ukjent. |
| `status` | `DELIVERY_CONFIRMED` | Levering videre mot C4 er bekreftet, eksempelvis gjennom MLS `AP`. |
| `status` | `FORWARDED_UNCONFIRMED` | Videresendt mot C4 uten bekreftelse, tilsvarende MLS `AB`. |
| `status` | `AVAILABLE` | C4 bekrefter at meldingen og nødvendige vedlegg er tilgjengelige for mottaker. |
| `status` | `READ` | C4 bekrefter at en autorisert bruker har åpnet meldingen. |
| `status` | `IN_PROGRESS` | C4 har startet behandlingen. |
| `status` | `PROCESSED` | C4 har fullført den avtalte behandlingen. |
| `status` | `REJECTED` | Meldingen er avvist på grunn av format, innhold eller en faglig vurdering. Årsaken angis separat. |
| `status` | `FAILED` | En teknisk feil hindrer levering, import eller behandling. |

## Format "message-status" eksempler

### C2: Avvist fordi formatet er feil

```json
{
  "processTypeId": "urn:fdc:digdir.no:2026:egovernment:health-exchange:1.0",
  "documentTypeId": "urn:fdc:digdir.no:2026:egovernment::AdministrativeMessage##urn:fdc:digdir.no:2026:egovernment:message-status:1.0",
  "messageId": "status-c2-001",
  "createdAt": "2026-09-10T14:30:01+02:00",
  "originalMessageId": "record-001",
  "originalDocumentTypeId": "urn:fdc:digdir.no:2026:egovernment::AdministrativeMessage##urn:fdc:digdir.no:2026:egovernment:record-message:1.0",
  "status": "REJECTED",
  "occurredAt": "2026-09-10T14:30:00+02:00",
  "reportedBy": {
    "corner": "C2",
    "systemId": "avsenders-aksesspunkt"
  },
  "source": "C2",
  "stage": "SUBMISSION",
  "reasonCode": "INVALID_FORMAT",
  "description": "Meldingen følger ikke avtalt skjema. Det obligatoriske feltet recipient mangler.",
  "retryable": false
}
```

Her betyr `retryable: false` at samme uendrede melding ikke bør forsøkes sendt igjen. Avsender må korrigere formatet.

### C3: Videresendt mot C4 uten bekreftelse (mottak av MLS type AB i C2)

Dette er C2 sin oversettelse av mottatt MLS til message-status for C1. Selve MLS-meldingen fra C3 følger Peppols format.

```json
{
  "processTypeId": "urn:fdc:digdir.no:2026:egovernment:health-exchange:1.0",
  "documentTypeId": "urn:fdc:digdir.no:2026:egovernment::AdministrativeMessage##urn:fdc:digdir.no:2026:egovernment:message-status:1.0",
  "messageId": "status-c2-002",
  "createdAt": "2026-09-10T14:31:06+02:00",
  "originalMessageId": "record-002",
  "originalDocumentTypeId": "urn:fdc:digdir.no:2026:egovernment::AdministrativeMessage##urn:fdc:digdir.no:2026:egovernment:record-message:1.0",
  "originalEnvelopeId": "39fcb125-d870-46ea-a5c1-7b2e90468d3f",
  "status": "FORWARDED_UNCONFIRMED",
  "occurredAt": "2026-09-10T14:31:04+02:00",
  "reportedBy": {
    "corner": "C2",
    "systemId": "avsenders-aksesspunkt"
  },
  "source": "MLS",
  "sourceCorner": "C3",
  "stage": "DELIVERY",
  "originalStatusCode": "AB",
  "description": "Meldingen er videresendt mot mottakersystemet, men levering er ikke bekreftet."
}
```


### C4: Meldingen er gjort tilgengelig for mottaker
```json
{
  "processTypeId": "urn:fdc:digdir.no:2026:egovernment:health-exchange:1.0",
  "documentTypeId": "urn:fdc:digdir.no:2026:egovernment::AdministrativeMessage##urn:fdc:digdir.no:2026:egovernment:message-status:1.0",
  "messageId": "a6b9f120-8c43-4e7d-9a25-6f03d2b841ce",
  "createdAt": "2026-09-10T14:32:05+02:00",
  "originalMessageId": "d281c7a4-56f9-4b30-a812-3e6c95d0f247",
  "originalDocumentTypeId": "urn:fdc:digdir.no:2026:egovernment::AdministrativeMessage##urn:fdc:digdir.no:2026:egovernment:record-message:1.0",
  "originalEnvelopeId": "39fcb125-d870-46ea-a5c1-7b2e90468d3f",
  "status": "AVAILABLE",
  "occurredAt": "2026-09-10T14:32:04+02:00",
  "reportedBy": {
    "corner": "C4",
    "systemId": "mottakers-fagsystem"
  },
  "source": "C4",
  "stage": "DELIVERY",
  "sequenceNumber": 1,
  "description": "Meldingen og vedleggene er lagret og tilgjengelige for mottaker."
}
```


### C4: Meldingen er lest

```json
{
  "processTypeId": "urn:fdc:digdir.no:2026:egovernment:health-exchange:1.0",
  "documentTypeId": "urn:fdc:digdir.no:2026:egovernment::AdministrativeMessage##urn:fdc:digdir.no:2026:egovernment:message-status:1.0",
  "messageId": "status-c4-003",
  "createdAt": "2026-09-10T15:10:01+02:00",
  "originalMessageId": "record-003",
  "originalDocumentTypeId": "urn:fdc:digdir.no:2026:egovernment::AdministrativeMessage##urn:fdc:digdir.no:2026:egovernment:record-message:1.0",
  "status": "READ",
  "occurredAt": "2026-09-10T15:10:00+02:00",
  "reportedBy": {
    "corner": "C4",
    "systemId": "mottakers-fagsystem"
  },
  "source": "C4",
  "stage": "PROCESSING",
  "sequenceNumber": 2,
  "description": "En autorisert bruker har åpnet meldingen."
}
```
