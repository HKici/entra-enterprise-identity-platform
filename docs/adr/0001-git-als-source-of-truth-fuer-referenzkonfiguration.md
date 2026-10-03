# ADR-0001: Git als Source of Truth für Referenzkonfiguration

- Status: Accepted
- Datum: 2026-10-03

## Kontext

Das Projekt soll IAM-Konfigurationen nachvollziehbar, reviewbar und reproduzierbar dokumentieren.

## Entscheidung

Git wird als Source of Truth für versionierbare Referenzkonfigurationen, Dokumentation und Automatisierungscode verwendet.

Der reale Zustand eines Entra-Tenants bleibt davon technisch getrennt. Spätere Drift-Detection vergleicht Referenzzustand und Tenant-Zustand.

## Begründung

Git ermöglicht:

- Versionshistorie
- Reviews
- nachvollziehbare Änderungen
- Branching
- Rollback auf Code-/Konfigurationsebene
- dokumentierte Architekturentwicklung

## Alternativen

### Dokumentation ohne Git

Verworfen, da Änderungen schlechter nachvollziehbar wären.

### Direkte Tenant-Konfiguration als alleinige Wahrheit

Verworfen, da Architekturabsicht und Änderungshistorie nicht ausreichend abgebildet werden.

## Konsequenzen

### Positiv

- klare Änderungshistorie
- nachvollziehbarer Engineering-Prozess
- Grundlage für Drift Detection

### Negativ

- Tenant und Repository können auseinanderlaufen
- zusätzliche Validierungslogik erforderlich

## Security-Auswirkungen

Es dürfen keine Secrets oder vertraulichen Tenant-Exporte eingecheckt werden.

## Betriebsauswirkungen

Spätere Automatisierung benötigt einen kontrollierten Review- und Deployment-Prozess.
