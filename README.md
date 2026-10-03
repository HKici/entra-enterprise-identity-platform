# Entra Enterprise Identity Platform

Referenzprojekt für den Entwurf einer skalierbaren Microsoft-Entra-ID-IAM-Plattform in einem fiktiven deutschen Handelsunternehmen.

Das Projekt verbindet **Identity Architecture**, **Conditional Access**, **Identity Governance**, **Application Onboarding**, **Microsoft Graph**, **PowerShell** und **Git-basierte Änderungsprozesse**.

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

## Ziele

1. Einheitliche Identity- und Access-Standards
2. Sichere und nachvollziehbare Conditional-Access-Baseline
3. Standardisiertes Application Onboarding
4. Automatisierte und versionierte Konfiguration
5. Nachvollziehbare Architekturentscheidungen über ADRs
6. Wiederholbare Änderungen über Git und Pull Requests
7. Lernplattform für Enterprise IAM und Microsoft Entra ID

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
- Infrastructure / Configuration as Code
- Git
- Security Architecture

## Repository-Struktur

```text
.
├── AGENTS.md
├── CHANGELOG.md
├── CONTRIBUTING.md
├── README.md
├── docs/
│   ├── architecture/
│   ├── concepts/
│   ├── project/
│   ├── security/
│   └── adr/
├── src/
│   ├── conditional-access/
│   ├── governance/
│   ├── graph/
│   └── applications/
├── tests/
└── examples/
    └── nordstern-handelsgruppe/
```

## Projektstatus

**Phase 0 – Projektgrundlage**

Die fachliche und technische Basis wird definiert. Produktive Automatisierung folgt erst, nachdem Architektur, Naming, Rollenmodell und Sicherheitsprinzipien dokumentiert wurden.

Siehe [Projektplan](docs/project/PROJEKTPLAN.md).

## Sicherheitsprinzip

Dieses Repository enthält ausschließlich synthetische Beispieldaten.

Keine produktiven Tenant-IDs, Domains, Benutzerkonten, IP-Adressen, Geheimnisse, Tokens oder Konfigurationen realer Organisationen dürfen eingecheckt werden.

## Lizenz

Dieses Projekt dient als technisches Portfolio- und Lernprojekt. Eine formale Open-Source-Lizenz wird bewusst erst später ausgewählt.
