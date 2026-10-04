# Joiner / Mover / Leaver

Status: **Entwurf (GOV-001)**

## Ziel und Abgrenzung

Dieses Dokument definiert das fachliche Identity-Lifecycle-Modell der Nordstern Handelsgruppe für Workforce-, externe, privilegierte und Emergency-Access-Identitäten. Es ordnet fachliche Auslöser, Kontrollpunkte und erwartete Wirkungen zu. Es erstellt keine Lifecycle Workflows, keine Entra-Konfiguration, keine Synchronisationsregeln und keine Automatisierung.

Das Modell setzt die Attributautorität aus dem [Source-of-Authority-Modell](../architecture/SOURCE-OF-AUTHORITY.md), die Trennung der Gruppenfamilien aus dem [Gruppenmodell](../concepts/GRUPPENMODELL.md) und die AU-Abgrenzung aus [Administrative Units](../concepts/ADMINISTRATIVE-UNITS.md) voraus. Die Architekturentscheidung ist in [ADR-0010](../adr/0010-joiner-mover-leaver-lifecycle-modell.md) mit Status `Proposed` dokumentiert. Die Bewertung von Microsoft Entra Lifecycle Workflows als möglichem Ausführungsbaustein steht in [LIFECYCLE-WORKFLOWS.md](LIFECYCLE-WORKFLOWS.md); GOV-001 bleibt die fachliche Quelle für Auslöser, Freigaben und Kontrollen.

## Grundsätze

1. **Fachlicher Auslöser vor technischer Änderung:** Ein Lifecycle-Ereignis entsteht aus einer bestätigten Änderung in der jeweils führenden Quelle. Interne Workforce-Ereignisse kommen aus dem generischen HR-System, externe Ereignisse aus dem noch nicht konkret benannten Vertrags-/Sponsor-System.
2. **Eine Schreibautorität je Attribut:** Widersprüche werden an die führende Quelle zurückgegeben. Es gibt keine „Last writer wins“-Behandlung und keine automatische Zusammenführung unklarer Identitäten.
3. **Identität ist nicht Berechtigung:** Persona-, Standort- und Business-Role-Zuordnungen werden getrennt von Access Groups, Entra Directory Roles und Anwendungszuweisungen geprüft. Standort- oder HR-Attribute erzeugen allein keine Access-Group-Mitgliedschaft.
4. **Delegation ist nicht Zugriff:** Direkte AU-Mitgliedschaften werden ausschließlich als Scope für delegierte Administration bewertet. Sie verleihen weder Ressourcenrechte noch folgen sie aus einer Security-Group-Mitgliedschaft.
5. **Entzug ist nachweisbar:** Ein Leaver- oder Rollenentzugsereignis wird erst geschlossen, wenn die vorgesehenen technischen Entzüge, abhängigen Prüfungen und der Audit-Nachweis bestätigt sind.
6. **Ausnahmen sind befristet:** Jede Abweichung benötigt Owner, Begründung, Freigabe, Ablaufdatum und Review. Eine Ausnahme darf die führende Quelle nicht dauerhaft übersteuern.

## Prozessrahmen und Verantwortlichkeiten

Jedes Ereignis wird als nachverfolgbarer Lifecycle-Fall mit einer stabilen Vorgangskennung geführt. Die Fallbearbeitung trennt fachliche Datenpflege, Freigabe, technische Ausführung und unabhängige Kontrolle.

| Prozessrolle | Verantwortung | Trennung und Abgrenzung |
| --- | --- | --- |
| Führende Fachquelle | Beschäftigungs-, Organisations-, Standort- oder Vertragsdaten korrekt führen. | Erteilt weder Access Groups noch Entra-Rollen allein durch ein Attribut. |
| Fachlicher Owner bzw. Sponsor | Persona, fachliche Funktion und erforderlichen Zugriff bestätigen; bei Externen Auftrag und Laufzeit bestätigen. | Genehmigt nicht allein die eigene technische Umsetzung. |
| Access-Group-Owner | Zugriff auf eine konkrete Anwendung oder Ressource fachlich verantworten. | Prüft und genehmigt nur das eigene Zugriffsprofil. |
| Identity Operations | Lifecycle-Fall validieren, technische Schritte koordiniert ausführen oder überwachen und Fehler eskalieren. | Darf fachliche Stammdaten nicht in einer nicht führenden Quelle umdeuten. |
| Security bzw. privilegierter Owner | Privilegierte Identitäten, Entra-Rollen, kritische Ausnahmen und Sicherheitsereignisse prüfen. | Muss von Anforderung und regulärer Ausführung angemessen getrennt sein. |
| AU-/Support-Owner | Bedarf einer direkten AU-Mitgliedschaft für delegierte Administration bestätigen. | Eine AU-Entscheidung ersetzt keine Access-Group-Freigabe. |
| Emergency-Access-Verantwortliche | Custodians, Verwahrung, Tests und Notfallverfahren gemäß CA-003 kontrollieren. | Gehören nicht zum normalen Workforce-JML-Approval- oder Deprovisioning-Pfad. |

Die konkrete personelle Besetzung, das Vier-Augen-Prinzip je Vorgangsklasse sowie verbindliche Fristen bleiben Governance-Entscheidungen. Bei unmittelbar notwendigen Security Events gelten die in diesem Dokument beschriebenen Eskalationswege; Emergency-Access-Overrides richten sich ausschließlich nach [EMERGENCY-ACCESS.md](../security/EMERGENCY-ACCESS.md).

## Joiner – interne Workforce

### Fachlicher Auslöser und Prüfung

Ein Joiner beginnt mit einem bestätigten, aktiven Beschäftigungsdatensatz im HR-System. Personenkern, Beschäftigungsstatus, Eintrittsdatum, Organisation sowie – soweit vorhanden – Standort und Filialcode werden fachlich geprüft. Die Identitätsprüfung bleibt ein HR- bzw. Onboarding-Prozess; dieses Dokument führt kein neues Identitätsnachweisverfahren ein.

Ein Eintrittsdatum in der Zukunft darf eine kontrollierte Vorbereitung auslösen. Eine vorbereitete Workforce-Identität bleibt bis zum bestätigten Startzeitpunkt ohne interaktive Nutzbarkeit; vor der Aktivierung sind Status, Persona, Standort und etwaige Genehmigungen erneut zu prüfen. Vorbereitende Identitäts- oder Kontoerstellung ersetzt weder den Beschäftigungsbeginn noch eine Zugriffsfreigabe.

### Bereitstellung und Zuordnung

| Schritt | Fachliche Wirkung und Abgrenzung |
| --- | --- |
| Workforce-Identität bereitstellen | AD wird nur einbezogen, wenn ein technisch erforderliches On-Premises-Konto besteht; Entra führt Cloud-Identitäts- und Zugriffsattribute. Ob und wie ein Objekt synchronisiert oder cloud-only erstellt wird, folgt dem noch offenen technischen Hybrid-Design. |
| Persona und Standort prüfen | Office User, Warehouse User oder Store User sowie Zentrale Hamburg, Lager Nord, Lager Süd oder Filiale werden aus bestätigten fachlichen Daten bestimmt. Persona- und Site Groups können kontrolliert zugeordnet werden; sie verleihen keinen Zugriff. |
| Business Role bewerten | Eine bestätigte fachliche Funktion kann eine Business-Role-Prüfung auslösen. Sie erzeugt weder eine Application Role noch eine Access Group. |
| Access Groups prüfen | Lizenz-, App- und Ressourcenzuweisungen sind nachgelagerte, zielsystem- und ownerbezogene Prozesse. Sie werden nicht allein aus HR-, Persona- oder Standortattributen erteilt. |
| AU-Mitgliedschaft prüfen | Für Lager Nord, Lager Süd und Filialen kann eine direkte Aufnahme in `AU-Warehouse-North`, `AU-Warehouse-South` oder `AU-Stores` geprüft werden, sofern der delegierte Support das Objekt verwalten muss. Für Zentrale Hamburg besteht keine Standard-AU. |
| Authentication Bootstrap durchführen | Vor der Aktivierung oder beim ersten kontrollierten Zugriff muss eine zugelassene Methode registriert werden. Ein Temporary Access Pass kann als kontrolliertes, befristetes Bootstrap-Credential dienen; er ist kein Dauerverfahren und keine Ziel-Authentication-Strength. [CA-BL-004](../security/CONDITIONAL-ACCESS-BASELINE.md) und [Authentication Methods & Authentication Strengths](../security/AUTHENTICATION-METHODS-AND-STRENGTHS.md) geben den Rahmen vor. |
| Aktivieren und bestätigen | Die interaktive Anmeldung wird erst zum bestätigten Startzeitpunkt und nach erfolgreicher Identitäts-, Methoden- und erforderlicher Zugriffsprüfung freigegeben. Die technische Umsetzung und ihre Propagationszeiten sind offen. |

Für Warehouse User in Lager Nord und Lager Süd sowie Store User in Filialen bleibt die Anmeldung personengebunden. Shared Devices, Scanner oder der Schichtbetrieb begründen weder eine gemeinsame Identität noch eine vorgezogene Ausnahme von Authentication oder Conditional Access.

## Mover – Standort, Organisation und Funktion

Ein Mover ist jede bestätigte Änderung von Standort, Filialcode, Organisation, Abteilung, Kostenstelle, Manager, fachlicher Funktion oder Beschäftigungsstatus, die die bisherige Zuordnung beeinflussen kann. Der Fall wird anhand eines stabilen Identitätsbezugs geöffnet; doppelte oder nicht korrelierbare Objekte werden nicht automatisch zusammengeführt.

| Mover-Szenario | Pflichtprüfung | Nicht zulässige Ableitung |
| --- | --- | --- |
| Lager Nord ↔ Lager Süd | Site Group sowie direkte AU-Mitgliedschaft auf den neuen und bisherigen Standort prüfen; lokale Gruppen- und Ressourcenbezüge bewerten. | Der Standortwechsel verleiht keine Business Role, Access Group oder Entra-Rolle. |
| Zentrale ↔ Lager oder Filiale | Persona, Site Group, Shared-Device-Kontext und gegebenenfalls AU-Scope neu bewerten. | Der Wechsel erzeugt keine automatische Anwendungs- oder Administratorberechtigung. |
| Filiale ↔ Filiale | Bisherige und neue Site Group sowie standortgebundene Ressourcenzugriffe prüfen. | Eine Filialzuordnung ist kein Ersatz für ein genehmigtes Zugriffsprofil. |
| Abteilungs- oder Funktionswechsel | Business Role und alle bestehenden Access Groups gegen die neue Aufgabe prüfen. | HR-Attribute dürfen Access Groups nicht ohne Antrag, Owner-Prüfung und Genehmigung zuweisen. |
| Ende administrativer Aufgabe | Separate privilegierte Identität, Entra-Rollen und künftige PIM-Berechtigungen prüfen und entziehen. | Die Zugehörigkeit zu einer Workforce- oder Persona Group verlängert keine privilegierte Funktion. |

Alte Zugriffe werden vor oder zusammen mit der wirksamen Vergabe neuer Zugriffe entzogen. Zeitliche Überschneidungen sind nur als dokumentierte Ausnahme mit fachlichem Owner, Begründung und Ablaufdatum zulässig. Jeder Mover enthält eine explizite Prüfung auf Permission Accumulation: Nicht mehr passende Access Groups, Business Role Groups, direkte AU-Mitgliedschaften und privilegierte Zuweisungen sind zu entfernen oder begründet zu bestätigen.

Ein Mover kann eine Neubewertung der Authentication- und Geräteanforderungen auslösen, etwa beim Wechsel in einen Shared-Device-Kontext. Er entscheidet jedoch kein Device-, Sitzungs- oder Conditional-Access-Modell und erzeugt keine neue CA-Policy.

## Leaver – Workforce und Zugriffsende

### Auslöser und Priorisierung

Ein Leaver wird durch ein Austritts-, Inaktivitäts- oder Entzugsereignis in der führenden fachlichen Quelle ausgelöst. Für geplante Austritte gilt das bestätigte Enddatum; für fristlose oder sicherheitskritische Fälle wird der Fall als sofortige Deaktivierung priorisiert. Der Lifecycle-Fall dokumentiert Quelle, Wirksamkeitszeitpunkt, Grundkategorie und alle abhängigen Identitäten, ohne sensible Personaldetails in dieses Repository zu übernehmen.

| Schritt | Geplanter Leaver | Fristloser bzw. sofortiger Leaver |
| --- | --- | --- |
| Interaktive Anmeldung | Zum bestätigten Austrittszeitpunkt sperren. | Unverzüglich sperren; Security und Identity Operations eskalieren. |
| Sessions und Tokens | Relevante Sitzungen und Tokens unmittelbar nach der Sperrung im vorgesehenen technischen Prozess beenden bzw. widerrufen und die Wirkung prüfen. | Unverzüglich auslösen und nachweisbar prüfen, soweit die betroffenen Plattformen dies unterstützen. |
| Privilegierte Identitäten | Alle verknüpften separaten Adminidentitäten, Rollen und künftigen PIM-Zuweisungen prüfen und zum Wirksamkeitszeitpunkt entziehen. | Vor oder zeitgleich mit der Workforce-Sperrung sperren bzw. entziehen; kein Warten auf den regulären Prozess. |
| Gruppen und Zugriffe | Access Groups entfernen oder durch Owner prüfen; Persona-, Site- und Business-Role-Gruppen nach Retention- und Prozessbedarf bereinigen. | Nicht benötigte Access Groups und privilegierte Gruppen unverzüglich entziehen; verbleibende Mitgliedschaften als Ausnahme prüfen. |
| AU-Scope | Direkte AU-Mitgliedschaften entfernen, sobald keine delegierte Verwaltung des Objekts mehr erforderlich ist. | Ebenso entfernen; AU-Mitgliedschaft ist keine Zugriffssteuerung und ersetzt keinen Rechteentzug. |
| Authentication Methods | Erst nach Sperrung der interaktiven Anmeldung sowie nach Behandlung von Sessions und Tokens entsprechend Incident-, Retention- und Forensik-Anforderungen entfernen, sperren oder für die Nachweisführung erhalten. | Für kompromiss- oder missbrauchsrelevante Fälle zusätzlich gemäß Security-Event-Prozess behandeln; keine pauschale sofortige Löschung. |

Geräte, lokale Ressourcen, Mailboxen, Daten, Dokumente, Application Ownership und Vertretungen werden als abhängige Offboarding-Prozesse über ihre jeweiligen Owner behandelt. GOV-001 definiert hierfür nur die Übergabeschnittstelle und kein vollständiges M365-, Endpoint- oder Daten-Offboarding. Eine Identität wird erst nach definierten Retention-, Rechts- und Governance-Prozessen gelöscht; eine Deaktivierung ist keine Löschung.

## Externe Dienstleister

Externe Dienstleister folgen einem eigenen, auftragsspezifischen Lifecycle. Die führende Fachquelle ist das noch nicht konkret benannte Vertrags-/Sponsor-System; es liefert mindestens Auftrag, Sponsor sowie Start- und Enddatum. Das bestätigte Enddatum ist grundsätzlich das maximale genehmigte Zugriffsende; eine Verlängerung muss vor seinem Ablauf bestätigt werden. Die konkrete Guest-, B2B-, Cross-Tenant- oder andere technische Zugriffsform bleibt offen.

| Ereignis | Pflichtwirkung |
| --- | --- |
| Start eines Auftrags | Sponsor, Auftrag, Laufzeit und erforderliche Access Groups fachlich prüfen; das technische externe Zugriffsobjekt erst danach nach dem späteren Kollaborationsmodell bereitstellen. |
| Änderung von Auftrag oder Sponsor | Alle Access Groups und Ressourcenzuweisungen gegen den neuen Bedarf prüfen; keine Übernahme interner Persona-, Site- oder Administratorrechte. |
| Regelmäßige Bestätigung | Sponsor bestätigt Auftrag, Laufzeit und weiterhin erforderliche Zugriffe in einem noch zu definierenden Intervall. Eine Verlängerung muss vor dem bestätigten Enddatum vorliegen. |
| Enddatum oder fehlende Sponsorbestätigung | Ohne vor Ablauf bestätigte Verlängerung endet der genehmigte Zugriff zum bestätigten Enddatum; es entsteht keine implizite Grace Period. Eine später benötigte Grace Period ist ausschließlich eine explizite, dokumentierte Governance-Ausnahme mit Owner, Begründung und Ablaufdatum. Die Identität wird nicht automatisch gelöscht. |

TAP, Authentication-Methoden, Home-Tenant-Steuerung und Conditional-Access-Behandlung externer Personen werden erst nach Entscheidung über das externe Kollaborationsmodell festgelegt.

## Privilegierte Identitäten

Eine privilegierte Administratoridentität besitzt einen separaten Lifecycle zusätzlich zur Workforce-Identität. Ihre Erstellung, Änderung und Fortführung benötigen eine bestätigte aktive beziehungsweise genehmigte Workforce-Funktion **und** eine gesonderte administrative Aufgabenfreigabe. Eine HR-Änderung darf keine privilegierte Identität, Rolle oder Access Group automatisch erzeugen oder erweitern.

Die administrative Identität darf nicht länger bestehen als die genehmigte administrative Funktion. Bei Funktionsentzug, Wechsel oder Workforce-Leaver werden die separate Identität, alle Entra Directory Roles, potenziell PIM-eligible oder aktive Zuweisungen, administrative Access Groups sowie registrierte Methoden nach dem privilegierten Prozess überprüft. Rollenmodell, PIM-Design, konkrete Entzugsschritte und zeitliche Aktivierung bleiben den Folgetasks vorbehalten.

Jeder Workforce-Leaver löst zwingend eine vollständige Prüfung aller mit der Workforce-Funktion verknüpften privilegierten Identitäten aus. Ein fehlender Bezug oder eine fortbestehende privilegierte Identität ist ein Security Finding und wird bis zur Klärung als sicherheitsrelevante Ausnahme behandelt.

## Emergency Access

Emergency-Access-Identitäten sind kein Teil des normalen HR-gesteuerten Joiner-, Mover- oder Leaver-Prozesses. Eine Änderung von Custodians, Verantwortlichkeiten, Verwahrung, Faktoren oder Notfallverfahren löst einen kontrollierten Review nach [EMERGENCY-ACCESS.md](../security/EMERGENCY-ACCESS.md) aus.

Ein Workforce-Leaver eines Custodians darf das Emergency-Access-Konto weder automatisch deaktivieren noch löschen. Stattdessen wird die Verantwortlichkeit sofort überprüft, ein neuer Custodian nach dem Notfallverfahren bestellt und die Verwahrungs-, Test- und Auditpflichten werden aktualisiert. Die Konten, ihre Entra-Rollen und ihr separat validierter Methoden- und Faktor-Scope bleiben von AD, Hybrid Sync, Föderation, PIM und regulären Workforce-Faktoren getrennt, sind jedoch technisch weiter von Microsoft Entra Authentication und den für ihren Scope zugelassenen Methoden abhängig.

## Security Events und Sonderfälle

| Ereignis | Abgrenzung und Sofortmaßnahme | Nachgelagerte Kontrolle |
| --- | --- | --- |
| Regulärer geplanter Leaver | Fachlich bestätigtes Enddatum; geplante Sperrung und Entzüge zum Wirksamkeitszeitpunkt. | Vollständigkeit von Access-Group-, AU-, Admin-, Session- und Methodenprüfungen nachweisen. |
| Fristlose / sofortige Deaktivierung | Workforce-Anmeldung unverzüglich sperren; privilegierte Zugriffe vor oder zeitgleich entziehen. | Sicherheitsbewertung, Stakeholder-Eskalation und Nachweis der technischen Wirkung. |
| Kompromittierte Identität | Security Incident, kein regulärer Leaver; betroffenen Zugang sperren bzw. absichern und Incident-Prozess aktivieren. | Faktor-, Session-, Token-, Gruppen- und Rollenprüfung sowie kontrollierte Wiederherstellung. |
| Verlorener Faktor | Authentication-Recovery-Ereignis, kein Leaver; Identität über getrennten Prozess prüfen. | Alten Faktor entfernen oder sperren und neue starke Methode gemäß [Authentication Methods & Authentication Strengths](../security/AUTHENTICATION-METHODS-AND-STRENGTHS.md) registrieren. |
| Administrativer Rollenentzug | Entzug oder Wechsel einer administrativen Aufgabe; separate Adminidentität und Rollen prüfen. | Entzug bestätigen; verbliebene privilegierte Zuweisungen und PIM-Abhängigkeiten prüfen. |

## Prozesskontrollen und Fehlerbehandlung

| Kontrolle | Anforderung |
| --- | --- |
| Separation of Duties | Datenpflege, fachliche Freigabe, technische Umsetzung und Auditkontrolle sind nach Risiko angemessen getrennt. Kritische privilegierte oder externe Zugriffe dürfen nicht allein durch dieselbe Person angefordert und freigegeben werden. |
| Approval und Ownership | Access-Group-, AU- und privilegierte Änderungen haben einen benannten Owner und eine nachvollziehbare Freigabe. Emergency Access folgt seinem eigenen Vier-Augen- und Override-Verfahren. |
| Audit Trail | Jeder Fall enthält Auslöser, führende Quelle, Vorgangskennung, Zeitpunkte, angeforderte und ausgeführte Änderungen, Approvals, Ausnahmen, Fehler sowie Abschlussnachweis. |
| Datenkonflikte | Bei unklaren, doppelten oder widersprüchlichen Daten keine Berechtigungsänderung durch „Last writer wins“. Der Fall wird angehalten, an die führende Quelle zurückgegeben und manuell geklärt. |
| Fehlerbehandlung | Teilfehler werden sichtbar dokumentiert; nicht kritische Folgeschritte werden nicht als abgeschlossen markiert. Sicherheitsrelevante Entzüge werden priorisiert und eskaliert. |
| Retry und Eskalation | Eine spätere Automatisierung muss idempotent anhand der Vorgangskennung und des gewünschten Zielzustands arbeiten. Wiederholungen dürfen keine doppelten Berechtigungen erzeugen; nach noch festzulegender Fehlergrenze ist eine manuelle Eskalation erforderlich. |

## Offene Governance-Werte und Abhängigkeiten

Die folgenden Werte sind bewusst **nicht** festgelegt und benötigen Governance-, Security- und Betriebsfreigabe:

- maximale Vorbereitungszeit für Joiner mit zukünftigem Startdatum;
- Frist für Aktivierung zum Startdatum und für die vollständige Joiner-Nachkontrolle;
- Frist für Mover-Review und den Entzug nicht mehr benötigter Zugriffe;
- Frist für geplante Leaver sowie Reaktionszeit für fristlose Deaktivierungen;
- Intervall und Eskalationsfrist für externe Sponsorbestätigungen;
- Frist für den Entzug privilegierter Identitäten nach Funktions- oder Workforce-Ende;
- Retention-, Lösch- und Nachweisfristen für Identitäten, Gruppenmitgliedschaften, Methoden und Auditdaten;
- Zielwerte für Retry, manuelle Eskalation und technische Fehlerbehebung;
- erforderliche Servicezeiten und Eskalationswege je Standort, insbesondere Lager Nord, Lager Süd und Filialen.

Weitere Abhängigkeiten sind das konkrete HR- und Vertrags-/Sponsor-System, die Hybrid-Synchronisation, Datenqualität und Korrelation, das externe Kollaborationsmodell, Device- und Session-Design, PIM, Access Reviews, Entitlement Management sowie die technischen Möglichkeiten der jeweiligen Zielsysteme.
