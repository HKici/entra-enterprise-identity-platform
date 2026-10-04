# Emergency-Access-Design

Status: **Entwurf (CA-003)**

## Zweck und Einsatzgrenzen

Emergency-Access-Identitäten (Break-Glass) stellen ausschließlich die letzte Wiederherstellungsoption für den Microsoft-Entra-Tenant der Nordstern Handelsgruppe dar. Sie dürfen verwendet werden, wenn reguläre privilegierte Identitäten den Tenant nicht mehr verwalten können, etwa durch fehlerhafte Conditional-Access-Policies, Ausfall eines regulären Authentifizierungswegs, einen PIM- oder privilegierten Zugriffsfehler oder den Verlust regulärer Administratorzugänge.

Sie sind kein Ersatz für tägliche Administration, lokale Standortunterstützung, reguläre PIM-Aktivierung, Anwendungssupport oder On-Premises-Notfallzugriff. Ein vollständiger Ausfall von Microsoft Entra ID oder der notwendigen Internetverbindung kann durch Entra-Emergency-Access nicht behoben werden; dafür sind getrennte Betriebs- und Resilienzverfahren erforderlich.

Das Konzept enthält keine produktiven Konten, Credentials, Objektkennungen oder Tenant-Daten. Die Architekturentscheidung ist in [ADR-0008](../adr/0008-emergency-access-break-glass-konzept.md) mit Status `Proposed` dokumentiert.

## Vorgeschlagenes Kontenmodell

Nordstern betreibt **initial zwei** getrennte Emergency-Access-Konten und hält mindestens zwei Konten vor. Zwei Konten schaffen Redundanz, ohne die Anzahl hochprivilegierter Dauerberechtigungen unnötig zu erhöhen. Jede Änderung der Kontenanzahl erfordert einen separaten Security-Review.

| Eigenschaft | EA-01 | EA-02 | Gemeinsame Regel |
| --- | --- | --- | --- |
| Identität | Eigenständige Emergency-Access-Identität | Eigenständige Emergency-Access-Identität | Keine Nutzung als Workforce-, Service-, Gast- oder reguläre Administratoridentität. |
| Bereitstellung | Cloud-only in Microsoft Entra ID | Cloud-only in Microsoft Entra ID | Nicht aus On-Premises AD, HR, Entra Connect Sync oder Cloud Sync erstellt oder geändert. |
| Anmeldeweg | Nicht über einen föderierten Unternehmens- oder externen Identity Provider | Nicht über einen föderierten Unternehmens- oder externen Identity Provider | Der Cloud-Anmeldeweg darf nicht von AD, Hybrid Sync, Föderation oder dem regulären Workforce-Lifecycle abhängen. |
| Rolle | Global Administrator, dauerhaft aktiv | Global Administrator, dauerhaft aktiv | Keine PIM-Eligible-Zuweisung als Voraussetzung für Notfallnutzung; keine zusätzlichen Dauerrollen. |
| Verwahrung | Getrennte Zugangsmittel und Verwahrort A | Getrennte Zugangsmittel und Verwahrort B | Kein gemeinsamer Schlüssel, kein gemeinsames Gerät, keine gemeinsame Mobilnummer, kein gemeinsamer Verwahrort und keine alleinige Kenntnis durch dieselbe Person. |

Die dauerhafte aktive Global-Administrator-Rolle ist für den eng begrenzten Wiederherstellungszweck vorgesehen, weil der PIM- oder Rollenaktivierungsweg selbst Teil eines Notfalls sein kann. Die Konten erhalten keine AU-basierte Rolle, keine Access-Group-Zuweisung und keine Berechtigungen für tägliche Betriebsaufgaben. Microsoft empfiehlt mindestens zwei cloud-only Konten und für Emergency Access eine dauerhaft aktive Global-Administrator-Zuweisung. [Microsoft Learn: Emergency Access](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/security-emergency-access)

## Authentifizierung und unabhängige Zugangsmittel

Emergency Access ist nicht „ungeschützt“. Die Konten benötigen einen besonders kontrollierten, phishing-resistenten Anmeldeweg, der von den regulären Administratorverfahren und voneinander möglichst unabhängig ist.

| Verfahren | Eignung für Emergency Access | Abhängigkeiten und Anforderungen |
| --- | --- | --- |
| FIDO2-Sicherheitsschlüssel | Phishing-resistente Option; kein Mobilfunk- oder persönliches Endgerät als notwendiger zweiter Faktor. | Je Konto eigene physische Schlüssel, getrennte Verwahrung und Funktionsprüfung. Verlust, Defekt, PIN-Verwaltung und Hersteller-/Firmware-Risiken sind im Testprozess zu berücksichtigen. |
| Zertifikatsbasierte Authentifizierung | Potenziell phishing-resistente Alternative mit anderer Faktorart. | Nur geeignet, wenn Zertifikatsausstellung, Vertrauenskette, Sperrung und Ablauf für den Notfall unabhängig von AD, On-Premises-PKI und dem jeweils anderen Konto verfügbar sind. Ohne diesen Nachweis bleibt sie ein offener Kandidat. |
| Mobiltelefon, SMS oder Anruf | Kann als zusätzliche Benachrichtigung oder untergeordnete Redundanz betrachtet werden. | Nicht als alleiniger Emergency-Access-Faktor: Mobilfunk, Gerät und Nummer können ausfallen oder kompromittiert sein; das Verfahren ist nicht phishing-resistent. |
| Passwortbasierter Anmeldeweg | Kann abhängig vom final validierten Entra-Anmeldemodell technisch erforderlich sein. | Kein alleiniger Schutz. Passwortmaterial, Wiederherstellungsdaten und ein möglicher zusätzlicher Faktor müssen getrennt verwahrt und gegen die gleichen Abhängigkeiten geprüft werden. |

Für **jedes** Konto wird vor Einführung mindestens ein phishing-resistentes Verfahren ausgewählt und in einer kontrollierten Probe erfolgreich getestet. EA-01 und EA-02 dürfen nicht dasselbe physische Zugangsmittel, dieselbe Mobilfunkabhängigkeit, denselben Verwahrort, dieselbe alleinige Person oder denselben ungeprüften Vertrauensanker voraussetzen. Die konkrete Kombination wird erst nach Kompatibilitäts- und Ausfalltests bestimmt; damit wird keine allgemeine Authentication-Strength-Architektur für Workforce- oder reguläre Administratoridentitäten festgelegt.

Microsoft nennt FIDO2-Sicherheitsschlüssel und zertifikatsbasierte Authentifizierung als phishing-resistente Beispiele und empfiehlt, für Emergency Access Verfahren einzusetzen, die sich von normalen Administratorkonten unterscheiden. [Microsoft Learn: Emergency Access](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/security-emergency-access)

Das allgemeine Methoden- und Strength-Modell für Workforce, privilegierte und externe Identitäten steht in [AUTHENTICATION-METHODS-AND-STRENGTHS.md](AUTHENTICATION-METHODS-AND-STRENGTHS.md). Emergency Access besitzt einen separat validierten Methoden- und Faktor-Scope: Es übernimmt keine normalen Workforce-Bootstrap- oder Recovery-Flows; seine Faktoren und deren Lifecycle sind von regulären Administratoridentitäten getrennt. Die Konten bleiben technisch von Microsoft Entra Authentication und den für ihren Scope zugelassenen Methoden abhängig, jedoch nicht von AD, Hybrid Sync, Föderation, PIM oder regulären Workforce-Faktoren.

## Verwahrung, Freigabe und Vier-Augen-Prinzip

Zugangsmittel, Kontenbezeichnungen und konkrete Verwahrinformationen werden nicht in diesem Repository dokumentiert. Sie werden in einem zugriffsbeschränkten Emergency-Access-Register geführt.

Das Register enthält mindestens den Kontozweck, den getrennten Verwahrort, die verantwortlichen Custodians, zulässige Auslöser, die zuletzt erfolgreiche Prüfung, den Ablaufstatus von Zugangsmitteln sowie den Verweis auf Incident- und Post-Mortem-Nachweise. Es enthält keine Secrets.

Für die Nutzung gelten folgende Regeln:

1. Ein Incident Lead dokumentiert Auslöser, Ziel und die Notwendigkeit des Emergency Access in einem Incident- oder Recovery-Vorgang.
2. Eine zweite, unabhängige berechtigte Person bestätigt die Freigabe außerhalb des gestörten Zugriffswegs. Diese Person darf nicht alleiniger Custodian desselben Zugangsmittels sein.
3. Ein Custodian gibt nur das für den konkreten Vorgang erforderliche, getrennt verwahrte Zugangsmittel frei. EA-01 und EA-02 werden nicht gleichzeitig verwendet, sofern der Incident Lead nicht beide Konten ausdrücklich begründet.
4. Die Notfalladministration erfolgt von einem vorab festgelegten, besonders geschützten Arbeitsweg. Der genaue Workstation-Standard bleibt Teil des künftigen privilegierten Device-Designs.
5. Sign-in und Handlungen werden überwacht; nach Abschluss wird der Vorgang unverzüglich in die Nachbereitung überführt.

Das Vier-Augen-Prinzip ist der organisatorische Normalfall und darf nicht durch die Verfügbarkeit einer regulären Entra- oder PIM-Identität blockiert werden. Die Notfallfreigabe, die Zugriffsmittel und die Kommunikationswege müssen daher voneinander getrennt geplant und getestet werden.

### Emergency Override bei fehlender zweiter berechtigter Person

Ein Emergency Override ist ausschließlich zulässig, wenn eine Wiederherstellung unmittelbar erforderlich ist, die Eskalation nachweislich eingeleitet wurde und trotz dieser Eskalation keine zweite berechtigte Person rechtzeitig verfügbar ist. Er ist weder eine Komfortausnahme noch ein Ersatz für die reguläre Vier-Augen-Freigabe.

In diesem Fall darf ein dazu benannter Custodian oder Incident Lead ein einzelnes Emergency-Access-Konto ohne vorherige zweite Bestätigung einsetzen. Der Override erfolgt ausschließlich über den vorab dokumentierten organisatorischen Notfallprozess; er darf keinen technischen Approval-Workflow über PIM, Conditional Access oder andere reguläre Tenant-Abhängigkeiten voraussetzen.

Für jeden Override sind verpflichtend:

- Dokumentation von Dringlichkeit, Zeitstempeln, Eskalationsversuchen und dem gewählten Konto vor oder unmittelbar bei der Nutzung;
- sofortiges Alerting an Identity und Security über die unabhängigen Kommunikationswege;
- vollständige Protokollierung aller Sign-ins, Audit-Aktionen und Recovery-Maßnahmen;
- unverzügliche nachträgliche Bestätigung durch Identity und Security, sobald diese erreichbar sind;
- ein vollständiges Post-Mortem einschließlich Begründung, warum die reguläre Vier-Augen-Freigabe nicht möglich war.

Der Override ändert weder die dauerhafte Pflicht zur Vier-Augen-Prüfung nach der Nutzung noch die getrennte Verwahrung der Zugangsmittel.

## Verantwortlichkeiten

| Rolle | Verantwortung | Darf nicht allein entscheiden oder handeln |
| --- | --- | --- |
| Identity Owner | Kontenmodell, Rollen- und CA-Ausschlüsse, Registervollständigkeit und technische Tests verantworten. | Zugriffsmittel freigeben und die eigene Freigabe bestätigen. |
| Security Owner | Alarmierung, Lognachweise, Testbegleitung, Incident- und Post-Mortem-Qualität verantworten. | Allein über die Verwendung eines Zugangsmittels entscheiden. |
| Custodian | Zugangsmittel getrennt und sicher verwahren sowie nur nach dokumentierter Freigabe bereitstellen. | Im Normalfall zugleich Incident Lead und alleinige freigebende Person für dasselbe Konto sein; der dokumentierte Emergency Override bleibt die enge Ausnahme. |
| Incident Lead | Notfall feststellen, Zweck und minimale Recovery-Maßnahmen dokumentieren sowie die Nachbereitung einleiten. | Die Vier-Augen-Freigabe allein ersetzen, außer bei dokumentiertem Emergency Override. |
| Zweite freigebende Person | Unabhängig bestätigen, dass Zweck, Umfang und Einsatz des Kontos angemessen sind. | Allein Zugangsmittel verwahren und selbst nutzen. |

Die konkreten Personen und Vertretungen werden ausschließlich im kontrollierten Emergency-Access-Register geführt. Bei Personal- oder Funktionswechseln sind die dortigen Verantwortlichkeiten unverzüglich zu prüfen.

## Conditional-Access-Ausschlüsse und kompensierende Kontrollen

Die beiden Emergency-Access-Konten werden bei späterem Enforcement direkt aus allen Conditional-Access-Policies ausgeschlossen, die Sign-ins blockieren oder einschränken können, insbesondere MFA-, Authentication-Strength-, Device-Compliance-, Standort-, Risiko-, Legacy-Authentication- und Sitzungs-Policies. In `report-only` ist kein technischer Ausschluss erforderlich, weil die Policies keine Sign-ins blockieren.

Der Ausschluss ist kein Verzicht auf Schutz. Er wird durch folgende Kontrollen kompensiert:

- cloud-only, nicht föderierte und nicht synchronisierte Konten;
- getrennte phishing-resistente Zugangsmittel und Verwahrung;
- dauerhafte Global-Administrator-Rolle ausschließlich für den Wiederherstellungszweck;
- Vier-Augen-Freigabe und Incident-Nachweis vor Nutzung;
- kritische Alarmierung bei jedem Sign-in und jeder relevanten Audit-Aktion;
- mindestens vierteljährliche Funktions- und Wiederherstellungstests;
- sofortige Nachbereitung jeder tatsächlichen oder Test-Nutzung;
- überprüfbare, direkte Ausschlüsse ohne breite Standort-, Persona- oder Gruppen-Ausnahmen.

Vor der Durchsetzung jeder neuen oder wesentlich geänderten CA-Policy ist deren Wirkung auf beide Konten mit `report-only`, Sign-in-Logs und einem kontrollierten Test zu prüfen. Die Baseline-Policies `CA-BL-001` bis `CA-BL-007` bleiben für alle anderen passenden Identitäten wirksam; der Emergency-Access-Ausschluss darf nicht als Muster für Workforce-, Lager-, Filial- oder externe Ausnahmen dienen.

Microsoft weist darauf hin, dass Emergency-Access-Konten bei MFA-, Device- oder anderen CA-Anforderungen im Notfall unbenutzbar werden können, und empfiehlt daher Ausschlüsse aus einschränkenden Policies sowie regelmäßige Tests. [Microsoft Learn: Emergency Access](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/security-emergency-access)

## Monitoring, Alerting und Nachweisführung

Jeder Sign-in, jede fehlgeschlagene Anmeldung und jede relevante Audit-Aktion der beiden Konten erzeugt einen kritischen Alarm an die Security- und Identity-Verantwortlichen. Relevante Audit-Aktionen umfassen mindestens Rollen- und Berechtigungsänderungen, Änderungen an Conditional Access, Authentication Methods, Credentials und Kontoeigenschaften sowie die Nutzung zur Wiederherstellung regulärer Administrationswege.

Das Zielsystem für Logaufbewahrung und Alarmierung wird nicht festgelegt. Es muss jedoch unabhängig vom regulären Zugriffspfad erreichbar sein und mindestens zwei getrennte Empfänger- oder Eskalationswege verwenden. Der Ausfall des Monitoring- oder Alerting-Systems darf die Nutzung im Incident nicht verhindern; er ist selbst im Incident-Protokoll zu vermerken und nachzubehandeln.

Sign-in- und Audit-Logs werden nach jeder Nutzung gesichert und dem Post-Mortem beigefügt. Microsoft empfiehlt Alarmierung für jede Emergency-Access-Nutzung und eine regelmäßige Prüfung der Sign-in- und Audit-Logs. [Microsoft Learn: Emergency Access](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/security-emergency-access)

## Tests, Wiederherstellung und Lifecycle

Für jedes Konto erfolgt mindestens alle 90 Tage ein dokumentierter Test. Tests werden angekündigt, von Security Monitoring begleitet und jeweils mit nur einem Konto durchgeführt. Sie umfassen mindestens:

- Prüfung von Verwahrung, Vier-Augen-Freigabe und unabhängigen Kommunikationswegen;
- erfolgreiche Anmeldung über den vorgesehenen, getrennten Zugangsweg;
- kontrollierte Verifikation der für Recovery erforderlichen Global-Administrator-Fähigkeit;
- Nachweis der CA-Ausschlüsse ohne Nutzung einer allgemeinen Policy-Ausnahme;
- Auslösung und Empfang von Sign-in- und Audit-Alerts;
- Prüfung, dass keine privaten Geräte, persönlichen Telefonnummern oder regulären Workforce-Authentifizierungsmethoden als alleinige Abhängigkeit hinterlegt sind;
- Prüfung, dass die Konten cloud-only bleiben, eine `*.onmicrosoft.com`-Anmeldung besitzen und keine Föderations-, AD- oder Hybrid-Sync-Abhängigkeit entstanden ist;
- Überprüfung der Custodians, der Zugriffsberechtigten, der Faktor- und Zertifikatslaufzeiten sowie der Dokumentation.

Ein Wiederherstellungstest bewertet zusätzlich, ob mit dem Konto ein regulärer privilegierter Zugriff wiederhergestellt oder eine fehlerhafte CA-Änderung sicher rückgängig gemacht werden könnte. Er verändert keine produktiven Einstellungen ohne einen genehmigten, nichtproduktiven oder kontrollierten Testplan.

Die Konten folgen keinem automatischen Joiner/Mover/Leaver aus HR, AD oder Hybrid Sync. Ändert sich jedoch die Workforce-Funktion eines Custodians oder Incident Leads, muss das Emergency-Access-Register unverzüglich geprüft und aktualisiert werden. Rollen, Zugangsmittel, Custodians, direkte CA-Ausschlüsse und Testergebnisse werden mindestens vierteljährlich sowie nach jeder Änderung oder Nutzung reviewt. Automatische Bereinigung, Lizenz- oder Ablaufprozesse dürfen die Notfallkonten oder ihre Zugangsmittel nicht unbeprüft entfernen oder sperren.

## Incident- und Recovery-Nutzung

Emergency Access darf nur verwendet werden, wenn ein dokumentierter Notfall vorliegt und reguläre privilegierte Zugriffswege nicht rechtzeitig verfügbar oder vertrauenswürdig sind. Zulässige Ziele sind die Wiederherstellung sicherer Administratorzugänge, die Korrektur einer blockierenden CA-Konfiguration, die Wiederherstellung einer erforderlichen Entra-Rolle oder die Absicherung eines laufenden Identity-Incidents.

Während der Nutzung gelten Least Privilege im Handeln und eine minimale Änderungsmenge: Es werden nur die Maßnahmen ausgeführt, die den regulären, kontrollierten Betriebszugang wiederherstellen. Das Konto wird nicht für Routinearbeiten, breite Datenabfragen oder dauerhafte Betriebsänderungen eingesetzt.

Nach jeder Nutzung – einschließlich eines fehlgeschlagenen Anmeldeversuchs – erfolgen unverzüglich:

1. Sicherung der Sign-in-, Audit- und Incident-Nachweise;
2. Bestätigung, dass der reguläre privilegierte Zugriffsweg wiederhergestellt oder ein Folgeincident eröffnet ist;
3. Prüfung aller vorgenommenen Änderungen durch eine zweite berechtigte Person;
4. Bewertung, ob Zugangsmittel, Konto, Verwahrprozess, CA-Ausschluss oder Monitoring beeinträchtigt wurden;
5. Post-Mortem mit Ursache, Zeitachse, getroffenen Maßnahmen, Folgeaufgaben und Freigabe durch Identity und Security.

## Verlorene oder kompromittierte Zugangsmittel

Der Verlust, Diebstahl, Verdacht auf Kompromittierung oder unerwartete Nutzung eines Zugangsmittels oder Kontos ist ein Security Incident. Der betroffene Zugang wird nicht weiterverwendet.

- Für die Untersuchung und Wiederherstellung wird ausschließlich das andere, unabhängige Emergency-Access-Konto nach Vier-Augen-Freigabe verwendet.
- Nach Wiederherstellung eines vertrauenswürdigen Administrationswegs werden betroffene Sessions, Zugangsmittel und gegebenenfalls das Konto kontrolliert gesperrt, ersetzt oder neu eingerichtet; die konkrete technische Maßnahme wird durch den Incident bestimmt.
- Der Ersatzweg muss wieder cloud-only, unabhängig, phishing-resistent geprüft, getrennt verwahrt und in einem vollständigen Funktions- und Alert-Test validiert sein, bevor der Incident geschlossen wird.
- Verbleibt nur ein funktionierendes Konto, wird dies als kritisches Resilienzrisiko eskaliert und der zweite unabhängige Weg mit Priorität wiederhergestellt.

## Abhängigkeiten und Wechselwirkungen

| Bereich | Vorgabe oder Abgrenzung |
| --- | --- |
| Microsoft Entra ID und Internetzugang | Für Entra-Recovery unvermeidbare Abhängigkeit. Ein umfassender Entra- oder Netzwerkausfall liegt außerhalb dieses Kontomodells. |
| On-Premises AD und Hybrid Sync | Keine Abhängigkeit: keine Synchronisation, keine AD-Authentifizierung, keine AD- oder Sync-gestützte Bereitstellung. |
| HR und Workforce-Lifecycle | Keine automatische Kontosteuerung. Nur Custodian- und Verantwortlichkeitswechsel lösen Register- und Review-Aktionen aus. |
| Föderation und externe Identity Provider | Nicht zulässiger Anmeldeweg für die beiden Konten. |
| Reguläre MFA-Dienste und mobile Faktoren | Nicht als alleinige Notfallabhängigkeit zulässig. Die ausgewählten Faktoren müssen pro Konto unabhängig geprüft sein. |
| Conditional Access | Direkte Ausschlüsse aus einschränkenden Policies, begründet durch kompensierende Kontrollen, Tests und Monitoring. |
| PIM und Privileged Roles | Die beiden Konten behalten Global Administrator dauerhaft aktiv und benötigen keine PIM-Aktivierung. Reguläre privilegierte Rollen und PIM bleiben der bevorzugte Betriebsweg und können durch Emergency Access wiederhergestellt werden. |
| Administrative Units | Kein AU-Scope und kein Ersatz für AU-delegierte Administration. |
| Monitoring und externe Systeme | Sie unterstützen Alerting und Nachweisführung, dürfen aber keine Voraussetzung für die Notfallanmeldung oder -freigabe sein. |

## Offene Fragen und Abhängigkeiten

- Welcher konkrete phishing-resistente Faktor je Konto die Unabhängigkeits-, Verwahr- und Wiederherstellungsanforderungen erfüllt.
- Ob eine zertifikatsbasierte Alternative ohne Abhängigkeit von On-Premises-PKI, AD oder einem gemeinsamen Vertrauensanker bereitgestellt und betrieben werden kann.
- Der verbindliche Standard für den besonders geschützten Notfallarbeitsplatz sowie dessen Ersatz bei Ausfall.
- Zielsystem, Aufbewahrungsfristen, Eskalationswege und unabhängige Kommunikationskanäle für Monitoring und Alerting.
- Verbindliche Liste der bei Recovery zulässigen Maßnahmen, Incident-Rollen und Eskalationsfristen.
- Lizenz-, Produkt- und Supportvoraussetzungen für die ausgewählten Faktoren, Rollen, Alerts und Testverfahren.
- Abstimmung des Prozesses mit künftigen PIM-, Device-, Incident-Response- und Business-Continuity-Designs.
