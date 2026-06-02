# Go html/template – rask guide

**Dokumentasjon:** https://pkg.go.dev/html/template@go1.26.3


## Definer og bruk templates
```html
{{define "navn"}} ... {{end}}
{{template "navn" .}}
```

## Skriv ut verdier
```html
{{.Felt}}                        <!-- felt på gjeldende objekt -->
{{.Metode "arg"}}                <!-- kall metode -->
{{funcName .Felt}}               <!-- kall registrert funksjon -->
```

## If / else
```html
{{if .Felt}}
  vises hvis truthy
{{else}}
  vises ellers
{{end}}
```

## Løkke (range)
```html
{{range .Liste}}
  {{.}}                          <!-- gjeldende element -->
  {{$.RootFelt}}                 <!-- tilgang til rot-kontekst med $ -->
{{end}}
```

## Variabler
```html
{{$x := .Felt}}
{{$x}}
```

## Kommentarer
```html
{{/* dette vises ikke i output */}}
```

## Rot-kontekst ($) i range
Inne i `range` peker `.` på hvert element. Bruk `$` for å nå den opprinnelige konteksten:
```html
{{range .Tags}}
  <button hx-post="/api/{{$.Id}}/remove">{{.}}</button>
{{end}}
```

## Datakilde: PocketBase Record
I dette prosjektet er templatedata typisk PocketBase `*core.Record`-objekter sendt fra Go:

```go
// handlers_timer.go
return renderPartial(e, "timing_item", "timing_item", map[string]any{
    "Timing": record,  // *core.Record fra PocketBase
})
```

Feltene leses med type-spesifikke metoder:
```html
{{.Timing.GetBool "isActive"}}        <!-- bool-felt fra DB -->
{{.Timing.GetString "description"}}   <!-- streng-felt fra DB -->
{{.Timing.GetDateTime "startTime"}}   <!-- tidspunkt-felt fra DB -->
{{.Timing.GetStringSlice "tags"}}     <!-- liste-felt fra DB -->
{{.Timing.Id}}                        <!-- innebygd ID-felt -->
```

Feltet `isActive` settes i Go-handleren med `record.Set("isActive", true)` og er definert som et databasefelt i migrasjonen (`pb_migrations/timings.go`).
