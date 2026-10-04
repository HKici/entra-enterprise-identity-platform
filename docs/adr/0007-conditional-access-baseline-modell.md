# ADR-0007: Conditional-Access-Baseline-Modell

- Status: Proposed
- Datum: 2026-10-04

## Kontext

Die Nordstern Handelsgruppe benötigt eine nachvollziehbare Conditional-Access-Baseline für interne Workforce-Identitäten, separat geführte privilegierte Administratoridentitäten und externe Dienstleister. Die Personas enthalten unterschiedliche Betriebs- und Risikokontexte: Lager und Filialen verwenden teilweise Shared Devices, externe Identitäten haben ein noch offenes Kollaborationsmodell und privilegierte Identitäten benötigen einen restriktiveren Schutz.

Ohne eine dokumentierte Baseline würden MFA, Legacy Authentication, Sicherheitsinformationsregistrierung, Risikoauswertung, Pilotierung und Ausschlüsse uneinheitlich behandelt. Eine direkte technische Konfiguration oder ein voreiliges Enforcement kann insbesondere Workforce- und Administratorzugriffe beeinträchtigen.

Emergency Access, konkrete Authentication Strengths, Device Compliance, Hybrid Join, PIM und das externe Kollaborationsmodell sind noch nicht abschließend entschieden.

## Entscheidung

Als Arbeitsmodell wird eine Conditional-Access-Baseline mit folgenden fachlichen Policy-Kandidaten vorgeschlagen:

- MFA für interne Workforce-Identitäten,
- MFA für tatsächliche Inhaber von Entra Directory Roles,
- Blockieren von Legacy Authentication,
- Schutz der Sicherheitsinformationsregistrierung,
- MFA für erhöhtes Sign-in Risk,
- Risk Remediation für hohes User Risk,
- MFA für externe Dienstleister.

Alle Kandidaten starten gemäß ADR-0002 in `report-only`. Ein späteres Enforcement erfordert je Policy eine Sign-in-Log-Auswertung, einen kontrollierten Pilot, dokumentierte Ausschlüsse, fachliche und betriebliche Freigabe sowie die Prüfung von Rollback und Supportprozessen.

Die Baseline verwendet die vorhandenen Persona- und Standortgruppen nur als CA-Scopes oder Pilotkohorten. Sie verleiht keine Anwendungs-, Ressourcen- oder Entra-Administratorberechtigungen und verändert nicht die Trennung aus ADR-0003. Für privilegierte Administration richtet sich der Schutzscope an tatsächlichen Inhabern unterstützter integrierter Entra Directory Roles aus; `GRP-Persona-Privileged-Administrators` dient nicht als Rollen- oder Berechtigungszuweisung. Custom Roles und AU-scoped Rollen benötigen einen ergänzenden, separat validierten Scope.

Jede interaktive Workforce-Identität muss durch einen definierten CA-Scope oder eine dokumentierte Ausnahme abgedeckt sein. Für Guest-/External-Identitäten ist vor Enforcement nachzuweisen, dass sie entweder durch das Persona-Modell oder durch natives Guest-/External-Targeting erfasst werden. Nicht abgedeckte Workforce- oder externe Identitäten sind Security Findings.

Für Warehouse Shared Devices, Scanner und Filialgeräte wird in dieser Entscheidung keine Device- oder Sitzungs-Policy festgelegt. Bis zum Device- und Anmeldemodell wirken die personenbasierten MFA-, Legacy- und Risiko-Policies; Standort- oder Persona-Zugehörigkeit erzeugt keine pauschale Ausnahme.

Emergency-Access-Identitäten werden nur als künftiger Ausschlussbedarf referenziert. Anzahl, Verwahrung, konkrete Schutzmaßnahmen, technische Ausschlussobjekte und Tests bleiben `CA-003` vorbehalten.

Vor einem Enforcement der Risiko-Policies muss die Registrierung von Sicherheitsinformationen betrieblich produktionsreif sein. Dazu gehören ein getestetes Bootstrap-Verfahren für Benutzer ohne registrierte starke Authentifizierung sowie Tests der seit Juli 2026 betroffenen Windows-Hello-for-Business- und macOS-Platform-SSO-Registrierungen. Damit wird keine konkrete Authentication Strength festgelegt.

## Begründung

Die getrennten Policy-Kandidaten machen Kontrolle und Auswirkungen nachvollziehbar. Ein allgemeiner MFA-Schutz, ein gesonderter Admin-Schutz, das Blockieren veralteter Authentifizierungsflüsse und der Schutz von Sicherheitsinformationen adressieren unterschiedliche Angriffsflächen. Risikoauswertung erfordert eigene Betriebs- und Lizenzvoraussetzungen und wird deshalb nicht mit der allgemeinen MFA-Policy vermischt.

Report-only mit vorhandenen Gruppen als Pilotkohorten erlaubt insbesondere für Hamburg und Lager Süd die kontrollierte Auswertung, ohne Standort- und Berechtigungssemantik zu vermischen. Das verhindert, dass ungeklärte Shared-Device- oder externe Abhängigkeiten über breite Ausschlüsse oder vorzeitiges Enforcement kaschiert werden.

## Alternativen

### Eine einzige MFA-Policy für alle Identitäten sofort durchsetzen

Verworfen. Sie berücksichtigt weder die unterschiedliche Betriebsreife der Shared Devices und externen Identitäten noch die erforderliche Auswertung von Legacy- und Risikoabhängigkeiten. Sie würde zudem das in ADR-0002 festgelegte `report-only`-Vorgehen unterlaufen.

### Geräte- und Standortausnahmen für Lager und Filialen vorab definieren

Verworfen. Geräte-, Anmelde- und Anwendungsmodell sind noch offen. Pauschale Ausnahmen würden die Sicherheitswirkung gerade an den Standorten mit Shared Devices reduzieren.

### Privilegierte Administration über die Persona Group absichern

Verworfen. Eine Persona Group darf keine Entra Directory Role oder privilegierte Berechtigung implizieren. Der CA-Schutz muss daher an tatsächlichen Directory-Role-Inhabern ausgerichtet werden.

### Emergency-Access-Design in die Baseline aufnehmen

Verworfen. Das Break-Glass-Konzept ist als `CA-003` abgegrenzt und benötigt eine eigenständige Architektur- und Sicherheitsbewertung.

## Konsequenzen

### Positiv

- MFA-, Legacy-, Registrierungs-, Risiko- und externe Zugriffskontrollen werden getrennt prüfbar.
- Privilegierte Zugriffe werden unabhängig von Standort- oder Persona-Berechtigungssemantik berücksichtigt.
- Warehouse Shared Devices und externe Identitäten werden nicht durch unbegründete Ausnahmen geschwächt.
- Report-only und Pilotierung senken das Risiko von Lockouts und Betriebsunterbrechungen.

### Negativ

- Die Baseline schützt vor einem späteren Enforcement noch nicht aktiv.
- Auswertung, Support, Lizenzierung und Ausschlussreviews erzeugen zusätzlichen Betriebsaufwand.
- Device-, Authentifizierungs- und Emergency-Access-Entscheidungen bleiben als Abhängigkeiten bestehen.

## Security-Auswirkungen

Die Entscheidung verbessert die spätere Schutzfähigkeit gegen gestohlene Zugangsdaten, Legacy Authentication, ungesicherte Änderungen von Sicherheitsinformationen und risikobehaftete Anmeldungen. Gleichzeitig begrenzt sie das Lockout-Risiko durch Report-only, Pilotierung und eine restriktive Ausschlussstrategie.

Bis zum Enforcement bleibt ein Restrisiko bestehen. Fehlende MFA-Registrierung, Legacy-Anwendungsabhängigkeiten, falsche Policy-Scopes, unklare externe Anmeldewege oder unzureichend getestete Emergency-Access-Ausschlüsse können die Sicherheits- oder Betriebswirkung beeinträchtigen.

## Betriebsauswirkungen

Vor Annahme und Enforcement sind Sign-in Logs, Directory-Role-Inhaber, Authentifizierungsregistrierungen, Legacy-Clients, externe Zugriffswege, Lizenzen, Support- und Incident-Prozesse sowie Rollback-Verfahren zu prüfen. Für risikobasierte Policies sind die Voraussetzungen von Microsoft Entra ID Protection und Microsoft Entra ID P2 zu validieren.

Die Entscheidung bleibt bis zum Architektur- und Security-Review `Proposed`.
