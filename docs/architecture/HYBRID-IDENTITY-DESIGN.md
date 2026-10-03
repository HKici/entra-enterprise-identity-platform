# Hybrid-Identity-Zielmodell

Status: **Entwurf (IAM-005)**

## Ziel und Abgrenzung

Dieses Dokument beschreibt das Zielmodell für die Koexistenz von On-Premises Active Directory (AD) und Microsoft Entra ID bei der Nordstern Handelsgruppe. Es konkretisiert, welche Objektklassen für eine hybride Bereitstellung geeignet sind, wie Korrelation und Scope zu behandeln sind und nach welchen Kriterien Microsoft Entra Connect Sync oder Microsoft Entra Cloud Sync ausgewählt werden.

Es wird **keine** Synchronisationstechnologie ausgewählt und keine produktive Konfiguration, OU-Struktur, Attributzuordnung oder Writeback-Funktion aktiviert. Das Zielmodell setzt die in ADR-0005 beschriebene Attribut-Source-of-Authority voraus; deren technische Synchronisations- und Korrelationsdetails bleiben eine Abhängigkeit.

## Zielzustand

Microsoft Entra ID ist die zentrale Cloud-Identity- und Access-Control-Plane. AD bleibt für die technisch erforderlichen On-Premises-Identitäten und Anwendungen verfügbar. Die hybride Synchronisation ist ein gezielter Übergangs- und Koexistenzmechanismus; sie darf weder die fachliche Source of Authority noch das Gruppen-, AU- oder Berechtigungsmodell ersetzen.

Pro Objekt darf zu einem Zeitpunkt nur ein Synchronisationstool aktiv Änderungen nach Entra exportieren. Connect Sync und Cloud Sync dürfen nicht parallel dieselbe Benutzer-, Gruppen- oder Geräteidentität aktiv nach Entra exportieren.

## Objektumfang

| Objektklasse | Vorgesehener Umgang | Begründung |
| --- | --- | --- |
| Interne Workforce-Benutzer der Zentrale Hamburg | Synchronisieren, wenn sie ein AD-Konto für On-Premises-Ressourcen benötigen. | Persönliche Büroarbeitsplätze und zentrale Fachanwendungen können weiterhin hybride Abhängigkeiten haben. |
| Interne Workforce-Benutzer Lager Nord und Lager Süd | Synchronisieren, wenn sie ein AD-Konto für Logistikanwendungen, gemeinsam genutzte Windows-Geräte oder andere On-Premises-Ressourcen benötigen. | Schichtbetrieb und Spezialgeräte rechtfertigen keine pauschale Synchronisation ohne technische Abhängigkeit. |
| Interne Workforce-Benutzer in Filialen | Synchronisieren, wenn Filialanwendungen oder Geräte ein AD-Konto erfordern. | Die Standortzugehörigkeit allein ist kein Synchronisationskriterium. |
| On-Premises-Gruppen | Nur synchronisieren, wenn sie für eine weiterhin benötigte On-Premises- oder Hybridressource erforderlich sind. | Cloud-Access-Groups bleiben vom gruppenbasierten Zugriffsmodell getrennt. |
| Geräteobjekte | Nur nach gesonderter Anforderungs- und Funktionsprüfung synchronisieren. | Gerätesynchronisation und Hybrid-Join-Anforderungen unterscheiden sich zwischen den Sync-Optionen. |
| Externe Dienstleister | Nicht aus AD synchronisieren. | Das technische externe Zugriffsobjekt und die Zugriffszuweisung werden cloudseitig geführt. |
| Privilegierte Administratoridentitäten | Nicht aus AD synchronisieren; cloud-only. | Separierte administrative Identitäten bleiben unabhängig vom Workforce-Sync. |
| Emergency-Access-Identitäten | Nicht aus AD synchronisieren; cloud-only. | Notfallidentitäten dürfen nicht von AD- oder Synchronisationsverfügbarkeit abhängen. |
| Entra-Rollenzuweisungen, Administrative Units und AU-Scopes | Cloud-only. | Diese Objekte sind Bestandteil des Cloud-Verwaltungs- und Delegationsmodells. |

## Attributfluss und Attributgrenzen

AD nach Entra fließen nur Attribute, die für den hybriden Verzeichnis- und Anwendungsbetrieb erforderlich sind und deren fachliche Ursprungssysteme durch das Source-of-Authority-Modell bestimmt sind. Wenn ein HR-geführtes Organisationsattribut über AD fließt, ist AD dabei nur technische Repräsentation, nicht fachliche Quelle.

| Attributklasse | Konzeptioneller Fluss | Regeln |
| --- | --- | --- |
| Stabile technische Korrelation | AD nach Entra | Ein unveränderliches, eindeutiges Korrelationsmerkmal verbindet das AD- und Entra-Objekt über seinen Lebenszyklus. Die konkrete Attributwahl wird erst im Sync-Design festgelegt. |
| Anmelde- und technische Kontodaten | AD nach Entra, soweit für Hybridbetrieb erforderlich | UPN und weitere technische Identifikatoren müssen eindeutig, validiert und mit dem Korrelationsmodell vereinbar sein. |
| Personenkern und Organisationsdaten | HR nach AD nach Entra, soweit für Cloud-Anwendungen benötigt | AD darf diese Werte nicht fachlich eigenständig überschreiben. Standort, Abteilung oder Funktion verleihen keine Berechtigung. |
| Gruppen und Gruppenmitgliedschaften | AD nach Entra nur für explizit genehmigte Hybridgruppen | Persona-, Site-, Business-Role- und Access-Group-Semantik bleibt gemäß Gruppenmodell getrennt. |
| Cloud-Authentifizierungsregistrierungen, Entra-Objektkennung und Cloud-Sicherheitsdaten | Ausschließlich Entra | Keine Rückführung als fachliche Attribute nach AD oder HR. |
| Entra-Rollen, Application Assignments, Cloud-Access-Groups, AUs und AU-Rollen | Ausschließlich Entra | Sie werden nicht aus AD-Organisations- oder Standortdaten abgeleitet. |

## Korrelation, Matching und Duplicate Prevention

Das Matching darf sich nicht allein auf veränderliche Attribute wie Anzeigename oder UPN stützen. Ein stabiles, eindeutiges und nicht wiederverwendbares Korrelationsmerkmal ist Voraussetzung für die Zusammenführung eines bestehenden Entra-Objekts mit dem passenden AD-Objekt. Microsoft beschreibt den sourceAnchor als unveränderlichen Identifikator über den Objektlebenszyklus. [Microsoft Learn: sourceAnchor](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/plan-connect-design-concepts)

Vor einer Synchronisation oder Migration sind mindestens zu prüfen:

- Eindeutigkeit von UPN, primären Anmeldekennungen und geplanten Korrelationsmerkmalen;
- vorhandene Cloud-only Benutzerobjekte und ihr zulässiges Matching;
- verwaiste, deaktivierte oder doppelte AD- und Entra-Objekte;
- Gruppen-, Manager- und andere referenzielle Abhängigkeiten;
- Auswirkungen von Scope-Änderungen auf Deprovisionierung und Mitgliedschaften.

Bei einer nicht eindeutigen Zuordnung wird kein automatisches Merge durchgeführt. Der Fall wird als Datenqualitäts- und Sicherheitsereignis geklärt, bevor der Benutzer in einen Sync-Scope aufgenommen wird.

## OU- und Scope-Filterung

OU- oder Scope-Filterung dient ausschließlich der kontrollierten Auswahl technischer Objekte für die Synchronisation. Sie ist kein Berechtigungsmodell und ersetzt weder Security Groups noch Administrative Units.

Ein konzeptioneller Scope soll:

- nur OUs oder Objektgruppen mit bestätigter Hybridabhängigkeit einschließen;
- privilegierte und Emergency-Access-Konten explizit ausschließen;
- Dienst- und technische Konten nur nach gesonderter Bewertung einschließen;
- Pilot-Scope und Produktions-Scope eindeutig trennen;
- referenzielle Abhängigkeiten von Benutzern, Gruppen und Managern vor einer Scope-Änderung prüfen;
- keine alleinige Ableitung aus Standort, Persona oder einer Security Group vornehmen.

Während einer Migration dürfen Objekte nicht vorschnell aus dem bestehenden Sync-Scope entfernt werden. Microsoft weist darauf hin, dass dadurch Referenzen, etwa Gruppenmitgliedschaften, entfernt werden können. [Microsoft Learn: Migration zu Cloud Sync](https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/migrate-azure-ad-connect-to-cloud-sync)

## Denkbare Writeback-Funktionen

Writeback ist kein Standardbestandteil des Zielmodells. Jede Funktion erfordert eine fachliche Berechtigung, ein klares Source-of-Authority-Modell, eine Risikoanalyse und ein eigenes Review.

| Denkbare Funktion | Möglicher Nutzen | Wesentliche Risiken und Bedingungen |
| --- | --- | --- |
| Password Writeback | Unterstützt einen konsistenten Passwort-Lifecycle für geeignete hybride Konten. | Änderungen wirken auf AD-Konten; Schutz gegen missbräuchliche Rückschreibungen, Ausfallverhalten und Helpdesk-Prozess müssen vorab definiert sein. |
| Group Writeback | Kann ausgewählte Cloud-Gruppen für notwendige On-Premises-Ressourcen verfügbar machen. | Unbeabsichtigte Gruppenmitgliedschaft kann On-Premises-Berechtigungen erweitern; nur klar abgegrenzte Gruppen und Ziel-OUs bewerten. |
| Exchange-Hybrid-Attributwriteback | Kann für einen später bestätigten Exchange-Hybrid-Use-Case relevant sein. | Nur bei tatsächlich bestehender Abhängigkeit; Attributautorität und Rückschreibekonflikte müssen eindeutig sein. |
| Device Writeback oder vergleichbare Gerätefunktionen | Historisch für bestimmte Hybridgeräte-Szenarien relevant. | Funktionsumfang und strategische Eignung unterscheiden sich zwischen den Optionen; keine Annahme ohne Geräte- und Plattformdesign. |

## Koexistenz und Migration

Eine Migration folgt einem kontrollierten, objektbezogenen Vorgehen:

1. Inventar der AD-, Entra- und Hybridabhängigkeiten erstellen.
2. Quelle, Scope, Korrelationsmerkmale und Writeback-Abhängigkeiten pro Objektklasse validieren.
3. Einen abgegrenzten Pilot-Scope mit synthetischen oder ausdrücklich freigegebenen Testobjekten verwenden.
4. Pro Objekt nur einen aktiven Exporteur nutzen; Connect Sync und Cloud Sync dürfen nicht parallel dieselben Objekte aktiv nach Entra exportieren.
5. Objekt-, Gruppen- und Referenzintegrität sowie JML-Fälle validieren.
6. In kontrollierten Wellen migrieren und einen dokumentierten Rollback-Punkt halten.
7. Nach erfolgreicher Objekt- und Referenzvalidierung den Cutover je Migrationswelle durchführen und den bisherigen Scope erst anschließend gemäß dem unterstützten Migrationsverfahren anpassen oder entfernen.

Für eine unterstützte Connect-Sync-zu-Cloud-Sync-Migration ist eine phasenweise, OU-basierte Koexistenz möglich. Objekte können bis zum Cutover weiterhin im Connect-Sync-Scope verbleiben, während die aktiven Exportflüsse kontrolliert unterdrückt werden. Dabei darf Cloud Sync für diese Objekte erst nach dem kontrollierten Übergang aktiv nach Entra exportieren; Referenzobjekte dürfen bis zum Abschluss der Welle nicht unkontrolliert aus dem bisherigen Scope entfernt werden. [Microsoft Learn: Migration zu Cloud Sync](https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/migrate-azure-ad-connect-to-cloud-sync)

## Architekturvergleich

| Kriterium | Microsoft Entra Connect Sync | Microsoft Entra Cloud Sync |
| --- | --- | --- |
| Funktionsumfang | Umfassender lokaler Sync-Engine-Funktionsumfang, einschließlich erweiterter Synchronisationsregeln und Geräte-Synchronisationsszenarien. | Breite Unterstützung für Benutzer, Gruppen, Kontakte, Password Hash Sync, Password Writeback, Exchange-Hybrid-Attribute und grundlegende Attributanpassungen; Funktionsparität ist je Szenario zu prüfen. |
| Komplexität | Höher bei komplexen Regeln, Topologien und lokaler Engine-Verwaltung. | Tendenziell geringer bei standardisierten, cloudverwalteten Konfigurationen; komplexe Regeln sind gegenüber Connect eingeschränkt. |
| Betriebsmodell | Lokaler Server und Sync-Engine; Staging und Wartung sind lokal zu betreiben. | Cloudverwalteter Provisioning-Dienst mit lokalen Agenten; mehrere Agenten können Resilienz und Lastverteilung unterstützen. |
| Resilienz | Erfordert ein bewusstes Staging-/Wiederherstellungsmodell für den lokalen Sync-Server. | Unterstützt mehrere Agenten und automatisches Failover; lokale AD- und Agent-Abhängigkeit bleibt bestehen. |
| Hybrid-Abhängigkeiten | Geeignet, wenn Connect-spezifische Anforderungen wie Geräte-Synchronisation oder erweiterte Sync-Regeln vorliegen. | Geeignet für standardisierte Benutzer-, Gruppen- und Kontakt-Synchronisation; einzelne Hybridfunktionen müssen im aktuellen Funktionsvergleich bestätigt werden. |
| Komplexe AD-Landschaften | Stärker für komplexe und stark angepasste Synchronisationsregeln. | Vorteilhaft für bestimmte getrennte Forest- und M&A-Szenarien; Grenzen für komplexe Regeln, große Gruppen und einzelne Gerätefunktionen prüfen. |
| Migration und Koexistenz | Kann während einer unterstützten Migration im Scope verbleiben, während Exportflüsse kontrolliert unterdrückt und Wellen validiert werden. | Unterstützt eine phasenweise Migration; pro Objekt ist bis zum Cutover nur ein aktiver Export nach Entra zulässig. Migrationsvoraussetzungen sind streng zu validieren. |
| Security- und Betriebsrisiken | Hohe lokale Berechtigungs- und Konfigurationssensitivität; komplexe Regeln erschweren Review und Recovery. | Agent- und Cloud-Konfigurationsabhängigkeit; fehlende Funktionsparität oder falsche Scope-Grenzen können zu Daten- und Berechtigungsfehlern führen. |

Der aktuelle Funktionsvergleich enthält unter anderem Unterschiede bei erweiterten Sync-Regeln, Geräte-Synchronisation, Gruppengrößen und Agentenresilienz. Die jeweils aktuelle Produktmatrix ist vor einer Auswahl verbindlich zu prüfen. [Microsoft Learn: Decision Guide](https://learn.microsoft.com/en-us/entra/identity/hybrid/cloud-sync/connect-to-cloud-sync-decision-guide)

## Entscheidungskriterien

Eine Produktauswahl erfolgt erst, wenn folgende Kriterien mit der tatsächlichen AD-Landschaft abgeglichen sind:

| Wenn die validierte Anforderung lautet … | Dann ist vorrangig zu bewerten … |
| --- | --- |
| Erweiterte, kundenspezifische Synchronisationsregeln oder Geräte-Synchronisation sind zwingend. | Connect Sync, weil Cloud Sync diese Szenarien möglicherweise nicht vollständig abdeckt. |
| Standardisierte Benutzer-, Gruppen- und Kontakt-Synchronisation mit mehreren Agenten und cloudverwaltetem Betrieb genügt. | Cloud Sync. |
| Getrennte Forests oder M&A-Szenarien benötigen eine vereinfachte Anbindung. | Cloud Sync, vorbehaltlich aktueller Szenario- und Attributprüfung. |
| Sehr große Gruppen, komplexe Referenzen oder nicht unterstützte Writeback-Funktionen sind erforderlich. | Connect Sync oder ein gemischtes, explizit unterstütztes Koexistenzmodell. |
| Bestehende Connect-Sync-Konfiguration enthält nicht migrierbare Anpassungen. | Connect Sync beibehalten, bis Funktionsparität oder ein genehmigtes Migrationsdesign vorliegt. |

## Abhängigkeiten zu Joiner, Mover und Leaver

- **Joiner:** Das fachliche Beschäftigungsereignis erzeugt den Bedarf für eine Workforce-Identität. Der technische Sync-Scope wird erst nach erfolgreicher Korrelation und bestätigter Hybridabhängigkeit angewendet.
- **Mover:** Änderungen an Standort, Organisation oder Funktion lösen eine Prüfung von Scope, Gruppen und gegebenenfalls direkter AU-Mitgliedschaft aus. Sie dürfen nicht automatisch privilegierte Rechte oder einen Technologiewechsel erzeugen.
- **Leaver:** Das fachliche Austrittsereignis muss den Entzug im führenden System und die Prüfung aller abhängigen AD- und Entra-Objekte auslösen. Privilegierte und Emergency-Access-Identitäten folgen eigenen, cloud-only Kontrollprozessen.

## Offene Architekturfragen und Abhängigkeiten

- ADR-0005 und das zugehörige Source-of-Authority-Dokument müssen vor einer technischen Implementierung verfügbar und angenommen sein.
- Tatsächliche AD-Topologie, Forest- und Domain-Anzahl, Vertrauensstellungen, Gruppenvolumen und benutzerdefinierte Sync-Regeln.
- Hybridgeräte-, On-Premises-Anwendungs- und Exchange-Hybrid-Anforderungen.
- Verbindliche Attributliste, Korrelationsmerkmal, Namens- und UPN-Standards sowie Behandlung bestehender Cloud-only Workforce-Konten.
- Notwendigkeit, Scope und Schutzmaßnahmen für mögliche Writeback-Funktionen.
- Agent-/Server-Resilienz, Monitoring, Backup, Recovery und Betriebsverantwortung.
- JML-Fristen, Datenqualitätsprozess und Ausnahmen für externe sowie privilegierte Identitäten.
