# ADR-0003: Gruppen- und Berechtigungsmodell

- Status: Proposed
- Datum: 2026-10-03

## Kontext

Die Nordstern Handelsgruppe benötigt ein nachvollziehbares Gruppenmodell für zentrale Verwaltung, Lager Nord, Lager Süd, Filialen, externe Dienstleister und privilegierte Administratoren. Ohne eine klare Trennung würden Gruppen leicht gleichzeitig Standort, Persona, Rolle und Berechtigung beschreiben. Das erschwert Least Privilege, Auditierbarkeit, Standortwechsel und spätere Access Reviews.

Source of Authority, Administrative Units, PIM und das Application Onboarding sind noch nicht entschieden. Das Gruppenmodell darf diese Entscheidungen nicht vorwegnehmen.

## Entscheidung

Als Arbeitsmodell werden vier semantisch getrennte Security-Group-Familien vorgeschlagen:

- Persona Groups für Identitäts- und Beschäftigungstypen,
- Site Groups für organisatorische Standorte,
- Business Role Groups für fachliche Funktionen,
- Access Groups für konkrete Anwendungs- oder Ressourcenzugriffe.

Nur Access Groups dürfen unmittelbar einer Anwendung oder Ressource zugewiesen werden. Persona-, Site- und Business-Role-Gruppen verleihen keine impliziten Zugriffs- oder Entra-Administratorrechte.

Die Persona „Privileged Administrator“ dient nicht als Berechtigungsnachweis für Entra Directory Roles. Die Behandlung privilegierter Rollen, PIM und möglicher role-assignable groups bleibt offen. Für Emergency-Access-Identitäten wird keine allgemeine Gruppenfamilie eingerichtet.

Bis Source of Authority, Attributqualität und JML-Prozess entschieden sind, werden keine dynamischen Gruppen für Berechtigungszuweisungen beschlossen.

## Begründung

Die Trennung erhält die Bedeutung einer Mitgliedschaft eindeutig: Ein Wechsel von Lager Nord zu Lager Süd ändert nur die Standortzugehörigkeit, nicht automatisch Rolle oder Ressourcenzugriff. Access Groups stellen die konkrete Berechtigung transparent dar und ermöglichen eine zielsystembezogene Prüfung.

Das Modell berücksichtigt die besonderen Betriebsumgebungen der Lager und Filialen, ohne Shared-Device-Anmeldung, Standortdelegation oder Identity Source technisch festzulegen.

## Alternativen

### Kombinierte Standort-/Rollen-/Berechtigungsgruppen

Beispiel: `GRP-Warehouse-North-Operators`. Verworfen, da die Gruppe mehrere unabhängige Bedeutungen verbindet. Änderungen am Standort oder an der Rolle führen dadurch leicht zu unbeabsichtigten Berechtigungsänderungen.

### Persona- oder Business-Role-Gruppen direkt Anwendungen zuweisen

Verworfen, da der konkrete Zugriffsprofilbezug im Gruppennamen und in Reviews nicht eindeutig wäre. Die Berechtigung soll stattdessen durch eine dedizierte Access Group sichtbar sein.

### Dynamische Berechtigungsgruppen vor der SoA-Entscheidung

Verworfen, weil die Verlässlichkeit, Semantik und Lifecycle-Verantwortung der benötigten Attribute noch nicht entschieden sind.

## Konsequenzen

### Positiv

- eindeutige Gruppenbedeutung und bessere Auditierbarkeit,
- getrennte Behandlung von Standort-, Persona- und Berechtigungswechseln,
- Grundlage für Least Privilege, Access Reviews und standardisiertes Application Onboarding,
- keine implizite Vergabe privilegierter Rechte.

### Negativ

- für einen einzelnen Zugriff können mehrere fachliche Zuordnungen neben einer Access Group sichtbar sein,
- die spätere Pflege benötigt klar definierte Ownership und JML-Prozesse,
- die technische Eignung von Gruppenverschachtelung muss je Zielsystem geprüft werden.

## Security-Auswirkungen

Der Entwurf reduziert das Risiko überbreiter oder unklarer Berechtigungen. Privilegierte Entra-Rollen und Emergency Access bleiben explizit außerhalb der normalen Persona- und Standortzuweisung.

## Betriebsauswirkungen

Vor einer Umsetzung sind Source of Authority, Gruppen-Ownership, Lifecycle-Prozess, Zielsystemzuweisungen und PIM-Design zu klären. Die Entscheidung bleibt bis zum Architektur- und Security-Review `Proposed`.
