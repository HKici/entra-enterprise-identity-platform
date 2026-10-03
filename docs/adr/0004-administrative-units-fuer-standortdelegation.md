# ADR-0004: Administrative Units für Standortdelegation

- Status: Proposed
- Datum: 2026-10-03

## Kontext

Lager Nord und Lager Süd haben eigenständige oder eingeschränkte lokale Supportstrukturen. Filialen sind dezentral, eine eigenständige Delegation je Filiale ist jedoch nicht beschrieben. Die Nordstern Handelsgruppe benötigt kontrollierte Delegation, ohne Standortzugehörigkeit, Security Groups und Berechtigungszuweisungen zu vermischen.

Microsoft Entra Administrative Units können den Scope unterstützter Entra-Administratorrollen auf Benutzer, Gruppen und Geräte begrenzen. Sie sind keine Security Groups und vergeben keine Anwendungs- oder Ressourcenberechtigungen.

## Entscheidung

Als Arbeitsmodell wird ein begrenztes, reguläres Standort-AU-Modell vorgeschlagen:

- `AU-Warehouse-North` für delegierbare Supportaufgaben im Lager Nord,
- `AU-Warehouse-South` für delegierbare Supportaufgaben im Lager Süd,
- `AU-Stores` für delegierbare, filialübergreifende Supportaufgaben in der ersten Ausbaustufe.

Für die Zentrale Hamburg, externe Dienstleister, privilegierte Administratoren und Emergency-Access-Administratoren werden keine AUs allein aufgrund der jeweiligen Zugehörigkeit geschaffen. Separate administrative Identitäten können, wenn fachlich erforderlich, unterstützte Rollen mit einem der genannten AU-Scopes erhalten.

Nur die tatsächlich zu verwaltenden Benutzer, Geräte und gegebenenfalls Gruppen werden direkt in eine AU aufgenommen. Die Mitgliedschaft einer Security Group in einer AU erweitert den Scope nicht auf ihre Mitglieder. Restricted-management AUs und dynamische AU-Mitgliedschaften sind nicht Teil dieses Arbeitsmodells.

## Begründung

Das Modell ermöglicht die kontrollierte Delegation an den beiden Lagerstandorten, ohne einen lokalen Administrator für den gesamten Tenant zu schaffen. Die gemeinsame Filial-AU vermeidet eine unnötige AU pro Filiale, solange keine eigenständige lokale Supportverantwortung vorliegt.

Die Entscheidung hält Standortzugehörigkeit, Berechtigungszuweisung und administrative Delegation getrennt. Sie lässt Source of Authority, PIM, Gruppenmodell und konkrete Rollenmatrix offen.

## Alternativen

### Keine AUs, nur tenantweite Rollen

Verworfen für delegierte Standortunterstützung, weil tenantweite Rollen die Reichweite lokaler Supportrechte unnötig ausweiten.

### Eine AU pro physischem Standort

Derzeit nicht vorgeschlagen. Die zusätzliche Granularität ist nur gerechtfertigt, wenn je Standort eine eigenständige und dauerhafte Supportverantwortung besteht.

### Security Groups als Administrationsscope

Verworfen, weil Security Groups keine Entra-Administratorrollen auf ihre Mitglieder beschränken können.

### Restricted-management AUs

Derzeit nicht vorgeschlagen, weil sie Objektänderungen weitreichend einschränken und zusätzliche Betriebs- sowie Governance-Risiken erzeugen können.

## Konsequenzen

### Positiv

- kleinere administrative Reichweite für lokale Supportaufgaben,
- Trennung von Persona, Standort, Access Groups und Administratorrollenscope,
- Grundlage für spätere Rollenreviews und PIM-Design.

### Negativ

- zusätzlicher Pflege- und Review-Aufwand für direkte AU-Mitgliedschaften,
- Eignung einzelner Rollen und Verwaltungsportale muss vor Einführung getestet werden,
- eine gemeinsame Filial-AU bietet noch keine Trennung einzelner Filialen.

## Security-Auswirkungen

Der Entwurf stärkt Least Privilege, indem delegierte Rollen nur auf AU-Mitglieder wirken sollen. Er ersetzt weder Anwendungsberechtigungen noch Conditional Access oder Emergency Access. Fehlerhafte AU-Mitgliedschaften und überbreite Rollen bleiben ein Risiko und benötigen kontrollierte Reviews.

## Betriebsauswirkungen

Vor einer Annahme müssen der Supportumfang je Standort, Rollenmatrix, Lizenzierung, PIM-Integration, Ownership und Lifecycle der AU-Mitgliedschaften geklärt werden. Die Entscheidung bleibt bis zum Architektur- und Security-Review `Proposed`.
