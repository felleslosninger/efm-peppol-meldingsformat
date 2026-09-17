# På vegne av

STERK ULYDIG HUND DA (311780735) har ikkje eige krypteringssertifkat. FILOSOFISK BEGEISTRET APE (313711218) sender og mottar på vegne av STERK ULYDIG HUND DA (311780735), og meldingen krypteres med FILOSOFISK BEGEISTRET APE (313711218) sitt krypteringssertifi.at

## Case 1 Sending

FILOSOFISK BEGEISTRET APE (313711218) sender melding på vegne av STERK ULYDIG HUND DA (311780735) til KUL SLITEN TIGER AS (314240979)

Tiger må akseptere at meldingen er signert med APE sitt sertifikat og ikkje HUND sitt (HUND har jo ikkje eige sertifikat)

```mermaid
sequenceDiagram
    actor H as STERK ULYDIG HUND DA (hj1)<br/>311780735
    participant A as FILOSOFISK BEGEISTRET APE<br/>313711218
    participant SR as SR-light
    participant AP2 as Ape sitt aksesspunkt (hj2)
    participant SMP as Peppol SMP/SML
    participant AP3 as Tiger sitt aksesspunkt (hj3)
    actor T as KUL SLITEN TIGER AS (hj4)<br/>314240979

    H->>A: Lagar melding til Tiger i Ape sitt system

    A->>SR: Lookup på Tiger
    SR-->>A: Tiger sitt public cert + Tiger sitt orgnr

    A->>A: Krypter payload med Tiger sitt public cert
    A->>A: Signer med sitt private cert<br/>på vegne av Hund


    A->>AP2: Lever melding (avsender = Hund, mottaker = Tiger)

    AP2->>SMP: Lookup på Tiger sitt orgnr
    SMP-->>AP2: Aksesspunkt-info for Tiger

    AP2->>AP3: Send melding over Peppol-transport

    AP3->>AP3: Dekrypter transportlaget
    AP3->>T: Lever kryptert payload
    T->>T: Dekrypter med sitt private cert
```

## Case 2 Receiving

KUL SLITEN TIGER AS (314240979) sender melding til STERK ULYDIG HUND DA (311780735).

FILOSOFISK BEGEISTRET APE (313711218) mottar melding på vegne av STERK ULYDIG HUND DA (311780735)

```mermaid
sequenceDiagram
    actor T as KUL SLITEN TIGER AS (hj1)<br/>314240979
    participant SR as SR-light
    participant AP2 as Tiger sitt aksesspunkt (hj2)
    participant SMP as Peppol SMP/SML
    participant AP3 as Aksesspunkt (hj3)
    participant A as FILOSOFISK BEGEISTRET APE<br/>313711218
    actor H as STERK ULYDIG HUND DA (hj4)<br/>311780735

    T->>SR: Lookup på Hund
    SR-->>T: Ape sitt public cert + Hund sitt orgnr

    T->>T: Krypterer payload med Ape sitt public cert
    T->>AP2: Lever melding (mottaker = Hund sitt orgnr)

    AP2->>SMP: Lookup på Hund sitt orgnr
    SMP-->>AP2: Aksesspunkt-info for Ape

    AP2->>AP3: Send melding over Peppol-transport

    AP3->>AP3: Dekrypter transportlaget
    AP3->>A: Lever kryptert payload
    A->>A: Dekrypter med sitt private cert
    A->>H: Sender melding til Hund <br/>eller viser meldinga til Hund i Ape sitt system
```