# Naming Convention

Status: **Entwurf (IAM-002)**

## Grundsatz

Dokumentation und Business-Bezeichnungen verwenden deutsche Begriffe. Technische Identifikatoren bleiben konsistent englisch.

## Beispiele

### Security Groups

Das Schema einer Security Group lautet:

```text
GRP-<Category>-<Name>[-<Qualifier>]
```

`<Category>` beschreibt genau eine der folgenden Semantiken:

| Kategorie | Bedeutung | Muster |
| --- | --- | --- |
| `Persona` | Identitäts- bzw. Beschäftigungstyp | `GRP-Persona-<Persona>` |
| `Site` | Organisatorischer Standort | `GRP-Site-<Site>` |
| `BusinessRole` | Fachliche Funktion ohne direkte technische Berechtigung | `GRP-BusinessRole-<Role>` |
| `Access-App` | Konkretes Zugriffsprofil einer Anwendung | `GRP-Access-App-<Application>-<AccessProfile>` |
| `Access-Resource` | Konkretes Zugriffsprofil auf eine Ressource | `GRP-Access-Resource-<Resource>-<AccessLevel>` |

Beispiele:

```text
GRP-Persona-Office-Users
GRP-Persona-Warehouse-Users
GRP-Persona-Store-Users
GRP-Persona-External-Contractors
GRP-Persona-Privileged-Administrators
GRP-Site-Hamburg
GRP-Site-Warehouse-North
GRP-Site-Warehouse-South
GRP-Site-Store-<StoreCode>
GRP-BusinessRole-Store-Manager
GRP-Access-App-<Application>-<AccessProfile>
GRP-Access-Resource-<Resource>-<AccessLevel>
```

Platzhalter in spitzen Klammern beschreiben die Namensform und sind keine produktiven Namen. Standort, Persona, fachliche Rolle und Zugriff werden nicht in einer einzelnen Gruppe kombiniert. Eine Ausnahme ist nur bei einer Access Group zulässig, wenn der Standort den tatsächlich begrenzten Umfang der benannten Ressource beschreibt und dies dokumentiert ist.

### Rollen und Application Roles

Entra Directory Roles und Application Roles sind keine Security Groups und verwenden deshalb nicht das Präfix `GRP`. Ihre konkrete Benennung und Zuweisung werden im Rollenmodell beziehungsweise Application Onboarding festgelegt. `GRP-BusinessRole-*` beschreibt ausschließlich eine fachliche Funktion und ist keine Entra Directory Role.

### Conditional Access

```text
CA-BL-001-Require-MFA-Workforce
CA-BL-002-Require-MFA-Privileged-Admins
CA-BL-003-Block-Legacy-Authentication
CA-BL-004-Protect-Security-Info-Registration
CA-BL-005-Require-MFA-Risky-SignIns
CA-BL-006-Require-Risk-Remediation-High-User-Risk
CA-BL-007-Require-MFA-External-Contractors
```

Das Schema für Baseline-Policies lautet:

```text
CA-BL-<Sequence>-<Control>-<Scope>
```

- `BL` kennzeichnet eine Baseline-Policy und trennt ihre technische Kennung von Task-IDs wie `CA-003`.
- `<Sequence>` ist eine dreistellige, innerhalb der Baseline eindeutige Nummer und wird nach einer Ausmusterung nicht wiederverwendet. Sie ist ausschließlich eine Kennung, keine Auswertungs- oder Prioritätsreihenfolge.
- `<Control>` beschreibt die beabsichtigte Kontrolle auf Englisch im Pascal-Case.
- `<Scope>` beschreibt den fachlichen Zielbereich, nicht zwingend eine konkrete Security Group.

Policy-Zustand und Rollout-Welle sind keine Bestandteile des Namens. Sie werden getrennt dokumentiert, damit ein Wechsel von `report-only` zu Enforcement nicht zu einer Umbenennung führt.

### Administrative Units

```text
AU-Warehouse-North
AU-Warehouse-South
AU-Stores
```

## Noch zu entscheiden

- verbindliche technische Kennungen für Anwendungen, Access Profiles und Ressourcen
- Owner- und Review-Modell für Gruppen
- Zulässigkeit und technische Grenzen von Gruppenverschachtelung
- Voraussetzungen und zulässige Einsätze dynamischer Gruppen nach der SoA-Entscheidung
- Rollenmodell, PIM und mögliche role-assignable groups
- Lifecycle-Workflows, Access Packages und Enterprise Applications
