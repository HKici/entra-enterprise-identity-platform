# Gruppenmodell

Status: **Entwurf (IAM-002)**

## Zweck und Geltungsbereich

Dieses Dokument beschreibt ein nachvollziehbares Gruppenmodell für die Nordstern Handelsgruppe. Es trennt Personen- und Standortzugehörigkeit, fachliche Rollen sowie technische Zugriffszuweisungen. Das Modell dient als fachliche Referenz; es erzeugt keine Gruppen und legt keine produktive Entra-Konfiguration fest.

Der Entwurf verwendet das in ADR-0005 vorgeschlagene Source-of-Authority-Modell für Beschäftigungs- und Standortattribute. Die technische Mitgliedschaftsverwaltung sowie das Synchronisationsmodell bleiben vom Architektur- und Security-Review, der Attributqualität und dem [Joiner/Mover/Leaver-Prozess](../governance/JOINER-MOVER-LEAVER.md) abhängig.

## Begriffe und Abgrenzung

| Element | Zweck | Darf unmittelbar Zugriff gewähren? |
| --- | --- | --- |
| Security Group | Technischer Container für Mitgliedschaften und Zuweisungen. Die semantische Kategorie ist im Namen erkennbar. | Nur, wenn es sich um eine Access Group handelt. |
| Persona Group | Beschreibt den Identitäts- bzw. Beschäftigungstyp, etwa Warehouse User oder External Contractor. | Nein. |
| Site Group | Beschreibt die organisatorische Standortzugehörigkeit. | Nein. |
| Business Role Group | Beschreibt eine fachliche Funktion, die unabhängig von einem konkreten System bestehen kann. | Nein; die konkrete Berechtigung wird über eine Access Group erteilt. |
| Access Group | Repräsentiert ein konkretes Zugriffsprofil auf eine Anwendung oder Ressource. | Ja, ausschließlich für das benannte Profil. |
| Entra Directory Role | Privilegierte Plattformrolle, keine Security Group. | Nicht über Persona-, Standort- oder Business-Role-Gruppen ableitbar. |
| Application Role | Von einer Anwendung definierte Fähigkeit, keine Security Group. | Kann über eine passende Access Group zugewiesen werden, wenn das spätere Application-Onboarding dies vorsieht. |

Damit ist „Rolle“ eindeutig: Eine Business Role Group beschreibt eine fachliche Funktion; eine Entra Directory Role beschreibt eine Plattformberechtigung; eine Application Role beschreibt eine anwendungsspezifische Fähigkeit.

## Modellprinzipien

1. **Eine Gruppe, eine Semantik:** Eine Gruppe beschreibt genau eine Persona, einen Standort, eine fachliche Rolle oder ein Zugriffsprofil.
2. **Zugriff nur über Access Groups:** Nur Gruppen der Kategorien `GRP-Access-App` und `GRP-Access-Resource` werden einer Anwendung oder Ressource zugewiesen.
3. **Keine impliziten Rechte:** Die Mitgliedschaft in einer Persona-, Standort- oder Business-Role-Gruppe verleiht selbst keine Anwendung-, Ressourcen- oder Entra-Administratorrechte.
4. **Standort und Rolle getrennt:** Eine Warehouse-Person in Lager Nord ist Mitglied einer Persona Group und einer Site Group. Eine Gruppe wie `GRP-Warehouse-North-Operators` wird nicht verwendet, weil sie Standort und Rolle vermischt.
5. **Privilegien separat:** Die Persona „Privileged Administrator“ kennzeichnet keinen Berechtigungsnachweis. Privilegierte Entra-Rollen sowie deren zeitliche Aktivierung und Verwaltung folgen einem gesonderten Modell.
6. **Nachvollziehbare Zuweisung:** Jede Access Group benötigt vor ihrer Nutzung ein dokumentiertes Zielsystem, Zugriffsprofil, fachliche Begründung und einen verantwortlichen Owner. Die konkrete Owner- und Review-Ausgestaltung bleibt Governance-Task.
7. **Keine vorausgesetzte Verschachtelung:** Das Modell setzt keine Gruppenverschachtelung voraus. Falls sie später eingesetzt wird, müssen die effektiven Zugriffswege je Zielsystem dokumentiert und technisch geprüft werden.

## Gruppenfamilien und Namensschema

Das verbindliche Schema und Beispiele stehen in [NAMING-CONVENTION.md](NAMING-CONVENTION.md). Die folgenden Familien sind vorgesehen:

| Gruppenfamilie | Muster | Bedeutung |
| --- | --- | --- |
| Persona | `GRP-Persona-<Persona>` | Identitäts- bzw. Beschäftigungstyp |
| Standort | `GRP-Site-<Site>` | Organisatorischer Standort |
| Fachliche Rolle | `GRP-BusinessRole-<Role>` | Fachliche Funktion ohne direkte technische Berechtigung |
| Anwendungszugriff | `GRP-Access-App-<Application>-<AccessProfile>` | Konkretes Zugriffsprofil einer Anwendung |
| Ressourcenzugriff | `GRP-Access-Resource-<Resource>-<AccessLevel>` | Konkretes Zugriffsprofil auf eine Ressource |

`<Application>`, `<AccessProfile>`, `<Resource>` und `<AccessLevel>` sind Platzhalter für später freigegebene technische Bezeichnungen. Sie sind keine Gruppen, bevor ein Zielsystem und sein Zugriffsprofil im Application Onboarding definiert wurden.

## Persona- und Standortgruppen

Die folgenden Gruppen bilden die bekannten Personas und Standorte ab. Sie sind Security Groups ohne unmittelbare Zugriffszuweisung.

| Kontext | Persona Group | Site Group | Begründung |
| --- | --- | --- | --- |
| Zentrale Hamburg | `GRP-Persona-Office-Users` | `GRP-Site-Hamburg` | Büro-Persona und Standort bleiben unabhängig auswertbar. |
| Lager Nord | `GRP-Persona-Warehouse-Users` | `GRP-Site-Warehouse-North` | Schicht- und Shared-Device-Kontext ist an die Warehouse-Persona gebunden, nicht an die Standortgruppe. |
| Lager Süd | `GRP-Persona-Warehouse-Users` | `GRP-Site-Warehouse-South` | Gleiches Persona-Modell wie Lager Nord; der Standort bleibt separat. |
| Filialen | `GRP-Persona-Store-Users` | `GRP-Site-Store-<StoreCode>` | Jede Filiale erhält bei Bedarf eine eigene Standortgruppe; ein konkreter Filialcode ist noch nicht definiert. |
| externe Dienstleister | `GRP-Persona-External-Contractors` | Keine Standard-Site-Group | Externe Zugehörigkeit begründet keine Standortzuordnung. Eine erforderliche Standortzuordnung muss separat begründet werden. |
| privilegierte Administratoren | `GRP-Persona-Privileged-Administrators` | Keine Standard-Site-Group | Privilegierung ist von Arbeitsstandort und regulärer Persona getrennt. |

Für alle Filialen kann ergänzend `GRP-Site-Stores` verwendet werden, wenn eine Anforderung ausdrücklich alle Filialstandorte betrifft. Diese Gruppe ist keine Ersatzgruppe für einzelne Filialzuordnungen und gewährt keinen Zugriff.

Für die Persona „Emergency Access Administrator“ wird keine allgemeine Persona Group vorgesehen. Notfallidentitäten und ihre Ausnahmebehandlung gehören ausschließlich in das noch offene Emergency-Access-Design (`CA-003`).

## Fachliche Rollen, Anwendungen und Ressourcen

Business Role Groups werden nur angelegt, wenn eine fachliche Funktion über mehrere Zielsysteme hinweg stabil definiert ist. Ein Beispiel ist `GRP-BusinessRole-Store-Manager` für die bereits beschriebene Marktleitung. Die Mitgliedschaft allein erzeugt keinen Zugriff.

Zugriff wird für jede Anwendung oder Ressource durch eine eigene Access Group repräsentiert. Beispiele für die Namensform, nicht für bereits freigegebene Berechtigungen:

```text
GRP-Access-App-<Application>-<AccessProfile>
GRP-Access-Resource-<Resource>-<AccessLevel>
```

Eine Access Group darf eine Standortbezeichnung nur enthalten, wenn das Zugriffsprofil tatsächlich auf eine standortgebundene Ressource beschränkt ist und die Begründung bei der Zuweisung dokumentiert wird. Ein Standortname wird nicht verwendet, um eine Personengruppe oder eine fachliche Rolle zu codieren.

Privilegierte Entra Directory Roles werden nicht aus der Zugehörigkeit zu `GRP-Persona-Privileged-Administrators` oder einer Business Role Group abgeleitet. Die Entscheidung zu Rollenmodell, PIM und möglichen role-assignable groups ist ausstehend.

## Mitgliedschaft und dynamische Gruppen

Mit `IAM-002` wird keine technische Mitgliedschaftsquelle festgelegt. Bis ADR-0005 angenommen, Attributqualität bewertet und der Lifecycle-Prozess entschieden sind, ist für jede Gruppe eine nachvollziehbar verwaltete statische Mitgliedschaft vorzusehen.

Dynamische Gruppen sind nur dann zu bewerten, wenn alle folgenden Voraussetzungen erfüllt sind:

- das verwendete Attribut stammt aus einer verbindlich festgelegten Identity Source,
- Attributsemantik und Datenqualität sind für den Berechtigungszweck geeignet,
- Zuständigkeit für Korrekturen und Lifecycle ist dokumentiert,
- die Regel und ihre Auswirkungen sind vor Produktivnutzung überprüft,
- die Gruppe gewährt keine privilegierte Entra-Rolle und keine Emergency-Access-Berechtigung.

Für Persona- oder Standortgruppen kann eine spätere dynamische Mitgliedschaft sinnvoll sein, falls diese Voraussetzungen erfüllt sind. Für Access Groups muss die Entscheidung je Zielsystem und Schutzbedarf erneut begründet werden; eine dynamische Berechtigungsvergabe wird mit diesem Entwurf nicht beschlossen.

## Sicherheits- und Betriebswirkung

- Die Trennung verhindert, dass ein Standortwechsel automatisch eine fachliche Rolle oder Anwendungsberechtigung impliziert.
- Access Groups machen die Berechtigung je Zielsystem und Profil prüfbar und unterstützen Least Privilege sowie Access Reviews.
- Separate privilegierte Identitäten bleiben von Workforce-Personas und Standortgruppen entkoppelt.
- Das Modell trifft keine Aussage zu Administrative Units und kann daher nicht als Delegationsmodell verwendet werden.
- Gruppenmitgliedschaften, Zielsystemzuweisungen und spätere Regeländerungen müssen auditierbar bleiben.

## Offene Architekturfragen und Abhängigkeiten

- `IAM-004`: ADR-0005 schlägt die Attributautorität vor; Synchronisationsmodell und technische Mitgliedschaftsverwaltung bleiben offen.
- `IAM-003`: Ob und wie Administrative Units für delegierte Verwaltung eingesetzt werden; dieses Gruppenmodell legt keine Administrative Units fest.
- `GOV-001` bis `GOV-005`: JML-Auslöser, Ownership, Access Reviews, Entitlement Management und PIM.
- Application-Onboarding-Phase: Verbindliche Kennungen für Anwendungen, Access Profiles und die Zuordnung von Application Roles.
- Entscheidung, ob und unter welchen technischen Voraussetzungen Gruppenverschachtelung oder role-assignable groups zulässig sind.
- `CA-003`: Emergency-Access-Identitäten und ihre Ausnahmebehandlung.

## ADR-Status

Die Trennung von Persona-, Standort-, Business-Role- und Access Groups sowie der Ausschluss impliziter Berechtigungen ist als Architekturentscheidung in [ADR-0003](../adr/0003-gruppen-und-berechtigungsmodell.md) mit Status `Proposed` dokumentiert. Eine Annahme erfordert Architektur- und Security-Review.
