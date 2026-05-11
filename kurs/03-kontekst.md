# Kontekst
Når du får dårlige resultat fra KI-agenten er det ofte fordi den mangler informasjonen den trenger for å svare deg i treningssettet sitt, at den ikke klarer selv å finne det i kodebasen du står i eller at det finnes mange måter å svare på. Da kan vi med hjelpe og styre agenten inn i riktig spor, ved å gi ekstra instruksjoner om hvor agenten kan finne informasjonen.

## Oppgave: Legge til kontekst fra fil
Du så kanskje at agenten leste README.md-filen til tidtaker når du ba om introduksjon? For noen instrukser, vil agenten starte å lete etter filer selv. Dersom du allerede vet hva den trenger, kan en like godt sende med den aktuelle filen som kontekst.

1. Åpne filen [timing_item.html](../tidtaker/templates/timer/timing_item.html)
2. Start en ny chat.
3. I chaten, trykk på filen slik at den legges med i kontekst.
4. Gi denne instruksen

> Hva slags templating er dette? Gi meg en konsis instruks for de vanligste tingene en kan gjøre med templatingen.

## Oppgave: Legg til deler av fil
1. Merk linje 8 i filen [timing_item.html](../tidtaker/templates/timer/timing_item.html): `{{if .Timing.GetBool "isActive"}}`
2. Start en ny chat.
3. Legg merke til at linjen allerede er lag til som kontekst.
4. Gi denne instruksen:

> Hvor kommer .Timing.GetBool fra?

Merking av bestemte områder som er interessant hjelper modellen å holde fokus på det du lurer på, men ofte fungerer det å legge med hele filen også. Det kommer mer om kostnad og ytelse senere.

## Oppgave: Legg til kontekst fra internett
En vanlig feil agenten gjør er å bruke gammel informasjon. Her har typisk modellen en cutoff for informasjonen sin, altså tidspunktet for data modellen var trent på.

Prøv disse to og se om du får ulike resultat:

> Gi meg kommandoen for å legge til gradle wrapper i prosjektet mitt, versjonsnummer skal være med.

> Gi meg kommandoen for å legge til gradle wrapper i prosjektet mitt, versjonsnummer skal være med. Bruk github releases for å finne siste gradle versjon.

Legg merke til at vi ikke trenger å spesifisere hvordan modellen skal hente versjonen fra github releases, men at vi må godta at den henter informasjonen.

TODO: registrere resultat / scoreboard
TODO: agent -> modell

Neste steg er [04-agenter.md](04-agenter.md).

