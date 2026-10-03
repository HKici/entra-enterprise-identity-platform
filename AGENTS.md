# AGENTS.md

## Zweck

Dieses Repository ist ein Enterprise-IAM-Referenz- und Architekturprojekt mit Fokus auf Microsoft Entra ID.

Codex und andere Coding-Assistenten dürfen bei Implementierung, Tests und Dokumentation unterstützen. Architektur- und Sicherheitsentscheidungen müssen jedoch nachvollziehbar begründet und bei Bedarf in ADRs dokumentiert werden.

## Arbeitsmodus

Codex soll:

1. zuerst `README.md`, `TASKS.md` und relevante Dokumente lesen,
2. nur den explizit ausgewählten Task bearbeiten,
3. Änderungen klein und überprüfbar halten,
4. keine noch nicht entschiedenen Architekturfragen selbstständig festlegen,
5. bei Architekturentscheidungen eine ADR vorschlagen bzw. anlegen,
6. bestehende Konventionen respektieren,
7. keine produktive Tenant-Konfiguration voraussetzen.

## Arbeitsprinzipien

1. **Security first** – keine Abkürzungen bei Identität, Authentifizierung oder Berechtigungen.
2. **Least privilege** – minimal notwendige Graph-Permissions und Rollen verwenden.
3. **Safe by default** – neue Conditional-Access-Policies standardmäßig zunächst in `report-only`.
4. **No secrets in Git** – niemals Tokens, Secrets, Zertifikate oder reale Tenant-Daten committen.
5. **Idempotent automation** – Skripte sollen wiederholbar und möglichst zustandsbewusst sein.
6. **Small changes** – Änderungen klein halten und logisch committen.
7. **Architecture before automation** – größere Funktionen benötigen zuerst ein dokumentiertes Design.
8. **German documentation, English code** – fachliche Dokumentation auf Deutsch; Code, Variablennamen und technische Identifikatoren auf Englisch.
9. **Synthetic data only** – ausschließlich fiktive Organisationen und Identitäten.
10. **Explain trade-offs** – bei mehreren sinnvollen Optionen Vor- und Nachteile dokumentieren.
11. **No speculative implementation** – keine Funktionen implementieren, deren fachliche Anforderungen noch nicht definiert wurden.
12. **Diagrams are architecture artifacts** – Diagramme müssen zur Dokumentation passen und versioniert werden.

## Codex Task Protocol

Vor Beginn:

- `TASKS.md` lesen.
- Task-ID nennen.
- Scope und betroffene Dateien bestimmen.
- Relevante ADRs lesen.
- Risiken prüfen.

Während der Umsetzung:

- nur Task-Scope ändern,
- keine unrelated refactors,
- keine echten Tenant-Werte,
- Tests und Dokumentation mitführen.

Nach Abschluss:

- geänderte Dateien nennen,
- Tests/Validierung nennen,
- offene Punkte festhalten,
- passenden Commit-Text vorschlagen.

## Coding-Konventionen

- PowerShell 7+
- `Set-StrictMode -Version Latest`
- `$ErrorActionPreference = 'Stop'` wo sinnvoll
- Microsoft Graph PowerShell SDK oder direkte REST-Aufrufe bewusst auswählen und begründen
- Funktionen mit Verb-Noun-Namensschema
- keine hart codierten Tenant-spezifischen IDs
- Konfiguration bevorzugt aus JSON laden
- Fehler verständlich ausgeben
- `-WhatIf` / Dry-Run unterstützen, wenn Änderungen am Tenant erfolgen
- Tests für Parsing, Validierung und Policy-Logik vorsehen

## Diagramm-Konventionen

- Quelldateien unter `docs/diagrams/source/`
- gerenderte Dateien unter `docs/diagrams/rendered/`
- bevorzugtes Format: `.drawio` als Quelle, `.svg` oder `.png` zur Anzeige
- ein Diagramm soll eine Hauptaussage haben
- keine produktiven Namen, Domains, Tenant-IDs oder Screenshots
- Diagramme in der zugehörigen Markdown-Dokumentation einbetten

## Git-Konventionen

Branch-Namen:

- `feature/...`
- `fix/...`
- `docs/...`
- `refactor/...`
- `security/...`

Commit-Nachrichten:

- `feat: ...`
- `fix: ...`
- `docs: ...`
- `refactor: ...`
- `test: ...`
- `security: ...`
- `chore: ...`

## ADR-Regel

Eine ADR ist erforderlich bei Änderungen an:

- Identity Source of Authority
- Synchronisationsmodell
- Conditional-Access-Strategie
- Rollen- und Berechtigungsmodell
- Authentifizierungsverfahren
- Application-Onboarding-Standard
- SCIM-/Provisioning-Modell
- Graph-Berechtigungen
- Break-Glass-Konzept
- Logging-/Monitoring-Architektur

## Verboten

- reale Unternehmensdaten aus Arbeitsumgebungen
- Secrets oder Zugangsdaten
- produktive Screenshots mit identifizierbaren Daten
- Umgehung von MFA oder Conditional Access
- produktive Änderungen ohne expliziten Dry-Run-/Review-Schritt
- künstlich aufgeblähte Dateien ohne fachlichen Nutzen
- generische Platzhaltertexte, die nicht zum Nordstern-Szenario passen
