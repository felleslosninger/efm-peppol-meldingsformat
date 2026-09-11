# Saksbehandling (DPO, DPF, DPV)
Denne use case dekker saksbehandlere som ønsker å sende en sak som en `digtal post til virksomhet` (enten det er private, offentlige eller statlige foretak).   Den benyttes en arkivmelding mot `DPO`, `DPF` og `DPV` i dag, men dekker også `DPO` typen avtalt.  

For `DPF` og `DPV` sendes ikke arkivmeldingen videre til hhv KS og Altinn, men konvertes i Integrasjonspunktet til svarut og correspondence.  Vi må uansett endre dagens arkivmelding, da det allerede i dag benyttes med uoffisielle utvidelser.

**Viktig innspill** Det kan kun knyttes ett aksespunkt til denne prosessen for en mottaker, så alle `avtaltmeldinger` til en mottaker havner på det samme aksesspunktet.  For meldinger til DPF og DPV så vil oppslag i SR-light sørge for at meldingene sendes til KS's eller ALTINN's aksesspunkt, og så vil distribusjon til rett postkassemottaker skje der.

**Registrering i SMP***
Det er viktig at det defineres egne dokument typer for alle avtalte formater, slik at mottaker kan regisrere seg med de avtalt formatene han kan håndtere.

**Utfordringen rundt addressering** I dag er det logikk knyttet til om meldingen skal til DPO (elma registrering), DPF (finner i ks adresseregister) eller DPV (fallback til altinn).  Skal vi ha ren peppol må alle bedrifter i hele norge registrers som mottaker av dokumenttypene normal & taushetsbelagt og vi må ha prosesser for KS, ALTINN og DPO (med hvert sitt aksesspunkt).  Et vedlikeholdsmareritt, så vi tenker at det gjøres et oppslag i `SR-light` av typen `jeg skal sende format xxx til virksomhet yyy` og så får man i retur `krypteres melding med sertifikat sss og sendes det til peppol mottaker zzz`.  Det vil forenkle mye og fungere omtrent som i dagens Integrasjonspunkt.

PROCESSID : urn:fdc:digdir.no:2026:egovernment:administration:1.0

DOCUMENTID :
- urn:fdc:digdir.no:2026:egovernment::AdministrativeMessage##urn:fdc:digdir.no:2026:egovernment:journal-message:1.0 `(ikke taushetsbelagt)`
- urn:fdc:digdir.no:2026:egovernment::AdministrativeMessage##urn:fdc:digdir.no:2026:egovernment:confidential-journal-message:1.0 `(taushetsbelagt)`
- urn:fdc:digdir.no:2026:egovernment::AdministrativeMessage##urn:fdc:digdir.no:2026:egovernment:custom-type-message:1.0 `(eksempl på et avtalt format)`
- urn:fdc:digdir.no:2026:egovernment::AdministrativeMessage##urn:fdc:digdir.no:2026:egovernment:message-status:1.0 `(felles status tilbake fra hjørnene, dekker både feil og positive statuser)`
