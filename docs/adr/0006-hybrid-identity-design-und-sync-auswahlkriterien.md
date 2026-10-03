# ADR-0006: Hybrid-Identity-Design und Sync-Auswahlkriterien

- Status: Proposed
- Datum: 2026-10-03

## Kontext

Die Nordstern Handelsgruppe betreibt eine hybride Identity-Landschaft mit weiterhin erforderlichen On-Premises-AD-Abhängigkeiten und Microsoft Entra ID als Cloud-Identity- und Access-Control-Plane. Die Anforderungen an AD-Topologie, Geräte-Synchronisation, erweiterte Regeln, Writeback und Migrationsfähigkeit sind noch nicht vollständig erhoben.

Eine voreilige Wahl zwischen Microsoft Entra Connect Sync und Microsoft Entra Cloud Sync würde technische Risiken erzeugen. Privilegierte und Emergency-Access-Identitäten müssen unabhängig von der hybriden Synchronisation cloud-only bleiben.

## Entscheidung

Es wird kein Synchronisationsprodukt final ausgewählt. Stattdessen wird ein Hybrid-Identity-Zielmodell mit verbindlichen Auswahlkriterien vorgeschlagen:

- Nur interne Workforce-Objekte mit bestätigter On-Premises-Abhängigkeit werden in einen Hybrid-Sync-Scope aufgenommen.
- Externe, privilegierte und Emergency-Access-Identitäten sowie Entra-Rollen, Administrative Units und Cloud-Zugriffsobjekte bleiben cloud-only.
- Jedes Objekt hat genau einen aktiven Synchronisationspfad; Connect Sync und Cloud Sync dürfen nicht gleichzeitig dieselben Objekte nach Entra exportieren.
- Ein unveränderliches, eindeutiges Korrelationsmerkmal ist Voraussetzung für Matching, Migration und Duplicate Prevention.
- Die Produktauswahl erfolgt nach dokumentierter Prüfung von Funktionsumfang, Komplexität, Betriebsmodell, Resilienz, Hybridabhängigkeiten, AD-Landschaft, Migration und Security-Risiken.

Die Entscheidung respektiert ADR-0005 als Source-of-Authority-Abhängigkeit und legt keine Synchronisationsrichtung, Attributzuordnung, OU-Struktur oder Writeback-Funktion fest.

## Begründung

Connect Sync bietet weiterhin Vorteile bei komplexen Regeln und bestimmten Geräte- oder Hybridanforderungen. Cloud Sync bietet ein cloudverwaltetes Agentenmodell und eignet sich für standardisierte sowie bestimmte Multi-Forest-Szenarien. Ohne Inventar der tatsächlichen Abhängigkeiten lässt sich kein Werkzeug verantwortbar auswählen.

Das objektbezogene Modell verhindert doppelte Exporte und trennt hybride Workforce-Objekte von cloud-only privilegierten und Notfallidentitäten.

## Alternativen

### Connect Sync sofort festlegen

Verworfen, weil die Anforderungen an komplexe Regeln, Geräte und Writeback noch nicht validiert sind.

### Cloud Sync sofort festlegen

Verworfen, weil Funktionsparität und Migrationsfähigkeit für die konkrete AD-Landschaft noch nicht bestätigt sind.

### Alle Identitäten aus AD synchronisieren

Verworfen, weil externe, privilegierte und Emergency-Access-Identitäten bewusst cloud-only bleiben sollen.

## Konsequenzen

### Positiv

- vermeidet einen nicht begründeten Tool-Lock-in,
- reduziert Duplicate- und Scope-Risiken,
- schafft eine prüfbare Grundlage für Migration und Koexistenz,
- erhält die Trennung von Workforce-, privilegierten und Emergency-Access-Identitäten.

### Negativ

- Produktauswahl und technische Implementierung bleiben bis zur Anforderungserhebung offen,
- Inventarisierung und Pilotierung verursachen zusätzlichen Designaufwand,
- Writeback und komplexe Hybridabhängigkeiten benötigen separate Bewertungen.

## Security-Auswirkungen

Die Entscheidung begrenzt den Sync-Scope auf bestätigte Hybridobjekte und schützt cloud-only Administrator- sowie Notfallidentitäten vor AD-abhängigen Änderungen. Falsches Matching, doppelte Exporte, Scope-Entzüge und unkontrollierte Writeback-Funktionen bleiben zentrale Risiken und benötigen Tests, Reviews und einen Rollback-Plan.

## Betriebsauswirkungen

Vor einer Auswahl sind Topologie, Funktionsbedarf, Agent- oder Serverresilienz, Monitoring, Backup, Recovery, Lizenzierung und JML-Integration zu bewerten. Die Entscheidung bleibt bis zum Architektur- und Security-Review `Proposed`.
