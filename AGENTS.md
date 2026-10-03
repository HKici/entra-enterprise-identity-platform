# AGENTS.md

## Zweck

Dieses Repository ist ein Enterprise-IAM-Lern- und Referenzprojekt mit Fokus auf Microsoft Entra ID.

Automatisierte Coding-Assistenten dürfen bei Implementierung, Tests und Dokumentation unterstützen. Architektur- und Sicherheitsentscheidungen müssen jedoch nachvollziehbar begründet und in ADRs dokumentiert werden.

## Arbeitsprinzipien

1. **Security first** – keine Abkürzungen bei Identität, Authentifizierung oder Berechtigungen.
2. **Least privilege** – minimal notwendige Graph-Permissions und Rollen verwenden.
3. **Safe by default** – neue Conditional-Access-Policies standardmäßig zunächst in `report-only`.
4. **No secrets in Git** – niemals Tokens, Secrets, Zertifikate oder reale Tenant-Daten committen.
5. **Idempotent automation** – Skripte sollen wiederholbar und möglichst zustandsbewusst sein.
6. **Small changes** – Änderungen klein halten und logisch committen.
7. **Architecture before automation** – neue größere Funktionen benötigen zuerst eine dokumentierte Entscheidung oder ein Design.
8. **German documentation, English code** – fachliche Dokumentation auf Deutsch; Code, Variablennamen und technische Identifikatoren auf Englisch.
9. **Synthetic data only** – ausschließlich fiktive Organisationen und Identitäten.
10. **Explain trade-offs** – bei mehreren sinnvollen Optionen Vor- und Nachteile dokumentieren.

## Coding-Konventionen

- PowerShell 7+
- `Set-StrictMode -Version Latest`
- `$ErrorActionPreference = 'Stop'` wo sinnvoll
- Microsoft Graph PowerShell SDK oder direkte REST-Aufrufe bewusst auswählen und begründen
- Funktionen mit Verb-Noun-Namensschema
- keine hart codierten Tenant-spezifischen IDs
- Konfiguration bevorzugt aus JSON/YAML laden
- Fehler verständlich ausgeben
- `-WhatIf` / Dry-Run unterstützen, wenn Änderungen am Tenant erfolgen
- Tests für Parsing, Validierung und Policy-Logik vorsehen

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
