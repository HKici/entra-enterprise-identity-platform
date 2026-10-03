# Tasks

Dieses Dokument ist die operative Arbeitsliste für ChatGPT/Codex.

Codex soll immer nur **einen klar abgegrenzten Task** bearbeiten.

---

## Phase 0 – Grundlagen

### IAM-001 – Personas konkretisieren

**Status:** Ready  
**Ziel:** Personas für Zentrale, Lager Nord, Lager Süd, Filialen, Externe und Administratoren detaillieren.

**Akzeptanzkriterien:**

- Beschäftigungs-/Identitätstyp
- typische Geräte
- typische Anwendungen
- Authentifizierungsanforderungen
- administrativer Kontext
- Lifecycle-Besonderheiten

**Dateien:**

- `docs/architecture/UNTERNEHMENSSZENARIO.md`
- `docs/concepts/PERSONAS.md`

---

### IAM-002 – Gruppenmodell definieren

**Status:** Blocked by IAM-001  
**Ziel:** Security- und Zuweisungsgruppen für Nordstern definieren.

**Akzeptanzkriterien:**

- keine Vermischung von Rollen- und Standortsemantik ohne Begründung
- Naming Convention eingehalten
- dynamische Gruppen nur mit dokumentierter Begründung
- Lager Nord / Süd berücksichtigt

---

### IAM-003 – Administrative Units bewerten

**Status:** Blocked by IAM-001  
**Ziel:** Prüfen, ob und wie Administrative Units für Lager/Filialen eingesetzt werden.

**Ergebnis:** ADR erforderlich.

---

### IAM-004 – Source of Authority definieren

**Status:** Blocked  
**Ziel:** Quellen für Workforce-, External- und privilegierte Identitäten definieren.

**Ergebnis:** ADR erforderlich.

---

### IAM-005 – Erste Architekturdiagramme erstellen

**Status:** Ready after IAM-001  
**Ziel:** erste Diagramme für Standortmodell und Personas erstellen.

**Diagramme:**

1. Unternehmens-/Standortmodell
2. Persona-/Identity-Modell

**Dateien:**

- `docs/diagrams/source/`
- `docs/diagrams/rendered/`

---

## Phase 1 – Conditional Access

### CA-001 – CA-Namensschema finalisieren

**Status:** Planned

### CA-002 – Baseline Policies fachlich beschreiben

**Status:** Planned

### CA-003 – Emergency Access Design

**Status:** Planned  
**Ergebnis:** ADR erforderlich.

### CA-004 – Policy JSON Schema

**Status:** Planned

### CA-005 – Graph Export

**Status:** Planned

### CA-006 – Dry-Run Deployment

**Status:** Planned

### CA-007 – Drift Detection

**Status:** Planned

---

## Phase 2 – Governance

### GOV-001 – Joiner/Mover/Leaver Prozess

**Status:** Planned

### GOV-002 – Lifecycle Workflows

**Status:** Planned

### GOV-003 – Access Reviews

**Status:** Planned

### GOV-004 – Entitlement Management

**Status:** Planned

### GOV-005 – PIM

**Status:** Planned

---

## Phase 3 – Application Onboarding

### APP-001 – Onboarding Intake Template

**Status:** Planned

### APP-002 – SAML Standard

**Status:** Planned

### APP-003 – OIDC/OAuth Standard

**Status:** Planned

### APP-004 – SCIM Standard

**Status:** Planned

### APP-005 – Application-Onboarding-Diagramm

**Status:** Planned

---

## Phase 4 – Betrieb

### OPS-001 – Logging Konzept

**Status:** Planned

### OPS-002 – Audit & Change Monitoring

**Status:** Planned

### OPS-003 – Incident Learning

**Status:** Planned

---

### SEC-001 – Compliance-Anforderungen definieren

**Status:** Planned

**Ziel:** NIS2- und KRITIS-orientierte Security- und Governance-Anforderungen als übergeordnete Architekturprinzipien für die IAM-Plattform dokumentieren.

**Scope:**

??? von hier bis ???ENDE könnten Zeilen eingefügt oder gelöscht sein
- Least Privilege
- starke Authentifizierung
- privilegierter Zugriff
- Auditierbarkeit
- Access Reviews
- Identity Lifecycle
- Logging und Nachvollziehbarkeit
- Incident-relevante IAM-Anforderungen

**Abgrenzung:**

Keine juristische Einstufung der Nordstern Handelsgruppe als KRITIS-Betreiber.

### SEC-001 – Compliance-Anforderungen definieren

**Status:** Planned

**Ziel:** NIS2- und KRITIS-orientierte Security- und Governance-Anforderungen als übergeordnete Architekturprinzipien für die IAM-Plattform dokumentieren.

**Scope:**

- Least Privilege
- starke Authentifizierung
- privilegierter Zugriff
- Auditierbarkeit
- Access Reviews
- Identity Lifecycle
- Logging und Nachvollziehbarkeit
- Incident-rele

**Abgrenzung:**

Keine juristische Einstufung der Nordstern Handelsgruppe als KRITIS-Betreiber.

**Status** Planned
