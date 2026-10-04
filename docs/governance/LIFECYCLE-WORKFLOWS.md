# Lifecycle Workflows

Status: **Entwurf (GOV-002)**

## Ziel und Abgrenzung

Dieses Dokument bewertet Microsoft Entra Lifecycle Workflows als möglichen Ausführungsbaustein innerhalb des fachlichen Joiner-/Mover-/Leaver-Modells. Es legt keine produktiven Workflows, Trigger, Aufgaben, Gruppen-, Lizenz- oder Kontenkonfigurationen fest.

GOV-001 in [JOINER-MOVER-LEAVER.md](JOINER-MOVER-LEAVER.md) bleibt die fachliche Quelle für Auslöser, Freigaben, Separation of Duties und Abschlusskriterien. Lifecycle Workflows können nur Aufgaben für bereits in Microsoft Entra ID vorhandene und regelkonform abgegrenzte Benutzerobjekte orchestrieren. Sie ersetzen weder die Attributautorität aus dem [Source-of-Authority-Modell](../architecture/SOURCE-OF-AUTHORITY.md) noch den HR-, Vertrags- oder Sponsorprozess.

Die Architekturentscheidung ist in [ADR-0011](../adr/0011-lifecycle-workflows-als-begrenzte-jml-orchestrierung.md) mit Status `Proposed` dokumentiert.

## Grundsätzliche Rolle

Lifecycle Workflows bestehen aus Ausführungsbedingungen und Aufgaben. Sie können für Joiner-, Mover- und Leaver-Szenarien orchestrieren, wenn das Benutzerobjekt sowie die benötigten Trigger- und Scope-Attribute in Entra verfügbar sind. Zeitbasierte Joiner- und Leaver-Auslöser stützen sich beispielsweise auf `employeeHireDate` beziehungsweise `employeeLeaveDateTime`; Attributänderungen und Gruppenmitgliedschaftsänderungen sind weitere dokumentierte Triggerarten. [Microsoft Learn: Ausführungsbedingungen](https://learn.microsoft.com/en-us/entra/id-governance/lifecycle-workflow-execution-conditions)

Das ist eine nachgelagerte Orchestrierung, keine Stammdatenpflege: HR bleibt für Beschäftigung, Organisation und Standort führend; das noch nicht konkret benannte Vertrags-/Sponsor-System bleibt für externe Aufträge, Sponsoren und Laufzeiten führend. Lifecycle Workflows dürfen widersprüchliche oder fehlende Daten nicht mit einer eigenen „Last writer wins“-Logik ersetzen.

| Voraussetzung | Bedeutung für Nordstern |
| --- | --- |
| Entra-Objekt und Attribute vorhanden | Das Ereignis kann erst verarbeitet werden, wenn das passende Benutzerobjekt sowie qualitätsgesicherte Attribute in Entra verfügbar sind. |
| Eindeutige Korrelation | Für hybride Workforce-Identitäten müssen HR-, AD- und Entra-Objekt eindeutig korreliert sein; unklare oder doppelte Objekte bleiben ein manueller Datenqualitätsfall. |
| Fachlich genehmigter Zielzustand | Eine Workflow-Aufgabe darf nur einen zuvor fachlich genehmigten, begrenzten Zielzustand ausführen. |
| Nachweis und Fehlerpfad | Workflow-Historie, Entra-Audit-Logs und der JML-Fall müssen zusammen auswertbar sein; kritische Entzüge dürfen nicht still fehlschlagen. |

Microsoft dokumentiert ausgewählte eingebaute Aufgaben für Konten, feste Gruppen, Teams, Lizenzen, Access Packages und Benachrichtigungen. Die verfügbaren Aufgaben sind kein Berechtigungsmodell und ersetzen keine Zielsystem- oder Owner-Freigabe. [Microsoft Learn: Workflow-Aufgaben](https://learn.microsoft.com/en-us/entra/id-governance/lifecycle-workflows-deployment)

Für aus AD DS synchronisierte Benutzer können die Kontoaufgaben zum Aktivieren, Deaktivieren und Löschen grundsätzlich verwendet werden. Dies erfordert unter anderem einen Microsoft Entra Provisioning Agent und passende Berechtigungen für die auszuführenden AD-Operationen; für Löschvorgänge gelten weitere Voraussetzungen. GOV-002 legt weder eine Agentenarchitektur noch eine Einführung dieser Voraussetzungen fest. [Microsoft Learn: AD-DS-synchronisierte Benutzer mit Lifecycle Workflows](https://learn.microsoft.com/en-us/entra/id-governance/lifecycle-workflow-on-premises)

## Unterstützte Orchestrierung im Zielmodell

| JML-Schritt | Grundsätzliche Eignung | Leitplanke |
| --- | --- | --- |
| Benachrichtigung an Manager oder Owner | Geeignet, sofern der Empfänger und Anlass fachlich bestätigt sind. | Eine Benachrichtigung ist keine Freigabe und kein Abschlussnachweis. |
| Aktivieren, Deaktivieren oder Löschen eines Entra-Benutzerkontos | Bedingt geeignet für nicht privilegierte, unterstützte Benutzerobjekte. | Für aus AD DS synchronisierte Benutzer können diese Kontoaufgaben grundsätzlich unterstützt werden, benötigen aber zusätzliche Voraussetzungen; privilegierte Identitäten bleiben separat. |
| Hinzufügen zu oder Entfernen aus ausgewählten Cloud-Gruppen | Bedingt geeignet für klar benannte, unterstützte Cloud-Gruppen. | Nur nach genehmigtem Zielzustand; keine automatische Access-Group-Zuweisung allein aus HR- oder Standortattributen. |
| Lizenzaufgaben | Bedingt geeignet für einzeln genehmigte Lizenzprofile. | Lizenzierung ist ein nachgelagerter Prozess und kein Ersatz für Zugriffsfreigaben. |
| TAP-Erzeugung und Benachrichtigung | Nur als kontrollierte Schnittstelle für den Authentication-Bootstrap denkbar. | Die Wahl, Ausgabe, sichere Übermittlung und Registrierung von Methoden bleiben im Authentication- und Supportprozess; kein automatischer Standard mit GOV-002. |
| Access-Package-Aufgaben | Produktseitig vorhanden, aber für Nordstern noch nicht eingeplant. | Entitlement Management und Access Packages sind ein Folgetask; daher keine Nutzung oder Entscheidung in GOV-002. |
| Custom Task Extensions | Außerhalb dieses Designs. | Sie benötigen zusätzliche externe Orchestrierung und Sicherheitsbewertung; keine Logic Apps, APIs oder Automatisierung werden hier entworfen. |

## Joiner

### Zukünftige Startdaten und Aktivierung

Ein zeitbasierter Workflow kann auf einem in Entra verfügbaren Eintrittsdatum aufbauen. Er kann damit eine kontrollierte Vorbereitung oder eine Aktivierung zum Startzeitpunkt unterstützen. Die Verarbeitung erfolgt jedoch nach Workflow-Zeitplanung und setzt verfügbare, korrekte Attribute voraus; sie ist kein Echtzeitnachweis des Arbeitsbeginns. Bei hybriden Identitäten muss eine Änderung zunächst über den gewählten Synchronisationsweg in Entra ankommen. [Microsoft Learn: Attribute und Zeitplanung](https://learn.microsoft.com/en-us/entra/id-governance/how-to-lifecycle-workflow-sync-attributes), [Microsoft Learn: hybride Benutzer](https://learn.microsoft.com/en-us/entra/id-governance/lifecycle-workflow-on-premises)

Der Workflow darf daher keine interaktive Nutzung vor dem in GOV-001 bestätigten Startzeitpunkt freischalten. Vor einem späteren Einsatz sind Datenqualität, Zeitzonen, Zeitplanung, Catch-up-Verhalten und die Behandlung verspätet bereitgestellter Datensätze zu validieren.

| Joiner-Schritt | Möglicher Workflow-Beitrag | Außerhalb bzw. Voraussetzung |
| --- | --- | --- |
| Workforce-Identität erstellen | Kein Standardbeitrag für die Nordstern-Entscheidung. | HR- bzw. Hybrid-Provisioning und Objektkorrelation folgen dem noch offenen Hybrid-Design. |
| Konto aktivieren | Für unterstützte, nicht privilegierte Objekte als mögliche Aufgabe bewertbar. | Fachlicher Startzeitpunkt, technischer Objektstatus und Hybrid-Voraussetzungen müssen bestätigt sein. |
| Persona- oder Site Group | Ausgewählte, fest definierte Cloud-Gruppen können technisch unterstützt werden. | Die Zuordnung muss aus dem bestätigten Zielzustand stammen und darf keine Access Group oder Berechtigung implizieren. |
| Lizenz- oder App-Zuweisung | Lizenzaufgaben sind grundsätzlich möglich. | Jede Lizenz und jede App-Zuweisung benötigt weiterhin ihr eigenes fachliches Zugriffsprofil, Owner und gegebenenfalls Application-Onboarding. |
| Authentication Bootstrap | TAP-bezogene Aufgabe ist produktseitig dokumentiert. | Nur Schnittstelle: TAP bleibt ein kontrolliertes, befristetes Credential; die Methodenregistrierung, Identitätsprüfung und sichere Übergabe folgen [AUTHENTICATION-METHODS-AND-STRENGTHS.md](../security/AUTHENTICATION-METHODS-AND-STRENGTHS.md). |
| Privilegierte Identität oder Rolle | Nicht geeignet. | Separate administrative Identität, Entra-Rollen und PIM folgen dem privilegierten Prozess. |

## Mover

Lifecycle Workflows können bei verfügbaren Attribut- oder Gruppenänderungen ausgewählte, statische Folgeschritte unterstützen. Für Nordstern eignen sie sich allenfalls für kontrollierte Benachrichtigungen, die Entfernung konkret benannter nicht mehr passender Cloud-Gruppen oder genehmigte Lizenzanpassungen. Eine Automatisierung darf nie den fachlichen Mover-Review aus GOV-001 ersetzen.

| Mover-Szenario | Möglicher Workflow-Beitrag | Bewusst außerhalb des Workflow-Scopes |
| --- | --- | --- |
| Lager Nord ↔ Lager Süd | Benachrichtigung und Prüfung festgelegter, nicht berechtigender Site-Groups nach validiertem Standortwert. | Neue Access Groups, Business Roles, lokale Ressourcenrechte und direkte AU-Mitgliedschaften nur durch fachliche Prüfung. |
| Zentrale ↔ Lager / Filiale | Unterstützende Erinnerung an Persona-, Site-, Shared-Device- und Lizenzreview. | Entscheidung über Device-, Session- oder CA-Modell sowie Erteilung neuer App-Zugriffe. |
| Abteilungs- oder Funktionswechsel | Entfernung vorher eindeutig genehmigter Gruppen oder Lizenzen kann geprüft werden. | Keine automatische Zuweisung von Access Groups aus Abteilung, Funktion, Kostenstelle oder Managerattributen. |
| Ende administrativer Aufgabe | Keine privilegierte Entzugsautomatisierung über reguläre Workflow-Aufgaben. | Separate Adminidentität, Entra Directory Roles und künftige PIM-Zuweisungen folgen Security- und Governance-Prozess. |
| AU-Scope | Kein eingebaute Lifecycle-Workflow-Aufgabe wird als AU-Mitgliedschaftsverwaltung vorausgesetzt. | Direkte AU-Mitgliedschaft bleibt ein separater, delegationsbezogener Prozess nach [ADMINISTRATIVE-UNITS.md](../concepts/ADMINISTRATIVE-UNITS.md). |

Alte Zugriffe dürfen nicht einfach fortgeschrieben werden. Ein Workflow kann einen Entzug nicht mehr benötigter, unterstützter statischer Gruppen ausführen, aber die vollständige Prüfung auf Permission Accumulation sowie jede neue Berechtigung verbleiben beim fachlichen Mover-Prozess.

## Leaver

Ein zeitbasierter Leaver-Workflow kann nach einem in Entra verfügbaren Austrittsdatum ausgewählte Offboarding-Aufgaben ausführen. Das bestätigt nicht selbst die fachliche Gültigkeit des Leaver-Ereignisses und ersetzt weder einen fristlosen Security Event noch die JML-Abschlussprüfung.

| Leaver-Schritt | Möglicher Workflow-Beitrag | Grenze |
| --- | --- | --- |
| Sign-in Block / Konto deaktivieren | Die eingebaute Aufgabe zum Deaktivieren eines Benutzerkontos ist grundsätzlich verfügbar. | Für synchronisierte AD-Benutzer kann sie bei erfüllten Voraussetzungen wie Provisioning Agent und passenden Berechtigungen unterstützt werden; Benutzer mit Entra-Rollenzuweisungen oder role-assignable Gruppen werden nicht als unterstützter Standardfall behandelt. |
| Ausgewählte Cloud-Gruppen entfernen | Für unterstützte, fest ausgewählte Cloud-Gruppen bewertbar. | Dynamische, mailaktivierte, Verteilungs- und role-assignable Groups werden nicht als unterstützter Standardfall angenommen. Access-Group-Owner und Vollständigkeitsprüfung bleiben erforderlich. |
| Lizenzen entfernen | Für genehmigte Lizenzprofile als nachgelagerte Aufgabe bewertbar. | Nicht als Ersatz für Sperrung, Rechteentzug, Mailbox- oder Datenübergabe. |
| Sessions und Tokens | Kein eingebaute Lifecycle-Workflow-Aufgabe für den Widerruf von Sitzungen oder Tokens wird mit GOV-002 vorausgesetzt. | Bleibt Teil des technischen Security- und Incident-Prozesses gemäß GOV-001. |
| Authentication Methods | Keine automatische Entfernung oder Wiederherstellung über reguläre Workflows vorsehen. | Behandlung folgt Incident-, Retention- und Forensik-Anforderungen nach Sperrung von Anmeldung sowie Sessions/Tokens. |
| Löschen | Produktseitig als Aufgabe vorhanden, aber nicht als Default vorgesehen. | Löschung erfolgt erst nach Retention-, Rechts- und Governance-Prozessen. |

Mailbox-, Daten-, Ownership-, Geräte- und Ressourcenübergaben bleiben abhängige Prozesse ihrer jeweiligen Owner. Lifecycle Workflows ersetzen weder vollständiges M365- oder Device-Offboarding noch Retention und rechtliche Aufbewahrung.

## Externe Dienstleister

Für externe Dienstleister ist die Eignung erst nach Festlegung des technischen Kollaborationsmodells zu bewerten. Das Vertrags-/Sponsor-System bleibt für Auftrag, Sponsor, Start, Enddatum und Verlängerungen führend. Microsoft Entra dokumentiert Guest Lifecycle Policies für `userType` `Guest` aktuell als Preview. Daraus wird für Nordstern keine B2B-, Cross-Tenant- oder Guest-Lifecycle-Entscheidung und keine produktive Nutzung abgeleitet. [Microsoft Learn: Guest Lifecycle Policies](https://learn.microsoft.com/en-us/entra/id-governance/guest-lifecycle-policies)

Das bestätigte externe Enddatum bleibt gemäß GOV-001 das maximale genehmigte Zugriffsende. Eine Lifecycle-Workflow- oder Guest-Policy-Funktion darf keine implizite Grace Period erzeugen. Eine später erforderliche Grace Period wäre eine dokumentierte Governance-Ausnahme mit Owner, Begründung und Ablaufdatum und benötigt eine gesonderte Validierung.

## Privilegierte Identitäten

Reguläre Lifecycle Workflows dürfen keine Entra Directory Roles, PIM-Zuweisungen oder separaten privilegierten Identitäten erzeugen, erweitern oder entziehen. Die eingebaute Kontoaufgabe ist für Benutzer mit Entra-Rollenzuweisungen oder role-assignable Gruppen kein unterstützter Standardfall. Privilegierte Identitäten bleiben deshalb außerhalb der regulären Workflow-Automatisierung und folgen einem separaten Security- und Governance-Prozess. [Microsoft Learn: Einschränkung der Kontenaufgabe](https://learn.microsoft.com/en-us/entra/id-governance/lifecycle-workflow-tasks)

Ein Workforce-Leaver muss weiterhin die vollständige Prüfung aller verknüpften Adminidentitäten, Rollen und künftigen PIM-Zuweisungen auslösen. PIM bleibt ein separater Architektur- und Governance-Baustein; GOV-002 definiert keine PIM-Architektur.

## Emergency Access

Emergency-Access-Identitäten werden ausdrücklich nicht durch reguläre Lifecycle Workflows gesteuert. Es gibt kein automatisches Disable, Delete, Method Removal oder eine reguläre Workflow-Aufgabe für diese Konten. Änderungen von Custodians, Verantwortlichkeiten, Verwahrung, Faktoren oder Notfallverfahren folgen ausschließlich [EMERGENCY-ACCESS.md](../security/EMERGENCY-ACCESS.md) und [ADR-0008](../adr/0008-emergency-access-break-glass-konzept.md).

## Fehlerbehandlung, Audit und Betrieb

| Kontrolle | Anforderung |
| --- | --- |
| Idempotenz | Jede künftige Workflow-Ausführung muss gegen einen fachlich genehmigten Zielzustand und eine stabile JML-Vorgangskennung geprüft werden. Wiederholungen dürfen keine zusätzlichen Gruppen, Lizenzen oder Rechte erzeugen. |
| Retry | Produktverhalten und Wiederholungsregeln für jede verwendete Aufgabe müssen vor Einsatz validiert werden. Ein Retry darf erst erfolgen, wenn der aktuelle Zielzustand und die Fehlerursache geprüft sind. |
| Kritische Entzüge | Fehlt der Nachweis für einen Sign-in Block, einen erwarteten Gruppenentzug oder eine andere kritische Leaver-Aufgabe, darf der Fall nicht still als erfolgreich weiterlaufen. Identity Operations und Security werden manuell eskaliert. |
| Audit Trail | Workflow-Historie und Entra-Audit-Logs ergänzen den JML-Fall um Ausführungs- und Aufgabenstatus. Sie ersetzen nicht die fachliche Quelle, Freigabe oder Abschlusskontrolle. [Microsoft Learn: Workflow-Historie](https://learn.microsoft.com/en-us/entra/id-governance/lifecycle-workflow-history) |
| Rollout | Vor einer breiteren Nutzung sind nichtproduktiver Test, begrenzter Pilot, On-demand-Auswertung, Fehlerpfade und Rollback je Workflow zu validieren. |

## Grenzen von Lifecycle Workflows

Lifecycle Workflows ersetzen nicht:

- HR-Stammdaten, fachliche Beschäftigungsentscheidungen oder die Source of Authority;
- das externe Vertrags-, Sponsor- und Verlängerungsmanagement;
- die Erstellung, Korrelation, OU-Steuerung oder komplexe Provisionierung von AD-/Hybridobjekten;
- vollständige App- und SaaS-Provisionierung, sofern kein separat entworfenes und abgesichertes Zielsystemmodell besteht;
- die fachliche Genehmigung, Ownership und Access Reviews von Access Groups;
- direkte AU-Mitgliedschaften und AU-gescopte Administrationsrollen;
- PIM, Entra Directory Roles und privilegierte Identitätslifecycles;
- Emergency Access einschließlich Faktoren, Custodians und CA-Ausnahmen;
- Session-/Token-Widerruf, Authentication-Method-Recovery sowie Incident- und Forensikprozesse;
- Mailbox-, Daten-, Ownership-, Geräte-, Ressourcen- und Retention-Offboarding.

## Offene Fragen und Abhängigkeiten

- Lizenz-, Editions- und gegebenenfalls Guest-Abrechnungsanforderungen für Lifecycle Workflows.
- Verfügbarkeit, Qualität, Zeitzone und Synchronisationslatenz von Eintritts-, Austritts-, Standort-, Organisations- und Scoping-Attributen in Entra.
- Konkrete Auswahl und Ownership zulässiger statischer Cloud-Gruppen, Lizenzprofile und Benachrichtigungen.
- Unterstützungsgrenzen von Konto-, Gruppen- und Lizenzaufgaben für die tatsächlichen hybriden, synchronisierten und privilegierten Objektklassen.
- Abgleich zwischen Workflow-Historie, Entra-Audit-Logs und dem JML-Vorgang sowie Monitoring- und Eskalationsverantwortung.
- Externes Kollaborationsmodell sowie Eignung, Preview-Status und Betriebsreife von Guest Lifecycle Policies für die später gewählte Identitätsart.
- PIM-, Access-Review-, Entitlement-Management-, Device-, Mailbox-, Daten- und Retention-Designs.
- Ob und welche Custom Task Extensions nach einer separaten Security- und Integrationsentscheidung erforderlich wären.
