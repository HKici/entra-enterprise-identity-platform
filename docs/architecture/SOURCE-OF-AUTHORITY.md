# Source-of-Authority-Modell

Status: **Entwurf (IAM-004)**

## Zweck und Entscheidungsrahmen

Dieses Dokument ordnet jeder Identitätsart und jedem relevanten Attribut genau eine führende Quelle zu. Es unterscheidet zwischen fachlicher Autorität für Beschäftigung und Organisation, technischer Autorität für Verzeichnisobjekte und Autorität für Cloud-Zugriffsdaten. Mehrere Systeme dürfen nur dann beteiligt sein, wenn sie unterschiedliche Attribute führen; ein Attribut darf nicht gleichzeitig von mehreren Systemen geschrieben werden.

Das Modell legt **keine** Implementierung mit Microsoft Entra Connect oder Cloud Sync fest. Attribut-Mappings, Korrelation und Synchronisationsrichtung werden erst nach einem separaten technischen Design bestimmt. Eine unveränderliche Korrelation zwischen On-Premises- und Cloud-Objekten ist dafür erforderlich, ihre konkrete technische Ausprägung wird hier aber nicht festgelegt. [Microsoft Learn: sourceAnchor-Konzept](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/plan-connect-design-concepts)

Die Architekturentscheidung ist in [ADR-0005](../adr/0005-source-of-authority-fuer-identitaeten-und-attribute.md) mit Status `Proposed` dokumentiert.

## Grundsätze

1. **Eine Schreibautorität je Attribut:** Fachliche, technische und cloud-spezifische Attribute werden getrennt geführt und nicht mit „Last writer wins“ aufgelöst.
2. **HR steuert Beschäftigung, Organisation und soweit vorhanden Standort, nicht Cloud-Zugriffe:** Ein HR-System bestimmt bei internen Mitarbeitenden die Person, das Beschäftigungsverhältnis sowie Organisations- und, soweit gepflegt, Standortattribute. Es vergibt weder Entra-Rollen noch App- oder Ressourcenberechtigungen.
3. **On-Premises AD führt nur On-Premises-Technik:** Besteht für eine Workforce-Identität ein AD-Konto, ist AD für dessen technisch notwendige Verzeichnisattribute authoritative, solange kein späteres Cloud-first-SoA-Modell diese Autorität ablöst. AD ist nicht die Quelle für Beschäftigungsstatus, Organisation, Standort oder Fachrolle.
4. **Entra führt Cloud-spezifische Objekte:** Entra ID ist maßgeblich für cloud-only Identitäten, Cloud-Zugriffsobjekte, Gruppenmitgliedschaften für Cloud-Zugriffe, Administrative Units, Entra-Rollenzuweisungen und Authentifizierungsregistrierungen.
5. **Keine implizite Berechtigung aus Stammdaten:** Attribute wie Standort, Abteilung oder Beschäftigungsart können spätere Prüfungen oder dynamische Gruppen unterstützen, verleihen allein aber keine Berechtigung.
6. **Kontrollierte Ausnahmen:** Manuelle Korrekturen in einem nicht führenden System sind zeitlich begrenzte Ausnahmefälle, müssen dokumentiert werden und dürfen die führende Quelle nicht dauerhaft übersteuern.

## Führende Quellen nach Identitätsart

| Identitätsart | Fachlich führende Quelle | Technische und Cloud-Autorität | Abgrenzung |
| --- | --- | --- | --- |
| Interne Mitarbeitende – Zentrale Hamburg | Generisches HR-System für Person, Beschäftigung, Organisation und, soweit vorhanden, Standort. | AD für vorhandene On-Premises-Kontoattribute; Entra für Cloud-Identitäts- und Zugriffsattribute. | Hamburg ist, soweit im HR-System gepflegt, ein Standortwert und keine eigenständige Identity Source. |
| Interne Mitarbeitende – Lager Nord | Generisches HR-System. | Wie Zentrale Hamburg. | Der Standortwert „Lager Nord“ unterstützt, soweit vorhanden, Gruppen- und AU-Prüfungen, erzeugt aber keine Rechte. |
| Interne Mitarbeitende – Lager Süd | Generisches HR-System. | Wie Zentrale Hamburg. | Der Standortwert „Lager Süd“ bleibt von Rolle und Berechtigung getrennt. |
| Interne Mitarbeitende – Filialen | Generisches HR-System. | Wie Zentrale Hamburg. | HR führt, soweit vorhanden, Standort und Filialcode; daraus folgen keine automatischen App-Berechtigungen. |
| Externe Dienstleister | Noch nicht konkret benanntes Vertrags-/Sponsor-System für Auftrag, Sponsor und Laufzeit. | Entra für das bei Nordstern vorhandene externe Zugriffsobjekt und dessen Cloud-Zugriffszuweisungen; der externe Identitätsanbieter führt die Anmeldeinformationen. | Das konkrete Modell für externe Identitäten bleibt offen; der externe Status rechtfertigt keine Administratorrechte. |
| Privileged Administrator | HR liefert die aktive beziehungsweise genehmigte Workforce-Funktion als Voraussetzung für die privilegierte Identität. | Die separate administrative Identität, ihre Cloud-Eigenschaften und die Entra-Rollenzuweisungen sind cloud-only und in Entra führend. | Eine HR-Änderung erzeugt oder erweitert keine privilegierte Identität ohne gesonderte, kontrollierte Freigabe. |
| Emergency-Access-Identität | Kein HR- oder AD-Stammdatensatz ist Quelle für die Notfallidentität. Ein kontrolliertes Emergency-Access-Register führt Verantwortliche und Prüfpflichten. | Das Notfallkonto, seine Cloud-Eigenschaften und Rollen sind cloud-only und in Entra führend. | Anzahl, Verwahrung, konkrete Schutzmaßnahmen, Wiederherstellung und Tests werden in `CA-003` entschieden. |

„Generisches HR-System“ bezeichnet die fachliche Kategorie, nicht ein konkretes Produkt. Das Szenario legt kein HR-Produkt fest.

## Attributverantwortung

| Attributklasse | Führende Quelle | Relevanz für Gruppen, AUs und Lifecycle | Regeln |
| --- | --- | --- | --- |
| Personenkern, Beschäftigungsart und Beschäftigungsstatus | HR-System für interne Mitarbeitende | Grundlage für Joiner und Leaver; Beschäftigungsart unterscheidet Workforce von anderen Identitätsarten. | Nicht in AD oder Entra fachlich überschreiben. |
| Eintritts-, Wechsel- und Austrittsdatum | HR-System für interne Mitarbeitende | Löst fachlich JML-Prüfungen aus; keine unmittelbare technische Berechtigung. | Fristen und technische Reaktion werden in `GOV-001` festgelegt. |
| Abteilung, Kostenstelle, fachliche Funktion und Manager | HR-System für interne Mitarbeitende | Kann Persona- oder Business-Role-Prüfungen unterstützen. | Nicht direkt als App- oder Entra-Rolle verwenden. |
| Standort und Filialcode | HR-System für interne Mitarbeitende, soweit die Attribute dort vorhanden und gepflegt sind | Prüfbasis für Site Groups und später für die direkte Mitgliedschaft in `AU-Warehouse-North`, `AU-Warehouse-South` oder `AU-Stores`. | Ein Standortwechsel löst Review aus, verleiht aber keine Access Group oder AU-Rolle. |
| On-Premises-Kontobezeichnung, DN, technisch notwendige AD-Kontoattribute und lokale Gruppenbezüge | On-Premises AD, sofern ein AD-Konto erforderlich ist und kein späteres Cloud-first-SoA-Modell die Autorität ablöst | Technische Abhängigkeit lokaler Anwendungen und Geräte. | Der technische Zustand folgt dem HR-geführten Beschäftigungsstatus; genaue Mappings und Korrelation sind offen. |
| Entra-Objektkennung, Cloud-Authentifizierungsregistrierungen und cloud-spezifische Identitätseigenschaften | Microsoft Entra ID | Grundlage für Cloud-Authentifizierung, Auditierung und Entra-Verwaltung. | Keine Rückschreibung in HR; konkrete Authentication-Methoden sind nicht Teil dieses Modells. |
| Persona-, Site-, Business-Role- und Access-Group-Objekte sowie Cloud-Gruppenmitgliedschaften | Microsoft Entra ID | Steuert die in `IAM-002` getrennten Gruppen- und Zugriffsmodelle. | HR-Attribute können spätere Regeln ermöglichen, aber die Mitgliedschaft bleibt bis zu einer gesonderten Entscheidung kontrolliert verwaltet. |
| Administrative Units, direkte AU-Mitgliedschaften und AU-gescopte Rollenzuweisungen | Microsoft Entra ID | Steuert ausschließlich delegierte Verwaltung gemäß `IAM-003`. | HR-Standortwerte können geprüft werden; Gruppenmitgliedschaften werden nicht als AU-Mitgliedschaft interpretiert. |
| Physischer Standort und Eigentümerschaft von Geräten | Noch festzulegende Asset- bzw. Device-Inventory-Quelle | Erforderlich, falls Standortgeräte in AUs aufgenommen werden sollen. | Bis zur Entscheidung keine automatisierte AU-Zuordnung für Geräte. |
| Auftrag, Sponsor und Vertragslaufzeit externer Dienstleister | Noch nicht konkret benanntes Vertrags-/Sponsor-System | Grundlage für externe Joiner, Änderungen und Entzug. | System, Attributkatalog und Genehmigungsablauf sind offen. |
| Externes Zugriffsobjekt, Cloud-Access-Groups und Anwendungszuweisungen | Microsoft Entra ID | Technische Umsetzung der genehmigten externen Zugriffe. | Keine Annahme zu B2B, Synchronisation oder Anmeldemethode. |
| Privilegierte Identität, Entra-Rollen und administrative Zugriffszuweisungen | Microsoft Entra ID | Separater privilegierter Lifecycle; Basis für PIM und Access Reviews. | An eine aktive beziehungsweise genehmigte Workforce-Funktion gekoppelt; die spätere Genehmigungs- und PIM-Instanz wird noch bestimmt. |
| Emergency-Access-Konto und technische Notfallrollen | Microsoft Entra ID | Notfallzugriff und Auditierung. | Anzahl, Verwahrung, konkrete Schutzmaßnahmen, Verantwortlichkeiten und Tests folgen dem späteren Emergency-Access-Design. |

## Bewusst cloud-only geführte Objekte

Folgende Objekte bleiben im vorgeschlagenen Modell bewusst cloud-only und werden nicht aus HR oder On-Premises AD bereitgestellt:

- separate privilegierte Administratoridentitäten;
- Emergency-Access-Identitäten;
- Entra-Rollenzuweisungen, Authentication-Method-Registrierungen und weitere cloud-spezifische Sicherheitsdaten;
- Cloud-Access-Groups und ihre Berechtigungsmitgliedschaften;
- Administrative Units, ihre direkten Mitgliedschaften und AU-gescopte Rollenzuweisungen;
- das bei Nordstern vorhandene technische Zugriffsobjekt für externe Identitäten.

Ein möglicher späterer Austausch von Daten mit On-Premises-Systemen ändert diese Autorität nicht ohne eine eigene Architekturentscheidung. Die Auswahl von Entra Connect oder Cloud Sync ist ausdrücklich nicht Gegenstand dieses Dokuments.

## Konfliktbehandlung

| Konflikt | Verbindliche Behandlung |
| --- | --- |
| HR und AD bzw. Entra enthalten unterschiedliche Beschäftigungs-, Organisations- oder Standortdaten. | Der HR-Wert gilt fachlich. Die Abweichung wird als Datenqualitätsfall behandelt; eine fachliche Korrektur erfolgt in HR, keine Gegenkorrektur in AD oder Entra. |
| AD und Entra enthalten unterschiedliche Werte für eine On-Premises-technische Eigenschaft. | AD gilt für die technische On-Premises-Eigenschaft. Mappings und zulässige Ausnahmen werden erst im Synchronisationsdesign festgelegt. |
| Ein Cloud-spezifisches Entra-Attribut wird in AD oder HR geändert. | Die Änderung ist nicht führend und wird nicht als gültige Fachänderung übernommen. Der Fall wird an die zuständige Quelle zurückgegeben. |
| Ein externer Auftrag endet, das externe Objekt besitzt aber noch Cloud-Zugriffe. | Das noch nicht konkret benannte Vertrags-/Sponsor-System löst die fachliche Beendigung aus; Entra entzieht die technischen Zugriffe nach dem noch festzulegenden JML-Prozess. |
| Die Workforce-Funktion ist nicht mehr aktiv oder genehmigt, eine privilegierte Identität besteht fort. | Das ist ein sicherheitsrelevanter Ausnahmefall. Die privilegierte Identität wird unabhängig geprüft und gemäß dem privilegierten Lifecycle entzogen oder gesperrt. |
| Technische Objektzuordnung ist unklar oder doppelt. | Keine automatische Zusammenführung und kein „Last writer wins“. Der Fall wird vor einer Synchronisation oder Berechtigungsänderung manuell geklärt. |

Für alle Quellen gilt: Änderungen werden an die führende Stelle zurückgegeben, mit Audit-Information dokumentiert und erst nach Korrektur oder genehmigter Ausnahme verarbeitet. Eine genehmigte Ausnahme benötigt Owner, Begründung, Ablaufdatum und Review.

## Risiken mehrerer führender Systeme

- Unterschiedliche oder überschreibende Werte können falsche Gruppen-, AU- oder Lifecycle-Entscheidungen verursachen.
- Fehlende eindeutige Korrelation kann doppelte Benutzerobjekte und verbleibende Zugriffe erzeugen.
- Verzögerte Austrittsdaten oder nicht abgeglichene privilegierte Identitäten können zu unberechtigtem Zugang führen.
- Standortdaten aus mehreren Quellen würden die Delegationsgrenzen von Lager- und Filial-AUs unzuverlässig machen.
- Manuelle Direktänderungen in nicht führenden Systemen erschweren Auditierung, Incident-Analyse und Wiederholbarkeit.

Die Risikobehandlung besteht aus Attribut-Ownership, kontrollierten Änderungen, dokumentierten Ausnahmen und späteren JML-, Review- und Monitoring-Prozessen; sie wird nicht durch ein Synchronisationstool allein gelöst.

## Auswirkung auf Joiner, Mover und Leaver

| Lifecycle-Ereignis | Fachlicher Auslöser | Grundsätzliche Wirkung |
| --- | --- | --- |
| Joiner – interne Mitarbeitende | Neuer aktiver Beschäftigungsdatensatz im HR-System. | Erzeugt den fachlichen Bedarf für eine Workforce-Identität. Die technische Bereitstellung in AD und/oder Entra folgt dem späteren Synchronisationsdesign und darf keine privilegierten Rechte implizieren. |
| Mover – Standort, Funktion oder Organisation | Änderung eines HR-geführten Attributs. | Löst eine Prüfung von Persona-, Site- und Business-Role-Gruppen sowie gegebenenfalls der direkten AU-Mitgliedschaft aus. Access Groups und Entra-Rollen werden nicht allein aufgrund eines Attributwechsels vergeben. |
| Leaver – interne Mitarbeitende | HR-geführtes Austritts- oder Inaktivitätsereignis. | Löst den Entzug oder die Sperrung technischer Workforce-Zugriffe und eine gesonderte Prüfung aller privilegierten Identitäten aus. Zeitpunkte und Automatisierung bleiben Governance-Entscheidungen. |
| Joiner/Mover/Leaver – externe Dienstleister | Beginn, Änderung oder Ende eines Auftrags im noch nicht konkret benannten Vertrags-/Sponsor-System. | Löst die Prüfung und Anpassung des Entra-Zugriffsobjekts sowie zeitlich begrenzter Access Groups aus. |
| Privilegierter Lifecycle | Aktive beziehungsweise genehmigte Workforce-Funktion, genehmigte administrative Aufgabe, Entzug der Aufgabe oder Ende der Workforce-Berechtigung. | Wird separat vom Workforce-Lifecycle geführt; die Entra-Identität und Rollen erhalten einen eigenen Review- und Entzugsprozess. |
| Emergency Access | Änderung der Verantwortlichkeiten oder des Notfallverfahrens. | Kein automatischer HR- oder AD-gesteuerter Lifecycle. Änderungen folgen dem kontrollierten Emergency-Access-Prozess in `CA-003`. |

## Offene Architekturfragen und Abhängigkeiten

- Konkretes HR-System, Vertrags-/Sponsor-System und Device-Inventory-System sowie deren Datenqualität und Attributkataloge.
- Unveränderlicher Korrelationswert, technische Konto- und Namensstandards sowie Fehlerbehandlung für Hybridobjekte.
- Auswahl und Design von Entra Connect oder Cloud Sync einschließlich Mappings, Schreibrichtung, Monitoring und Recovery.
- Zulässige Dynamik für Persona- und Site-Groups nach Attributqualitätsprüfung; Access Groups bleiben davon getrennt.
- Zuständigkeiten, Fristen und Automatisierungsgrad für `GOV-001` sowie Reviews für privilegierte Identitäten und AUs.
- Externes Kollaborations- und Authentifizierungsmodell.
- PIM- und Emergency-Access-Design einschließlich der Führung des zugehörigen Kontrollregisters.
