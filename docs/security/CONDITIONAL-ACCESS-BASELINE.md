# Conditional-Access-Baseline-Modell

Status: **Entwurf (CA-001 / CA-002)**

## Ziel und Abgrenzung

Dieses Dokument beschreibt die fachliche Conditional-Access-Baseline der Nordstern Handelsgruppe. Sie schützt Workforce-, privilegierte und externe Identitäten mit klar abgegrenzten Policy-Kandidaten. Sie ist keine produktive Konfiguration, enthält keine JSON- oder Graph-Automatisierung und setzt keine Policy in Enforcement.

Die Baseline ergänzt das Source-of-Authority-, Gruppen- und Hybrid-Identity-Modell, ersetzt diese aber nicht. Sie erzeugt keine Berechtigungen, verändert keine Gruppenmitgliedschaften und legt weder eine Authentication Strength noch ein Geräte- oder Hybrid-Join-Modell fest. Die Architekturentscheidung ist als [ADR-0007](../adr/0007-conditional-access-baseline-modell.md) mit Status `Proposed` dokumentiert.

Microsoft empfiehlt für eine allgemeine MFA-Baseline eine Policy für alle Benutzer und Ressourcen sowie eine MFA-Anforderung. Die Nordstern-Baseline staffelt diesen Umfang zunächst nach den vorhandenen Personas, damit Auswirkungen auf Shared Devices und externe Identitäten kontrolliert ausgewertet werden können. [Microsoft Learn: MFA für alle Benutzer](https://learn.microsoft.com/en-us/entra/identity/conditional-access/policy-all-users-mfa-strength)

## Grundsätze

1. **Report-only zuerst:** Alle Baseline-Policies starten und verbleiben in diesem Entwurf in `report-only`, gemäß [ADR-0002](../adr/0002-neue-ca-policies-starten-in-report-only.md).
2. **Eine Policy, ein Schutzbedarf:** MFA, Legacy Authentication, Sicherheitsinformationsregistrierung, Risiko und externe Zugriffe werden getrennt dokumentiert, damit Auswirkungen nachvollziehbar bleiben.
3. **Persona- und Rollenmodell respektieren:** CA-Zielgruppen steuern ausschließlich Zugriffsbedingungen. Sie verleihen weder App- noch Ressourcenberechtigungen und ersetzen keine Entra Directory Roles.
4. **Keine pauschalen Standortausnahmen:** Lager, Filialen und externe Dienstleister erhalten keine Ausnahmen allein aufgrund des Standorts oder der Beschäftigungsart.
5. **Emergency Access ist separiert:** Emergency-Access-Identitäten werden nur als künftiger Ausschlussbedarf referenziert. Anzahl, Verwahrung, technische Ausgestaltung, Kompensationsmaßnahmen und Tests bleiben `CA-003` vorbehalten.
6. **Workload Identities bleiben außerhalb dieses Scopes:** Diese Benutzer-Policies decken keine Service Principals oder sonstigen Workload Identities ab. Eine etwaige Conditional-Access-Strategie für Workload Identities ist ein separater Architektur- und Security-Task.
7. **Vollständige Workforce-Coverage:** Jede interaktive Workforce-Identität muss durch einen definierten CA-Scope oder eine dokumentierte Ausnahme erfasst sein. Nicht klassifizierte Workforce-Konten sind ein Security Finding und vor jedem Enforcement zu bereinigen oder ausdrücklich zu behandeln.

## Policy-Naming

Für Baseline-Policies gilt die in [NAMING-CONVENTION.md](../concepts/NAMING-CONVENTION.md) definierte Form:

```text
CA-BL-<Sequence>-<Control>-<Scope>
```

Der technische Name enthält weder den Status `report-only` noch die Pilotwelle. Status, Ausschlüsse und Rollout werden je Policy separat dokumentiert. Damit bleibt die Kennung über ihren gesamten Lifecycle stabil.

## Baseline-Policies

Alle nachstehenden Policies sind fachliche Kandidaten. Die angegebenen Scope-, Condition- und Grant-Definitionen sind vor einer Konfiguration gegen die aktuelle Produktunterstützung, Lizenzierung, Sign-in Logs und die Ausnahmen nach `CA-003` zu prüfen.

| Policy | Ziel | Scope | Conditions | Grant Control | Rollout-Status |
| --- | --- | --- | --- | --- | --- |
| `CA-BL-001-Require-MFA-Workforce` | Mindestschutz für interne reguläre Workforce-Identitäten. | Einschließen: `GRP-Persona-Office-Users`, `GRP-Persona-Warehouse-Users`, `GRP-Persona-Store-Users`. Externe und privilegierte Identitäten sind nicht enthalten, da sie eigene Policies erhalten. | Alle Ressourcen; alle Client-Apps; keine Standort-, Geräte- oder Risikoauswahl. | Require multifactor authentication. Die konkrete Authentication Strength bleibt offen. | `report-only`; Auswertung vor Pilotierung und vor jeder späteren Durchsetzung. |
| `CA-BL-002-Require-MFA-Privileged-Admins` | Separierte, cloud-only administrative Identitäten mit einer MFA-Anforderung schützen. | Einschließen: Inhaber unterstützter integrierter Entra Directory Roles. `GRP-Persona-Privileged-Administrators` dient nur für Pilotierung und Abgleich, nicht als Nachweis oder Zuweisung einer Directory Role. Custom Roles und AU-scoped Rollen benötigen einen ergänzenden, separat validierten Scope. | Alle Ressourcen; alle Client-Apps; keine Device-Condition in dieser Baseline. | Require multifactor authentication. Eine stärkere Authentication Strength oder ein privilegierter Gerätestandard werden erst nach Authentifizierungs- und Device-Design bewertet. | `report-only`; vor einem Pilot alle aktiven Directory-Role-Inhaber sowie die Abdeckung von Custom Roles und AU-scoped Rollen mit der Persona- und Workforce-Kopplung abgleichen. |
| `CA-BL-003-Block-Legacy-Authentication` | Nicht moderne Authentifizierungsflüsse blockieren, die moderne Zugriffsbedingungen umgehen können. | Einschließen: alle Benutzeridentitäten, einschließlich Workforce, Externe und privilegierte Identitäten. | Alle Ressourcen; Legacy-Authentication-Clients. Die konkret beobachteten Protokolle und Abhängigkeiten werden aus Sign-in Logs validiert. | Block access. | `report-only`; erst nach belegter Auswertung und abgestimmter Behandlung betroffener Anwendungen stufenweise durchsetzen. |
| `CA-BL-004-Protect-Security-Info-Registration` | Registrierung oder Änderung von Sicherheitsinformationen vor unbefugten Änderungen schützen. | Einschließen: die drei internen Workforce-Persona-Gruppen sowie die privilegierten Administrationsidentitäten. Externe Identitäten bleiben bis zur Entscheidung über ihr Kollaborations- und Anmeldemodell außerhalb des Scopes. | User action `Register security information`; keine Geräte- oder Standortcondition. Seit 6. Juli 2026 sind auch Windows-Hello-for-Business- und macOS-Platform-SSO-Registrierungen betroffen. | Require multifactor authentication. | `report-only`; vor Enforcement Bootstrap-Verfahren, Registrierungspfad, Support-Prozess sowie Windows-Hello-for-Business- und macOS-Platform-SSO-Registrierung testen. Die Registrierungsfähigkeit muss betrieblich produktionsreif sein, bevor die Risiko-Policies in Enforcement gehen. |
| `CA-BL-005-Require-MFA-Risky-SignIns` | Erhöhtes Sign-in-Risiko durch zusätzliche Verifikation abfangen. | Einschließen: interne Workforce-Persona-Gruppen und Inhaber von Entra Directory Roles. Externe Identitäten bleiben bis zur Entscheidung über Cross-Tenant- und Risikoverarbeitung außerhalb des Scopes. | Alle Ressourcen; Sign-in risk `medium` oder `high`; keine Geräte- oder Standortcondition. | Require multifactor authentication. | `report-only`; setzt vor einem späteren Enforcement geeignete MFA-Registrierung, P2-Lizenzierung, Auswertung und Support-Runbook voraus. |
| `CA-BL-006-Require-Risk-Remediation-High-User-Risk` | Identitäten mit hohem Benutzerrisiko kontrolliert zur Selbstbehebung führen. | Einschließen: interne Workforce-Persona-Gruppen und Inhaber von Entra Directory Roles. Externe Identitäten bleiben zunächst außerhalb des Scopes. | Alle Ressourcen; User risk `high`; keine Geräte- oder Standortcondition. | Require risk remediation. Die zugehörige Authentication Strength und Sign-in Frequency werden durch den Grant Control vorgegeben; ihre Eignung ist vor Durchsetzung zu validieren. | `report-only`; P2-Lizenzierung, Risikobehandlung, Helpdesk- und Incident-Prozess sind Voraussetzungen für Enforcement. |
| `CA-BL-007-Require-MFA-External-Contractors` | Zeitlich und auftragsbezogenem externem Zugriff eine starke, nachvollziehbare Authentifizierung voranstellen. | Einschließen: `GRP-Persona-External-Contractors`. Keine Site Group wird verwendet. | Alle Ressourcen; alle Client-Apps; keine Compliance-, Plattform- oder Standortcondition. | Require multifactor authentication. Die konkrete Behandlung externer Sicherheitsinformationen bleibt offen. | `report-only`; nur nach Validierung des noch offenen externen Kollaborations- und Anmeldemodells pilotieren. |

Die Risikopolicies setzen Microsoft Entra ID Protection und eine entsprechende Microsoft Entra ID P2-Lizenzierung voraus. Microsoft beschreibt MFA für erhöhtes Sign-in-Risiko sowie Risk Remediation für hohes User Risk als getrennte Controls; bei Risk Remediation werden Authentication Strength und die Sign-in Frequency `Every time` durch den Grant Control ergänzt. [Microsoft Learn: Sign-in Risk](https://learn.microsoft.com/en-us/entra/identity/conditional-access/policy-risk-based-sign-in), [Microsoft Learn: User-Risk-Remediation](https://learn.microsoft.com/en-us/entra/identity/conditional-access/policy-risk-based-user)

## Admin Protection

`CA-BL-002` richtet sich an tatsächliche Inhaber unterstützter integrierter Entra Directory Roles, nicht an eine Standort-, Business-Role- oder Access Group. Damit wird vermieden, dass die Zugehörigkeit zu `GRP-Persona-Privileged-Administrators` selbst eine Plattformrolle impliziert. Die Gruppe darf lediglich zur kontrollierten Pilotierung und zur Überprüfung dienen, ob jede privilegierte cloud-only Identität an eine aktive oder genehmigte Workforce-Funktion gekoppelt ist.

Conditional-Access-Targeting über Directory Roles deckt weder Custom Roles noch AU-scoped Rollen ab. Für diese Rollen muss vor Enforcement ein ergänzender Scope separat validiert werden, ohne die Rollen- oder AU-Entscheidungen vorwegzunehmen. [Microsoft Learn: CA-Zielgruppen](https://learn.microsoft.com/en-us/entra/identity/conditional-access/concept-conditional-access-users-groups)

Für privilegierte Administratoridentitäten ist nach dem in [AUTHENTICATION-METHODS-AND-STRENGTHS.md](AUTHENTICATION-METHODS-AND-STRENGTHS.md) beschriebenen Zielmodell eine phishing-resistente Authentication Strength vorgesehen. CA-BL-002 bleibt bis zur Methoden-, Device- und PIM-Validierung `report-only`; die Auswertung muss insbesondere administrative Sign-ins, verwendete Methoden, Gerätepfade und mögliche Emergency-Access-Treffer sichtbar machen.

## Legacy Authentication

`CA-BL-003` behandelt Legacy Authentication unabhängig von der allgemeinen MFA-Policy. Dadurch lässt sich erkennen, welche Anwendungen oder Geräte noch ältere Protokolle oder Clients verwenden, bevor ein Block wirksam wird. Die Auswertung muss unter anderem Logistik- und Filialfachanwendungen, Scanner, Shared Devices und nicht interaktive technische Konten unterscheiden.

Nicht interaktive Service- oder Synchronisationskonten werden nicht stillschweigend als Benutzer-Ausnahme aufgenommen. Benutzer-Policies erfassen Service Principals nicht; für technische Konten und Workload Identities ist stattdessen eine gesonderte Inventarisierung und Sicherheitsentscheidung erforderlich. Microsoft empfiehlt, erforderliche Benutzer-Ausnahmen regelmäßig zu prüfen und Workload Identities über eigene Conditional-Access-Policies zu behandeln. [Microsoft Learn: Conditional-Access-Templates und Ausschlüsse](https://learn.microsoft.com/en-us/entra/identity/conditional-access/concept-conditional-access-policy-common)

## Security Information Registration

`CA-BL-004` schützt die User Action `Register security information`. Die Registrierungsfähigkeit muss betrieblich produktionsreif sein, bevor die Risiko-Policies in Enforcement gehen: Eine Sign-in-Risk-MFA-Anforderung kann nur dann sinnvoll abgearbeitet werden, wenn die betroffene Person bereits eine geeignete MFA-Registrierung besitzt. Dazu gehört ein getestetes Bootstrap-Verfahren für Personen ohne registrierte starke Authentifizierung, beispielsweise ein Temporary Access Pass; damit wird keine konkrete Authentication Strength festgelegt. [Microsoft Learn: risikobasierter Schutz](https://learn.microsoft.com/en-us/entra/identity/authentication/tutorial-risk-based-sspr-mfa), [Microsoft Learn: Security-Info-Registrierung](https://learn.microsoft.com/en-us/entra/identity/conditional-access/policy-all-users-security-info-registration)

Seit 6. Juli 2026 umfasst das Targeting der User Action `Register security information` auch die Registrierung von Windows Hello for Business und macOS Platform SSO. Diese Flows müssen deshalb im `report-only`-Pilot und vor einem Enforcement mit dem vorgesehenen Grant Control getestet werden.

Ob externe Dienstleister ihre Sicherheitsinformationen im Nordstern-Tenant, in einem externen Identity Provider oder über ein anderes Modell führen, ist noch nicht entschieden. Deshalb enthält diese Baseline dafür keine Annahme.

## Risk-based Protection

Die Baseline trennt Sign-in Risk (`CA-BL-005`) und User Risk (`CA-BL-006`), weil sie unterschiedliche Auslöser und Reaktionen haben:

- Ein erhöhtes **Sign-in Risk** bewertet einen konkreten Anmeldevorgang und fordert MFA an.
- Ein hohes **User Risk** bewertet den möglichen Kompromittierungszustand eines Kontos und nutzt Risk Remediation.

Vor Enforcement sind Lizenzierung, Risikoschwellen, Fehlalarme, MFA-Registrierungsquote, Support- und Incident-Runbooks sowie eine nachvollziehbare Ausnahmebehandlung zu prüfen. Die Werte `medium`/`high` für Sign-in Risk und `high` für User Risk sind Baseline-Kandidaten, keine abschließende Risikoakzeptanzentscheidung.

## External Access

`CA-BL-007` ist auf die vorhandene Persona Group für externe Dienstleister beschränkt. Diese Auswahl erzeugt keine Ressourcenzuweisung und ersetzt weder das Vertrags-/Sponsor-System noch die zeitlich begrenzte Access-Group-Zuweisung.

Ein konkretes B2B-, Cross-Tenant-Trust- oder Synchronisationsmodell wird nicht angenommen. Vor einem Pilot muss daher geklärt werden, ob und wie MFA, Risiko- und Security-Information-Registrierung für externe Identitäten technisch bewertet werden. Bis dahin gilt die Policy nur als `report-only`-Kandidat.

Vor Enforcement ist zusätzlich nachzuweisen, dass jede Guest-/External-Identität entweder durch das Persona-Modell oder durch ein natives Guest-/External-Targeting abgedeckt ist. Nicht abgedeckte externe Identitäten sind als Security Finding zu behandeln; diese Coverage-Anforderung entscheidet weder ein B2B- noch ein Cross-Tenant-Modell.

## Warehouse- und Shared-Device-Kontext

Für Lager Nord und Lager Süd gilt die Workforce-MFA-, Legacy- und Risikobaseline genauso wie für andere interne Beschäftigte. Die Zugehörigkeit zu `GRP-Persona-Warehouse-Users` oder einer Standortgruppe ist jedoch keine Rechtfertigung für eine Ausnahmeregel.

Es wird **keine** separate Device-Compliance-, Hybrid-Join-, Plattform-, Client-App- oder Sitzungs-Policy für Warehouse Shared Devices und Scanner festgelegt. Das konkrete Anmelde- und Sitzungsmodell, die Geräteverwaltung, die unterstützten Anwendungen sowie die erforderliche betriebliche Verfügbarkeit sind noch offen. Eine solche Policy würde ohne diese Grundlagen zu einem Sicherheits- oder Betriebsrisiko führen.

Lager Süd kann gemäß Persona-Modell als Pilotstandort dienen. `GRP-Site-Warehouse-South` wird dafür nur als zeitlich begrenzter CA-Pilotscope verwendet; sie bleibt eine Standortgruppe und gewährt keine Berechtigung.

## Report-only-Rollout und Pilotgruppen

Der Rollout folgt den Phasen in der Zielarchitektur und ADR-0002:

1. **Readiness:** Authentifizierungsmethoden, Sign-in Logs, Legacy-Clients, Inhaber unterstützter integrierter Directory Roles, Custom Roles, AU-scoped Rollen, externe Zugriffswege, Sicherheitsinformationsregistrierung und mögliche Emergency-Access-Treffer inventarisieren. Die CA-Coverage jeder interaktiven Workforce- sowie jeder Guest-/External-Identität nachweisen.
2. **Report-only – vorgesehener Endscope:** Jede Policy zunächst mit ihrem beabsichtigten Scope in `report-only` auswerten. `report-only` schützt noch nicht aktiv.
3. **Fachliche Pilotierung:** Nach der Logauswertung kontrolliert mit den vorhandenen Gruppen in Enforcement validieren, sofern alle in der jeweiligen Policy genannten Eintrittskriterien erfüllt sind.
4. **Stufenweiser Rollout:** Weitere Gruppen erst nach dokumentiertem Review, Support-Freigabe und ohne ungeklärte Ausschlüsse aufnehmen.
5. **Betrieb:** Auswirkungen, Ausnahmen, Rollback und Sign-in- bzw. Audit-Ergebnisse fortlaufend prüfen.

| Pilotbedarf | Vorhandene Gruppe | Verwendung im CA-Rollout | Abgrenzung |
| --- | --- | --- | --- |
| Zentrale Workforce | `GRP-Site-Hamburg` | Pilotkohorte für Workforce-Auswirkungen, nachdem `CA-BL-001`, `CA-BL-003` und die Registrierungswege in `report-only` bewertet wurden. | Standortgruppe bleibt organisatorisch; sie gewährt keine App- oder Administratorrechte. |
| Warehouse / Shared Devices | `GRP-Site-Warehouse-South` | Pilotkohorte für die Auswertung von MFA-, Legacy- und Risikoauswirkungen im Lagerbetrieb. | Keine automatische Device-Policy und keine Ausnahmeregel für Lager Nord oder Süd. |
| Privilegierte Administration | `GRP-Persona-Privileged-Administrators` | Kontrollierter Abgleich und Pilot der MFA-Auswirkungen für separat verwaltete administrative Identitäten. | Die Gruppe weist keine Directory Role zu und ersetzt keine PIM-Entscheidung. |
| Externe Dienstleister | `GRP-Persona-External-Contractors` | Pilot erst nach Validierung des externen Kollaborations- und Anmeldemodells. | Keine Standort- oder implizite Zugriffssemantik. |

Die genannten Gruppen dienen ausschließlich als temporäre CA-Zielkohorten. Ihre Membership muss kontrolliert, auditierbar und von Access-Group-Mitgliedschaften getrennt sein. Neue Pilotgruppen werden mit CA-001 nicht eingeführt.

## Ausschlussstrategie

Ausschlüsse senken den Schutz und sind deshalb keine Lösung für nicht getestete Anwendungen, Standorte, Shared Devices oder externe Zugriffe. Für jede Ausnahme gilt:

- Ausschlüsse werden nur mit fachlicher Begründung, verantwortlichem Owner, Ablaufdatum, kompensierenden Kontrollen und Review-Nachweis zugelassen.
- Es gibt keine dauerhaften pauschalen Ausnahmen für Lager, Filialen, externe Dienstleister, privilegierte Administratoren oder Standorte.
- Eine Ausnahme aus einer Policy bedeutet keine Berechtigung für Anwendung, Ressource oder Entra-Rolle.
- Der technische und organisatorische Ausschluss der Emergency-Access-Identitäten wird erst in `CA-003` festgelegt. Bis dahin werden keine Konten, Gruppen, Anzahl, Verwahrung oder Schutzmaßnahmen in dieser Baseline definiert.
- Vor jeder späteren Enforcement-Phase ist zu prüfen, ob die Emergency-Access-Identitäten aus den einschränkenden Policies ausgeschlossen sind. In `report-only` ist keine technische Ausnahme erforderlich, weil die Policy keinen Zugriff blockiert.

Microsoft weist darauf hin, dass Emergency-Access-Konten bei einschränkenden Policies nicht verfügbar sein können und dass `report-only` noch keine technische Ausnahme benötigt. Die konkrete Ausgestaltung folgt dennoch bewusst erst dem separaten Emergency-Access-Design. [Microsoft Learn: Emergency Access](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/security-emergency-access)

Das konkrete Modell für cloud-only Konten, direkte CA-Ausschlüsse, kompensierende Kontrollen, Monitoring und Tests ist in [EMERGENCY-ACCESS.md](EMERGENCY-ACCESS.md) beschrieben. Die dort vorgeschlagene Architekturentscheidung ist bis zum Review in ADR-0008 als `Proposed` markiert.

## Offene Fragen und Abhängigkeiten

- Verbindliche Authentication Strengths, zugelassene MFA-Methoden und deren Eignung für reguläre, privilegierte und externe Identitäten.
- Bootstrap-Verfahren für Benutzer ohne registrierte starke Authentifizierung sowie Tests der seit Juli 2026 betroffenen Windows-Hello-for-Business- und macOS-Platform-SSO-Registrierungen.
- Device-Management-, Compliance-, Hybrid-Join-, Plattform- und Sitzungsmodell, besonders für Warehouse Shared Devices, Scanner und Filialgeräte.
- Inventar der Legacy-Authentication-Abhängigkeiten, nicht interaktiven Konten, Anwendungskompatibilität und Ablösepfade.
- Microsoft Entra ID P2-Lizenzierung sowie Risikoakzeptanz, Support- und Incident-Runbooks für risikobasierte Controls.
- Konkretes B2B-, Cross-Tenant- oder anderes Kollaborationsmodell für externe Dienstleister.
- Vollständiges Emergency-Access-Design einschließlich Ausschlussobjekten, Anzahl, Verwahrung, Schutzmaßnahmen, Monitoring und Tests in `CA-003`.
- Rollenmodell, PIM, privilegierter Gerätestandard sowie der ergänzende CA-Scope für Custom Roles und AU-scoped Rollen.
- Ownership, Review-Zyklen, Telemetrie und Erfolgskriterien für die spätere Enforcement-Freigabe.
