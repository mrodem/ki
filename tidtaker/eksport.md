# Eksport av tidtakinger til Xledger

## Mål

Automatisk føre tidtakinger fra tidtaker-appen inn i Xledger timeregistrering via Playwright.

---

## Del 1 – Eksport-endepunkt i tidtaker

### Ny rute
```
GET /api/timings/export?month=2026-06
```

Krever innlogging (cookie-sesjonen som allerede brukes).

### Responsformat (JSON)
```json
[
  {
    "date": "2026-06-02",
    "description": "Møte med kunde",
    "tags": ["prosjekt-abc"],
    "durationMinutes": 60
  }
]
```

### Avrundingsregler
- Avrunding skjer **per tidtaking**, til nærmeste 30 minutter.
- Grense: < 15 min → rund ned, ≥ 15 min → rund opp.
- Eksempel: 20 min → 30 min. To slike = 2 × 30 = 60 min totalt.
- Tidtakinger under 15 min (→ 0 min etter avrunding) **utelates**.

### Hva inkluderes
- Kun **fullførte** tidtakinger (`isActive = false`).
- Kun tidtakinger med `startTime` i angitt måned.

---

## Del 2 – Playwright-script

### Fil
`tidtaker/scripts/xledger.ts`

### Kjøring
```bash
# Eksporter tidtakinger for juni
curl -b cookies.txt "http://localhost:8090/api/timings/export?month=2026-06" > timings-2026-06.json

# Kjør Playwright-scriptet
npx ts-node scripts/xledger.ts timings-2026-06.json
```

### Konfigurasjonsfil (`xledger-config.json`)
Definerer mapping fra tag → Xledger-prosjektkode.

```json
{
  "url": "https://www.xledger.net/",
  "tagMapping": {
    "prosjekt-abc": { "project": "1234", "activity": "Utvikling" },
    "intern":        { "project": "0001", "activity": "Interntid" }
  },
  "defaultProject": "0001"
}
```

**Antakelse:** En tidtaking kan ha flere tags; scriptet bruker **første tag** med en kjent mapping. Tidtakinger uten kjent tag føres på `defaultProject`.

### Flyten i scriptet
1. Les JSON-fil og konfigurasjonsfil.
2. Åpne Xledger i Chromium.
3. Naviger til `https://www.xledger.net/` og **vent på manuell innlogging** (trykk Enter i terminalen for å fortsette). Alternativt: bruk env-variabler `XLEDGER_USER` / `XLEDGER_PASS` for automatisk innlogging.
4. For hver dag i JSON-filen:
   - Naviger til timeregistrering for den dagen.
   - Summer timer per prosjekt/aktivitet for dagen.
   - Fyll inn én rad per prosjekt i Xledger-timeregistreringen.
5. Skriv ut oppsummering til terminalen.

---

## Antakelser om Xledger

Disse er **ikke verifisert** og må sjekkes mot faktisk UI:

| Antakelse | Begrunnelse |
|-----------|-------------|
| Timeregistrering finnes under menyvalg "Tid" eller lignende | Vanlig struktur i Xledger |
| Én rad per prosjekt+aktivitet per dag | Standard Xledger-oppsett |
| Datovelger lar seg sette via klikk eller input-felt | Observert i lignende systemer |
| Timeantall angis som desimaltall (f.eks. `1.5`) | Vanlig i Xledger |
| Lagring skjer via "Lagre"- eller "Godkjenn"-knapp | Må verifiseres |

---

## Gjenstående avklaringer

- [ ] Verifiser faktisk DOM-struktur i Xledger (navigasjon, feltenes `name`/`id`, lagreknapp).
- [ ] Hva skjer ved **konflikt** – finnes det allerede timer for dagen? (Foreløpig antakelse: hopp over og advar.)
- [ ] Skal scriptet håndtere **dager uten tidtakinger**, eller bare ignorere dem?
- [ ] Ønsker du at scriptet kjører i `headless`-modus eller med synlig nettleser? (Anbefaler synlig ved første bruk.)
