# Projektplan

## Zielbild

Aufbau einer nachvollziehbaren Referenzarchitektur für eine Enterprise-IAM-Plattform auf Basis von Microsoft Entra ID.

Das Projekt soll gleichzeitig:

1. technische Fähigkeiten demonstrieren,
2. Architekturentscheidungen dokumentieren,
3. als Lernumgebung dienen,
4. sichere Automatisierung mit Microsoft Graph zeigen.

---

## Phase 0 – Projektgrundlage

**Ziel:** Fachlichen und organisatorischen Rahmen definieren.

- [x] Repository-Struktur
- [x] Unternehmensszenario
- [x] Git-Konventionen
- [x] ADR-Prozess
- [x] Security-Grundsätze
- [ ] detaillierte Personas
- [ ] Gruppen- und Rollenmodell
- [ ] Naming Convention
- [ ] Tenant-Zielarchitektur

**Exit-Kriterium:** Identity-Modell und Architekturgrundlagen sind nachvollziehbar dokumentiert.

---

## Phase 1 – Conditional Access Baseline

**Ziel:** Enterprise-taugliche CA-Strategie modellieren.

Geplante Policies:

1. Require MFA for Workforce
2. Strong Authentication for Administrators
3. Block Legacy Authentication
4. Protect Security Information Registration
5. Require Compliant Device for Admin Access
6. Risk-based Sign-in Protection
7. Risk-based User Protection
8. Guest / External Access Baseline
9. Warehouse Shared Device Policy
10. Emergency Access Exclusions

Zusätzlich:

- [ ] Policy-Naming
- [ ] Report-only Rollout
- [ ] Pilotgruppen
- [ ] Ausschlussstrategie
- [ ] Emergency-Access-Konzept
- [ ] Graph-basierter Export
- [ ] Graph-basierter Deployment-Dry-Run
- [ ] Drift Detection

---

## Phase 2 – Identity Governance

- [ ] Joiner / Mover / Leaver
- [ ] Lifecycle Workflows
- [ ] Access Reviews
- [ ] Entitlement Management
- [ ] PIM
- [ ] Administrative Units
- [ ] Rezertifizierungskonzept

---

## Phase 3 – Application Onboarding Factory

- [ ] Intake-Template
- [ ] SAML-Standard
- [ ] OIDC/OAuth-Standard
- [ ] Gruppen-/App-Rollen-Modell
- [ ] SCIM-Provisioning
- [ ] Claims-Standard
- [ ] Conditional-Access-Anbindung
- [ ] Dokumentations-Template
- [ ] Automatisierungs-Prototyp

---

## Phase 4 – Hybrid Identity

- [ ] Source-of-Authority-Modell
- [ ] AD / Entra Synchronisation
- [ ] Entra Connect vs. Cloud Sync
- [ ] Authentifizierungsoptionen
- [ ] Migration / Koexistenz
- [ ] Service Accounts
- [ ] Break-Glass / Emergency Access

---

## Phase 5 – Betrieb und Observability

- [ ] Sign-in Logs
- [ ] Audit Logs
- [ ] Change Monitoring
- [ ] Drift Detection
- [ ] Alerting
- [ ] Incident Learning
- [ ] Betriebsdokumentation

---

## Phase 6 – Portfolio Release 1.0

- [ ] Architekturdiagramme
- [ ] End-to-End Demo
- [ ] Screenshots ausschließlich aus Lab
- [ ] README finalisieren
- [ ] Security Review
- [ ] Repository Cleanup
- [ ] Release `v1.0.0`
