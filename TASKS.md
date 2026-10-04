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

### IAM-005 – Hybrid Identity Design

**Status:** Review
**Ziel:** Hybrid-Identity-Zielmodell und Auswahlkriterien für Microsoft Entra Connect Sync und Microsoft Entra Cloud Sync dokumentieren.

**Akzeptanzkriterien:**

- Synchronisationsscope und cloud-only Objekte abgegrenzt
- Attribute, Korrelation und Duplicate Prevention dokumentiert
- Connect Sync und Cloud Sync auf Architekturebene verglichen
- Migration, Koexistenz, Writeback und JML-Abhängigkeiten bewertet
- ADR mit Auswahlkriterien, aber ohne voreilige Produktentscheidung

**Dateien:**

- `docs/architecture/HYBRID-IDENTITY-DESIGN.md`
- `docs/adr/0006-hybrid-identity-design-und-sync-auswahlkriterien.md`

---

### IAM-006 – Erste Architekturdiagramme erstellen

**Status:** Review
**Ziel:** erste Diagramme für Standortmodell, Personas und Hybrid Identity erstellen.

**Diagramme:**

1. Unternehmens-/Standortmodell
2. Persona-/Identity-Modell
3. Hybrid-Identity-Zielarchitektur

**Dateien:**

- `docs/diagrams/source/`
- `docs/diagrams/rendered/`

---

## Phase 1 – Conditional Access

### CA-001 – CA-Namensschema finalisieren

**Status:** Review
**Ziel:** Nachvollziehbares Namensschema für Conditional-Access-Baseline-Policies festlegen.

**Dateien:**

- `docs/concepts/NAMING-CONVENTION.md`
- `docs/security/CONDITIONAL-ACCESS-BASELINE.md`

### CA-002 – Baseline Policies fachlich beschreiben

**Status:** Review
**Ziel:** Conditional-Access-Baseline fachlich beschreiben und ausschließlich in `report-only` einordnen.

**Dateien:**

- `docs/security/CONDITIONAL-ACCESS-BASELINE.md`
- `docs/adr/0007-conditional-access-baseline-modell.md`

### CA-003 – Emergency Access Design

**Status:** Review
**Ziel:** Cloud-only Emergency-Access-Konzept mit begründeten CA-Ausschlüssen, unabhängigen Zugangsmitteln und kontrolliertem Notfallbetrieb definieren.

**Dateien:**

- `docs/security/EMERGENCY-ACCESS.md`
- `docs/adr/0008-emergency-access-break-glass-konzept.md`
- `docs/security/CONDITIONAL-ACCESS-BASELINE.md`
- `docs/concepts/PERSONAS.md`

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
