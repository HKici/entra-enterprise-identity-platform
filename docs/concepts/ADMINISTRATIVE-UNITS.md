# Administrative Units

Status: **Entwurf (IAM-003)**

## Ergebnis der Bewertung

Für die Nordstern Handelsgruppe ist ein begrenztes Standortmodell mit **regulären** Microsoft Entra Administrative Units (AUs) sinnvoll, wenn lokale oder regionale Supportaufgaben kontrolliert delegiert werden sollen. AUs werden dabei ausschließlich als Scope für delegierte Entra-Administrationsrechte eingesetzt. Sie sind weder ein Organisationsverzeichnis noch ein Berechtigungs- oder Anwendungszuweisungsmodell.

Vorgeschlagen sind:

```text
AU-Warehouse-North
AU-Warehouse-South
AU-Stores
```

Die Entscheidung ist in [ADR-0004](../adr/0004-administrative-units-fuer-standortdelegation.md) mit Status `Proposed` festgehalten. Es werden mit diesem Dokument keine AUs, Mitglieder oder Rollenzuweisungen konfiguriert.

## Begriffe und klare Abgrenzung

| Element | Zweck | Keine Funktion als |
| --- | --- | --- |
| Organisatorische Gruppierung | Beschreibt Zugehörigkeit zu Persona, Standort oder fachlicher Rolle. | Scope für Entra-Administrationsrollen, sofern keine AU verwendet wird. |
| Security Group | Bildet Mitgliedschaften und – bei einer dedizierten Access Group – App- oder Ressourcenzugriffe ab. | Administrative Unit oder Ersatz für den Scope einer Entra-Rolle. |
| Administrative Unit | Container für Benutzer, Gruppen oder Geräte, auf dessen Mitglieder unterstützte Entra-Administratorrollen begrenzt werden können. | Security Group, App-/Ressourcenberechtigung oder Conditional-Access-Ziel. |
| Entra-Administratorrolle | Beschreibt, was eine administrative Identität tun darf; die AU begrenzt, auf welche Objekte die Rolle wirkt. | Mitgliedschafts- oder Anwendungsberechtigung. |

Eine Security Group in einer AU bringt nur die Gruppe selbst in den administrativen Scope. Die Benutzer oder Geräte dieser Gruppe sind dadurch nicht automatisch Mitglieder der AU und können nicht aufgrund der Gruppenmitgliedschaft administriert werden. Zu verwaltende Benutzer und Geräte müssen deshalb direkt im passenden AU-Scope enthalten sein. [Microsoft Learn: Administrative Units](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/administrative-units)

## Vorgeschlagenes AU-Modell

| Kontext | AU-Entscheidung | Vorgesehener administrativer Scope | Begründung |
| --- | --- | --- | --- |
| Zentrale Hamburg | Keine eigene AU in der ersten Ausbaustufe. | Zentrale Administration bleibt unter dem später zu definierenden tenantweiten Rollenmodell. | Für die Zentrale ist keine abgegrenzte, lokale Delegationsgrenze beschrieben. Eine AU wäre dort nur eine zusätzliche Organisationsschicht ohne nachgewiesenen Delegationsbedarf. |
| Lager Nord | `AU-Warehouse-North` als reguläre AU. | Direkt aufgenommene Benutzer, Geräte und gegebenenfalls die lokal zu verwaltenden Gruppen. | Die lokale Support-Situation, Shared Devices und hohe Verfügbarkeit begründen einen abgegrenzten Support-Scope. |
| Lager Süd | `AU-Warehouse-South` als reguläre AU. | Direkt aufgenommene Benutzer, Geräte und gegebenenfalls die lokal zu verwaltenden Gruppen. | Eigenständige lokale Support-Strukturen rechtfertigen einen getrennten Scope; er vermeidet Zugriff auf Objekte des Lagers Nord. |
| Filialen | `AU-Stores` als reguläre AU für die erste Ausbaustufe. | Direkt aufgenommene Benutzer, Geräte und gegebenenfalls filialübergreifend zu verwaltende Gruppen. | Die Filialen sind dezentral, ein eigenständiger Support je Filiale ist aber noch nicht beschrieben. Eine gemeinsame AU begrenzt zentral oder regional organisierten Filialsupport, ohne für jede Filiale vorschnell einen Verwaltungsbereich zu schaffen. |
| Externe Dienstleister | Keine Standard-AU. | Kein lokaler administrativer Scope allein aufgrund des externen Status. | Externe Zugehörigkeit ist ein Lifecycle- und Zugriffsmerkmal, keine Delegationsgrenze. Ein begründeter Ausnahmefall benötigt ein gesondertes Design. |
| Privileged Administrator | Keine AU-Mitgliedschaft als Persona-Regel. | Die separaten administrativen Identitäten können als Principals eine Rolle mit Lager- oder Filial-AU-Scope erhalten. | Die Persona beschreibt die Administrationsidentität, die AU begrenzt den Objektumfang. Privilegierung und Standort werden nicht vermischt. |
| Emergency Access Administrator | Keine AU-Mitgliedschaft und keine AU-basierte Rolle als Standard. | Nicht anwendbar; Notfallzugriff folgt `CA-003`. | Emergency Access darf nicht durch lokale Delegationsgrenzen ersetzt oder von ihnen abhängig gemacht werden. |

Die vorgeschlagenen AUs sind keine Hierarchie der gesamten Organisation. Insbesondere wird keine `AU-Hamburg`, keine `AU-External-Contractors` und keine AU für privilegierte Administratoren eingerichtet, solange keine konkrete delegierte Verwaltungsaufgabe nachgewiesen ist.

## Objektumfang und Mitgliedschaft

Eine AU kann Benutzer, Gruppen und Geräte enthalten. Für Lager und Filialen sollen nur die Objekte aufgenommen werden, die ein delegiertes Support-Team tatsächlich verwalten muss:

- Benutzerobjekte der jeweiligen Standortbelegschaft, sofern deren Benutzerverwaltung delegiert wird;
- Shared Devices, Scanner oder andere Geräte, sofern die delegierte Rolle die zugehörige Entra-Geräteverwaltung unterstützt;
- Gruppenobjekte nur dann, wenn deren Eigenschaften oder Mitgliedschaften lokal verwaltet werden müssen.

Die Mitgliedschaftsquelle und eine mögliche Automatisierung werden nicht entschieden. Bis `IAM-004` den Source of Authority und `GOV-001` den Lifecycle festlegt, ist die AU-Mitgliedschaft kontrolliert und nachvollziehbar zu pflegen. Dynamische AU-Mitgliedschaft wird in diesem Entwurf nicht verwendet: Sie setzt belastbare Attribute voraus und kann für Benutzer oder Geräte, nicht jedoch für Gruppen, regelbasiert definiert werden. [Microsoft Learn: dynamische AU-Mitgliedschaft](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/admin-units-members-dynamic)

## Delegierte Administration und Rollenscope

Eine AU begrenzt bei unterstützten Entra-Rollen die Verwaltung auf ihre Mitglieder. Sie erweitert keine Berechtigungen und erlaubt keine Verwaltung tenantweiter Konfigurationen, Anwendungen, Ressourcen oder Conditional-Access-Policies. Eine Rolle mit AU-Scope kann zum Beispiel Gruppen verwalten, die selbst AU-Mitglieder sind; daraus entsteht keine Verwaltungsberechtigung für deren Mitglieder, wenn diese nicht ebenfalls direkt in der AU liegen. [Microsoft Learn: AU-scoped role assignments](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/manage-roles-portal)

Für Lager und Filialen sind nach fachlicher Prüfung nur die folgenden **Kandidaten** für einen AU-Scope zu bewerten:

| Kandidat für delegierte Rolle | Möglicher Zweck im AU-Scope | Abgrenzung |
| --- | --- | --- |
| Helpdesk Administrator | Unterstützungsaufgaben für nicht privilegierte Benutzer im jeweiligen Standort-Scope. | Keine Administration privilegierter Konten und keine tenantweite Rolle. |
| User Administrator | Verwaltung von Benutzerobjekten im jeweiligen Standort-Scope, soweit betrieblich erforderlich. | Keine Ableitung von Anwendungs- oder Ressourcenrechten. |
| Groups Administrator | Verwaltung der direkt in der AU enthaltenen Gruppen, soweit dies für lokale Supportaufgaben nötig ist. | Keine Verwaltung der Benutzerobjekte innerhalb einer Gruppe ohne deren direkte AU-Mitgliedschaft. |
| Cloud Device Administrator | Verwaltung unterstützter Entra-Gerätefunktionen für direkt enthaltene Standortgeräte, wenn der genaue Supportumfang dies rechtfertigt. | Kein Intune- oder allgemeines Endpoint-Management-Modell; dessen Scope ist separat zu entscheiden. |

Ob, welche und in welcher Form diese Rollen tatsächlich vergeben werden, einschließlich PIM, zeitlicher Aktivierung, Anzahl der Administratoren und Testszenarien, bleibt offen. Die Rolle wird einer separaten administrativen Identität zugewiesen, nicht der Office-, Warehouse- oder Store-Persona. Einige administrative Aufgaben und Portale unterstützen keinen AU-Scope; die Eignung jeder Rolle ist vor Einführung anhand der aktuellen Produktdokumentation und eines nichtproduktiven Tests zu prüfen.

## Vor- und Nachteile

| Vorteile | Nachteile und Grenzen |
| --- | --- |
| Delegierte Supportrechte lassen sich auf Lager Nord, Lager Süd oder alle Filialen begrenzen. | AUs steuern nur Entra-Verwaltungsrechte, keine App-, Ressourcen- oder CA-Zugriffe. |
| Standortwechsel können aus einem Verwaltungsscope entfernt werden, ohne die fachliche Rolle oder Access Groups zu ändern. | Benutzer, Gruppen und Geräte müssen für den gewünschten Verwaltungsumfang gegebenenfalls direkt als AU-Mitglieder gepflegt werden. |
| Der Scope unterstützt Least Privilege und erleichtert die Prüfung lokaler Administrationsrechte. | Zusätzliche AUs und Rollenzuweisungen erhöhen Pflege- und Review-Aufwand. |
| Ein gemeinsamer Filial-Scope vermeidet zunächst eine AU pro Filiale. | Eine gemeinsame Filial-AU trennt einzelne Filialen nicht voneinander. |
| Reguläre AUs sind mit späterer, kontrollierter Delegation vereinbar. | Für AU-Administratoren sind passende Lizenzvoraussetzungen zu prüfen; Microsoft nennt hierfür Entra ID P1 oder P2. |

Reguläre AUs schützen nicht vor der Einsicht außerhalb des Scopes durch normale Standardberechtigungen und sind kein Isolations- oder Sicherheitsboundary für Anwendungen. Restricted-management AUs werden nicht für die erste Ausbaustufe vorgeschlagen: Sie können Objektänderungen stark einschränken und kollidieren derzeit mit mehreren Entra-Governance-Funktionen. Ihr Einsatz wäre eine separate Security- und Betriebsentscheidung. [Microsoft Learn: Restricted management AUs](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/admin-units-restricted-management)

## Alternativen

### Keine Administrative Units; ausschließlich tenantweite Administration

Diese Alternative reduziert Verwaltungsobjekte, erlaubt aber keine Begrenzung lokaler Supportrechte auf Lager oder Filialen. Sie wird für delegierte Standortunterstützung nicht empfohlen, weil sie Least Privilege und kontrollierte Delegation schwächt.

### Eine AU für jeden einzelnen Standort einschließlich Hamburg und externer Dienstleister

Diese Alternative maximiert die organisatorische Granularität, erzeugt aber Verwaltungsaufwand ohne belegten Delegationsbedarf. Sie wird nicht vorgeschlagen, weil AUs Verwaltungsgrenzen und keine Abbildung jeder Organisationszugehörigkeit sind. Eine eigene Filial-AU kann später ergänzt werden, wenn ein Filialstandort eine eigenständige Supportverantwortung erhält.

### Security Groups als Ersatz für AUs

Diese Alternative ist ungeeignet. Security Groups unterstützen Personen- und Zugriffszuweisungen, begrenzen aber nicht den Objekt-Scope einer Entra-Administratorrolle.

## Sicherheitsauswirkungen

- Das Modell reduziert die Reichweite lokaler Supportrechte auf direkt enthaltene Objekte der Lager oder Filialen.
- Die klare Trennung verhindert, dass Standort- oder Persona-Gruppen automatisch App-, Ressourcen- oder Entra-Administratorrechte verleihen.
- Separate administrative Identitäten bleiben Voraussetzung für delegierte Rollen; Emergency Access bleibt davon getrennt.
- Falsch gepflegte AU-Mitgliedschaften oder zu breite AU-Rollen können dennoch übermäßige Verwaltungsrechte erzeugen. Sie benötigen vor Einführung Review, Auditierung und einen testbaren Entzugsprozess.
- Restricted-management AUs werden nicht als Ersatz für reguläre Schutzmaßnahmen oder Emergency Access eingesetzt.

## Offene Architekturfragen und Abhängigkeiten

- `IAM-004`: Authoritative Quelle und Lifecycle der Benutzer-, Geräte- und Gruppenobjekte für eine spätere AU-Mitgliedschaft.
- `GOV-001` bis `GOV-005`: Owner, Joiner/Mover/Leaver, Access Reviews, PIM und zeitliche Aktivierung delegierter Rollen.
- Exakter lokaler Supportumfang für Lager Nord, Lager Süd und Filialen sowie die verantwortlichen Teams.
- Konkrete Rollenmatrix, Lizenzprüfung und nichtproduktiver Test der unterstützten Rollen und Verwaltungsportale.
- Entscheidung, ob einzelne Filialen später eigene AUs benötigen.
- `CA-003`: vollständiges Emergency-Access-Design; AUs ersetzen dessen Ausnahmen und Kontrollen nicht.
