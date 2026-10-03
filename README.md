# Entra Enterprise Identity Platform

Referenzprojekt für den Entwurf einer skalierbaren Microsoft-Entra-ID-IAM-Plattform in einem fiktiven deutschen Handelsunternehmen.

Das Projekt verbindet **Identity Architecture**, **Conditional Access**, **Identity Governance**, **Application Onboarding**, **Microsoft Graph**, **PowerShell**, **Git** und nachvollziehbare Architekturentscheidungen.

> Ziel ist nicht, eine produktive Umgebung 1:1 abzubilden, sondern Architekturentscheidungen, Sicherheitsprinzipien und Automatisierung nachvollziehbar zu dokumentieren und praktisch umzusetzen.

## Unternehmensszenario

Die fiktive **Nordstern Handelsgruppe GmbH** betreibt:

- eine zentrale Verwaltung in Hamburg
- **Lager Nord**
- **Lager Süd**
- Filialen in Deutschland
- interne Mitarbeitende
- externe Dienstleister
- privilegierte Administratoren
- gemeinsam genutzte Endgeräte in Logistikbereichen

Die bestehende Identity-Landschaft ist hybrid. Microsoft Entra ID soll schrittweise zur zentralen Identity- und Access-Plattform ausgebaut werden.

## Projektziele

1. Einheitliche Identity- und Access-Standards
2. Sichere und nachvollziehbare Conditional-Access-Baseline
3. Standardisiertes Application Onboarding
4. Automatisierte und versionierte Konfiguration
5. Nachvollziehbare Architekturentscheidungen über ADRs
6. Wiederholbare Änderungen über Git und Pull Requests
7. Lernplattform für Enterprise IAM und Microsoft Entra ID
8. Professionelles technisches Portfolio mit verständlichen Architekturdiagrammen
9. Berücksichtigung von NIS2- und KRITIS-orientierten Security- und Governance-Anforderungen

## Technische Schwerpunkte

- Microsoft Entra ID
- Conditional Access
- Authentication Strengths
- Identity Governance
- Microsoft Graph API
- PowerShell
- SAML / OIDC / OAuth 2.0
- SCIM
- Hybrid Identity
- Configuration as Code
- Git
- Security Architecture

## Arbeitsweise mit Codex

Codex arbeitet in diesem Repository **nicht autonom an der gesamten Roadmap**.

Vor jeder größeren Implementierung:

1. relevanten Task in `TASKS.md` auswählen,
2. zugehörige Fach- und Architekturdokumentation lesen,
3. prüfen, ob ein ADR nötig ist,
4. kleine, überprüfbare Änderung implementieren,
5. Tests/Dokumentation aktualisieren,
6. Änderung als logisch abgegrenzten Commit vorbereiten.

Die verbindlichen Regeln stehen in [`AGENTS.md`](AGENTS.md).

## Repository-Struktur

```text
.
├── AGENTS.md
├── TASKS.md
├── CHANGELOG.md
├── CONTRIBUTING.md
├── README.md
├── docs/
│   ├── adr/
│   ├── architecture/
│   ├── concepts/
│   ├── diagrams/
│   │   ├── source/
│   │   └── rendered/
│   ├── project/
│   └── security/
├── src/
│   ├── applications/
│   ├── conditional-access/
│   ├── governance/
│   └── graph/
├── tests/
└── examples/
    └── nordstern-handelsgruppe/
```

## Projektstatus

**Phase 0 – Projektgrundlage**

Die fachliche und technische Basis wird definiert. Produktive Automatisierung folgt erst, nachdem Architektur, Naming, Rollenmodell und Sicherheitsprinzipien dokumentiert wurden.

Siehe:

- [Projektplan](docs/project/PROJEKTPLAN.md)
- [Codex Workflow](docs/project/CODEX-WORKFLOW.md)
- [Diagramm-Konventionen](docs/project/DIAGRAMME.md)
- [Lernplan](docs/project/LERNPLAN.md)

## Sicherheitsprinzip

Dieses Repository enthält ausschließlich synthetische Beispieldaten.

Keine produktiven Tenant-IDs, Domains, Benutzerkonten, IP-Adressen, Geheimnisse, Tokens oder Konfigurationen realer Organisationen dürfen eingecheckt werden.

## Lizenz

Dieses Projekt dient als technisches Portfolio- und Lernprojekt. Eine formale Open-Source-Lizenz wird bewusst erst später ausgewählt.
